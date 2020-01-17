

# Prerequisites

Suitable rights to create resources
access token

# Create EKS Cluster
```
eksctl create cluster -f eksctl-cluster.yaml
```

# Enable GitOps

```
EKSCTL_EXPERIMENTAL=true eksctl \
    enable repo -f eksctl-cluster.yaml \
    --git-url=git@github.com:robparrott/k8s-gitops.git \
    --git-email=[ email ]
```