

# Prerequisites

Suitable rights to create resources
access token

# Create EKS Cluster
```
eksctl create cluster -f eksctl-cluster.yaml
```

# Enable GitOps

Setup gitops repo

```
touch README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:robparrott/k8s-gitops.git
git push -u origin master
```

Enable ssh auth

```
ssh-keygen -f ~/.ssh/gitops-k8s
cat ~/.ssh/gitops-k8s.pub 

```

Add that key as a deploy key in GitHub or wherever.

Then enable gitops 
```
EKSCTL_EXPERIMENTAL=true eksctl \
    enable repo -f eksctl-cluster.yaml \
    --git-url=git@github.com:robparrott/k8s-gitops.git \
    --git-email=[ email ] \
    --git-private-ssh-key-path ~/.ssh/gitops-k8s
```

and confirm things are running.

You may need to get the newly created key and also add it as a deploy key in GitHub ... little flaky. Install `fluxcyl` and run `fluxctl identity --k8s-fwd-ns flux`. See https://docs.fluxcd.io/en/latest/tutorials/get-started.html#giving-write-access. YMMV.


