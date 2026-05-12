# from-docker-to-helm

## Requirements

microk8s with ingress addon enabled.

## Docker

```
docker run -it -p 8081:80 --rm uzyexe/tetris
```

## Kubernetes

```
kubectl apply -f templates/deployment.yaml
kubectl apply -f templates/service.yaml
kubectl apply -f templates/ingress.yaml
```

```
kubectl get all
kubectl get ingress
```

```
kubectl delete -f templates/deployment.yaml
kubectl delete -f templates/service.yaml
kubectl delete -f templates/ingress.yaml
```

## Helm chart

```
helm upgrade --install tetris .
```

```
kubectl get all
kubectl get ingress
```

## Modify chart

Take a look to *templates/deployment.yaml* where *{{ .Values.replicas }}* was added.

This value is defined in *values.yaml*.

```
helm upgrade --install tetris .
kubectl get po
```

## Redefine replicas:

```
helm upgrade --install tetris . --set replicas=5
```

## Flux

Agregamos repositorio de flux:
```
helm repo add fluxcd https://charts.fluxcd.io
helm repo update
helm repo list
```

Instalamos flux para que monitorice la rama gitops de este repositorio:
```
kubectl create namespace flux
helm upgrade --install flux fluxcd/flux \
    --set git.url=https://github.com/jmferrer/from-docker-to-helm.git \
    --set git.branch=gitops \
    --set git.pollInterval=1m \
    --namespace flux
helm upgrade --install helm-operator fluxcd/helm-operator \
    --set helm.versions=v3 \
    --namespace flux
```