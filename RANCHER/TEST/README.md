 # Installieren einer RANCHER Testumgebung

### Empfehlung für den Anfang

```
Host:
Docker + Rancher Container
VMs:
node1: etcd + control plane + worker
node2: worker
node3: worker
```

### Server Sizing

```
4 CPUs und 8 GB RAM
```

![alt text](image.png)

 ### Installation von Ubuntu 24.04 LTS
Updates installieren
```
apt update && apt-upgrade -y
```

Docker installieren
```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

#TESTEN
sudo systemctl status docker
```
SWAP deaktivieren
```
# SWAP deaktivieren

swapoff -a
nano /etc/fstab # Swap zeile ausklammern mit #
```

Hosts in /etc/hosts eintragen
```
cat << EOF >> /etc/hosts
192.168.20.130 srv-kubemast-01
192.168.20.131 srv-kubewrk-01
192.168.20.132 srv-kubewrk-02
192.168.20.133 srv-kubewrk-03
EOF
```

NTP Dienst installieren
```
apt install ntp
```

Docker Image installieren
```
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /opt/rancher:/var/lib/rancher \
  --privileged \
  rancher/rancher:latest
  ```
  

