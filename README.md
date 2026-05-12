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

Create the Flux source and apply the manifests:
```
kubectl apply -f tetris/tetris-source.yaml
kubectl apply -f tetris/tetris-namespace.yaml
kubectl apply -f tetris/tetris-release.yaml
```

Check pods:
```
watch kubectl get po -n tetris
```
