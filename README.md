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

How to link a service to a deployment: https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/

## Helm chart

```
helm upgrade --install tetris .
```

```
kubectl get all
kubectl get ingress
```

## Parametrize helm chart

```
git checkout replicas
```
