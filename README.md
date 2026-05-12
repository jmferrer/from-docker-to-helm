# from-docker-to-helm

## Requirements

microk8s with ingress addon enabled.

## Docker

```
docker run -it -p 8081:80 --rm uzyexe/tetris
```

## Kubernetes

```
kubectl apply -f charts/tetris/templates/deployment.yaml
kubectl apply -f charts/tetris/templates/service.yaml
kubectl apply -f charts/tetris/templates/ingress.yaml
```

```
kubectl get all
kubectl get ingress
```

```
kubectl delete -f charts/tetris/templates/deployment.yaml
kubectl delete -f charts/tetris/templates/service.yaml
kubectl delete -f charts/tetris/templates/ingress.yaml
```

## Helm chart

```
helm upgrade --install tetris ./charts/tetris -n tetris --create-namespace
```

```
kubectl get all
kubectl get ingress
```

## Modify chart

Take a look to *charts/tetris/templates/deployment.yaml* where *{{ .Values.replicas }}* was added.

This value is defined in *charts/tetris/values.yaml*.

```
helm upgrade --install tetris ./charts/tetris -n tetris --create-namespace
kubectl get po
```

## Redefine replicas:

```
helm upgrade --install tetris ./charts/tetris -n tetris --set replicas=5
```

## Flux v2 (GitOps)

Ensure there is not tetris in default namespace:
```
helm uninstall tetris -n default --ignore-not-found=true
```

Install Flux controllers with Helm (didactic Helm-first workflow):
```
helm repo add fluxcd-community https://fluxcd-community.github.io/helm-charts
helm repo update
helm upgrade --install flux2 fluxcd-community/flux2 \
	--namespace flux-system \
	--create-namespace
```

Optional check:
```
kubectl get pods -n flux-system
```

Create the Flux source and the Flux Kustomization:
```
kubectl apply -f flux-system/from-docker-to-helm-source.yaml
kubectl apply -f flux-system/tetris-kustomization.yaml
```

With this setup, Flux manages both:
- the Helm chart under `charts/tetris`
- the application manifests under `tetris/`

This means a `git push` to `gitops` can update:
- chart templates and values consumed by the chart
- the `HelmRelease` definition itself, for example `replicas` in `tetris/tetris-release.yaml`

The bootstrap manifests live outside `tetris/` on purpose, so the application
Kustomization cannot prune its own `GitRepository` source.

Check pods:
```
watch kubectl get po -n tetris
```

Check reconciliation status:
```
kubectl get gitrepositories.source.toolkit.fluxcd.io -n flux-system
kubectl get kustomizations.kustomize.toolkit.fluxcd.io -n flux-system
kubectl get helmreleases.helm.toolkit.fluxcd.io -n tetris
```
