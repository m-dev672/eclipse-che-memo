## Eclipse Che setup commands

### Install Required Tools
```bash
# Docker requied

curl -LO https://github.com/kubernetes/minikube/releases/download/v1.29.0/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64

curl -LO https://dl.k8s.io/release/v1.23.9/bin/linux/amd64/kubectl
sudo install kubectl /usr/local/bin/kubectl && rm kubectl

bash <(curl -sL  https://che-incubator.github.io/chectl/install.sh)
```

### Start Minikube
```bash
minikube start --driver=docker --addons=ingress,dashboard --vm=true --memory=6144 --cpus=4 --disk-size=100GB --kubernetes-version=v1.23.9
```

### Change the DNS server for nip.io to 8.8.8.8
```bash
sudo mkdir -p /etc/systemd/resolved.conf.d/
sudo nano /etc/systemd/resolved.conf.d/eth0.conf
```

```ini
[Resolve]
DNS=8.8.8.8
Domains=~nip.io
```

```bash
sudo systemctl restart systemd-resolved
```

### Deploy Eclipse Che
```bash
chectl server:deploy --platform minikube --domain 192.168.2.6.nip.io
```

### Install HAProxy for external access
```bash
sudo apt update && sudo apt install haproxy -y
sudo nano /etc/haproxy/haproxy.cfg
```

```
listen https_proxy
    bind *:443
    mode tcp
    server server1 192.168.49.2:443
```

```bash
sudo systemctl restart haproxy
```

### After reboot host machine

```
minikube ssh

sudo mkdir -p /tmp/hostpath-provisioner/admin-che/claim-devworkspace
sudo chmod 777 /tmp/hostpath-provisioner/admin-che/claim-devworkspace

exit
```
