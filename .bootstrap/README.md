# FluxCD Operator

Taken from: [flux-operator](https://github.com/controlplaneio-fluxcd/flux-operator)

## Install the Operator

```bash
helm install flux-operator oci://ghcr.io/controlplaneio-fluxcd/charts/flux-operator \
  --namespace flux-system --create-namespace
```

## Create secret for GitHub

```bash
flux create secret git flux-gitlab \
  --namespace=flux-system \
  --url=ssh://git@github.com/micxba/lab-fluxcd.git
```

> Add the deploy key to GH as an authorized key

## Create an instance

```bash
apiVersion: fluxcd.controlplane.io/v1
kind: FluxInstance
metadata:
  name: flux
  namespace: flux-system
spec:
  cluster:
    domain: cluster.local
    multitenant: false
    multitenantWorkloadIdentity: false
    networkPolicy: true
    size: small
    type: kubernetes
  components:
  - source-controller
  - source-watcher
  - kustomize-controller
  - helm-controller
  - notification-controller
  distribution:
    artifact: oci://ghcr.io/controlplaneio-fluxcd/flux-operator-manifests
    registry: ghcr.io/fluxcd
    version: 2.7.x
  migrateResources: true
  sync:
    interval: 1m
    kind: GitRepository
    path: clusters/homelab
    pullSecret: flux-gitlab
    ref: refs/heads/main
    url: ssh://git@github.com/micxba/lab-fluxcd
  wait: true
```
