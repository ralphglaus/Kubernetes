# Deinstallieren der Rancher Agenten auf den Worker Nodes

### Prüfen

```shell
systemctl status rancher-system-agent
systemctl status rke2-agent
```

### Uninstall Scirpt laufen lassen

```shell
/usr/local/bin/rke2-uninstall.sh
```
### Verzeichnisse löschen

```shell
sudo rm -rf \
/etc/rancher \
/etc/kubernetes \
/etc/cni \
/opt/cni \
/var/lib/rancher \
/var/lib/kubelet \
/var/lib/cni \
/var/lib/calico \
/run/calico \
/run/flannel

```

### CDR installieren für Gateway API



### GatewayClass definieren

```yaml





# Installieren von Rancher



docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /opt/rancher:/var/lib/rancher \
  --privileged \
  rancher/rancher:latest
  

