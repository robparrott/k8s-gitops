# Overview 

This repository and it's companion https://github.com/parrottr/gitops-example provide an example of establishing a GitOps pattern in Kubernetes (http://k8s.io/) using ArgoCD (https://argocd.io/).

In particular, this repository provides a point of control for excluding other sets of stacks or packages into a "App of Apps" pattern common in ArgoCD:

* https://argoproj.github.io/argo-cd/operator-manual/cluster-bootstrapping/

In particular, we organize sets of capabilities heirarchically as follows

* `Stacks`: Sets of packages deployed together into a suite of capabilities, usually a common layer/overall application
* `Packages`: common set of components, but deployed indovidually into kubernetes (sets of helm charts, manifests etc.)
* `Modules`: nidividually deployed bits and pieces (helm charts, manifest sets etc.)

Before jumping in here refer to the companion repository https://github.com/robparrott/gitops-example for any setup.

# Layout

For this repository, we deploy specify stacks for deployment

tacks
For reference on configuraiton ArgoCD declaratively, see the docs at:

* https://argoproj.github.io/argo-cd/operator-manual/declarative-setup/

This entire repository can be referenced as the source of an application from Argo CD by pointing to the base of the repository in ArgoCD. This sources the root `kustomize.yaml` which then includes a set of `Application` definitions from various component or application directories, which are written to the `argocd` namespace. This set of "Application" CRD's is what constitutes that "wrapper" application. *Note*: this is a stylistic choice for convenience and not a required layout topology.

Each of the individual Application's are defined in `application.yaml` files (my convention here). An example for a very simple Applciation that enables basic admin access to kubernetes look like this:

```
---
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: admin-access
  namespace: argocd 
spec:
  project: default
  source:
    repoURL: https://github.com/parrottr/k8s-gitops.git
    targetRevision: HEAD
    path: systems/admin-access
  destination:
    server: https://kubernetes.default.svc
    namespace: kube-system
```

See this ( https://argoproj.github.io/argo-cd/operator-manual/application.yaml ) full the full set of options for this CRD definition.

By using a `kustomize.yaml` file we can add or remove these "sub" applciations at will. This allows us to conveniently define a related set of Applciations within asingle repository. We could also have made each Application a separate repository, and managed the set of deployed Applications in the other repository.

We can also ... in the companion repository ... add other remote setups in other repositories. This defines a convenient model for allowing mutiple teams to collaborate; in this repository we can manage sub-systems of the installation such as operators, monitoring and logging, and then allow app teams to define using GitOps their own layouts. This also allows different feature vbranches to be defined and deployed to different environments as desired by siply tweawking the configuraiton of the Applciation in ArgoCD, since the revision is part of the Application definition.

The particular topology is defined within the repositories themselves, and should be driven by organizational needs. The best practice constraint is that such a topology definition should be encoded in the source code, and the only difference between envidonments may be the source code branch or revision (which shold indeally be managed in some upstream CD service as well, and not by hand).

# Use

## Getting the cluster admin bearer token

Service Account "eks-admin" has a bearer token for access, so you need to dig that out to access Kubernetes Dashboard:

```
SECRET=$(kubectl -n kube-system get secret | grep eks-admin-token | awk '{print $1}')
TOKEN=$( kubectl -n kube-system describe secret ${SECRET} | grep "^token:" | awk '{print $2}' )
echo $TOKEN
```

Use for accessing the Kubernetes Dashboard once installed.

## Tailing Logs 

You can get the `kail` tool to selectively tail logs from pods locally:

* https://github.com/boz/kail

Exmaple: tail all logs from a given namespace:

```
kail -n argocd
```

## Forwarding Services:

Vault:

```
kubectl port-forward service/vault-server -n vault 8200 
```

Kubernetes Dashboard:

```
kubectl port-forward service/kube-dashboard-kubernetes-dashboard -n kube-system 8443:443 
```

Kibana:

```
kubectl port-forward service/kibana-kibana -n logging 5601
```

MySQL:

```
kubectl port-forward service/mysql-vault-integration-demo 3306 
```




