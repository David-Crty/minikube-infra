# minikube-infra


```
argocd app list
```
---
## Render
This command will render the "final" manifests that will be applied to the cluster.
We can compare this to helm template output.

```
crossplane render \
    helm/crossplane-resources/templates/my-app.yaml \
    helm/crossplane/templates/composition.yaml \
    helm/crossplane/templates/fn.yaml
```