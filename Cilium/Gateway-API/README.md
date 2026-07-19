
### CDR installieren für Gateway API

```shell
kubectl apply --server-side -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.4.1/standard-install.yaml
```

### CDR Version abfragen

```shell
kubectl get crd gateways.gateway.networking.k8s.io -o yaml | grep -i version
```




### GatewayClass definieren


### Gateway definieren


### L2Announcement

./helm upgrade cilium cilium/cilium --set=l2announcements.enabled=enabled





  

