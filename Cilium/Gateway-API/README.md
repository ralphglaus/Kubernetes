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
    --set disable-kube-proxy=true \
    --set l7Proxy=true \
    --set gatewayAPI.enabled=true \
    --set l2announcements.enabled=true

### Logs anschauen

```shell

kubectl -n kube-system logs -l app.kubernetes.io/name=cilium-operator
kubectl -n kube-system logs -l app.kubernetes.io/name=cilium-envoy
kubectl -n kube-system logs -l app.kubernetes.io/name=cilium-agent
```



### Cilium GatewayClass definieren



### Gateway definieren
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: cilium
spec:
  controllerName: io.cilium/gateway-controller
  description: The default Cilium GatewayClass


Prüfen:

```shell
kubectl get gatewayclass
```
Testen der Gatewayclass (Achtung ACCEPTED muss auf True sein)


In Cilium muss die gatewayAPI aktiviert werden:
```yaml
gatewayAPI:
  enabled: true
```

### IP Pool erstellen

```yaml
apiVersion: cilium.io/v2
kind: CiliumLoadBalancerIPPool
metadata:
  name: vip-pool
spec:
  blocks:
    - start: 172.29.35.200
      stop: 172.29.35.210
```

### L2Announcement

./helm upgrade cilium cilium/cilium --set=l2announcements.enabled=enabled

```yaml
l2announcements:
  enabled: true
```

Prüfen:
```shell
kubectl -n kube-system get configmap cilium-config -o yaml | grep -i l2
```

```yaml
apiVersion: cilium.io/v2alpha1
kind: CiliumL2AnnouncementPolicy
metadata:
  name: l2announcement-policy
spec:
  serviceSelector:
    matchExpressions:
      - key: io.kubernetes.service.namespace
        operator: NotIn
        values:
          - kube-system

  interfaces:
    - ^eth[0-9]+
    - ^ens[0-9]+
    - ^enp[0-9]+

  externalIPs: true
  loadBalancerIPs: true
```
Prüfen:

```shell
kubectl get ciliuml2announcementpolicies
```

### Nginx Deployment und Service einrichten

Ngnix Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: default
spec:
  replicas: 2

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx:alpine

          ports:
            - containerPort: 80
```

Service zu nginx einrichten:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: default

spec:
  selector:
    app: nginx

  ports:
    - name: http
      port: 80
      targetPort: 80

  type: ClusterIP
```

Prüfen:

```shell
kubectl get pods -o wide
kubectl get svc nginx
```

### Gateway einrichten

Gateways können in verschiedenen Namespaces installiert werden. Die Route wird nur ind den gleichen Namspace erlaubt.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: nginx-gateway
  namespace: default
spec:
  addresses:
  gatewayClassName: cilium
  adresses:
  - type: IPAddress
    value: 172.29.35.140
  listeners:
    - name: web-gw
      port: 80
      protocol: HTTP
      
      allowedRoutes:
        namespaces:
          from: Same 
```

Prüfen:

```shell
kubectl get gateway
```

Erzeugter Loadbalancer prüfen:

```shell
kubectl get svc
NAME                         TYPE           CLUSTER-IP     EXTERNAL-IP
nginx                        ClusterIP      10.43.120.20   <none>
cilium-gateway-nginx-gateway LoadBalancer   10.43.x.x      172.29.35.200
```

### Route erstellen

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: nginx-route
  namespace: default

spec:

  parentRefs:
    - name: nginx-gateway
      sectionName: http

  rules:

    - matches:
        - path:
            type: PathPrefix
            value: /

      backendRefs:
        - name: nginx
          port: 80
```

Prüfen:
```shell
kubectl get httproute
```







  

