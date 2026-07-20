### Prerequest

### CDR installieren für Gateway API (Version 1.4.1)
```shell
kubectl apply --server-side -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.4.1/standard-install.yaml
```

### CDR Version abfragen

```shell
kubectl get crd gateways.gateway.networking.k8s.io -o yaml | grep -i version
```

### Cilium Werte anpassen

    --set kubeProxyReplacement=true \
    --set gatewayAPI.enabled=true
    --set l2announcements.enabled=true

### Logs anschauen

```shell

kubectl -n kube-system logs -l app.kubernetes.io/name=cilium-operator
kubectl -n kube-system logs -l app.kubernetes.io/name=cilium-envoy
kubectl -n kube-system logs -l app.kubernetes.io/name=cilium-agent
```



### GatewayClass definieren


### Gateway definieren


### L2Announcement

./helm upgrade cilium cilium/cilium --set=l2announcements.enabled=enabled





  

