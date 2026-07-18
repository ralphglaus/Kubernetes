CDRs installieren

```shell
kubectl apply --server-side -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.6.0/standard-install.yaml
```

Namespace für Gateway konfigurieren
```shell
kubectl create ns gateway
```

### Gatwayclass installieren

```shell
kubectl apply -f gatewayclass.yaml -namespace gateway
```
