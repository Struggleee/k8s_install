## 安裝Docker 、k8s
 ```bash
 swapoff -a && sysctl -w vm.swappiness=0
 find /etc/fstab |sudo  xargs -i sed -i 's@\/swapfile@\#\/swapfile@g' {}
echo  "net.ipv4.ip_forward = 1\nnet.bridge.bridge-nf-call-ip6tables = 1\nnet.bridge.bridge-nf-call-iptables = 1"  > /etc/sysctl.d/k8s.conf
sysctl -p /etc/sysctl.d/k8s.conf
### Install Docker Engine - Community
apt-get update && apt-get install -y \
apt-transport-https \
ca-certificates \
curl \
gnupg-agent \
software-properties-common \
unzip
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
apt-key fingerprint 0EBFCD88
add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"
#### Install Docker Engine - Community
apt-get update
apt-get install -y docker-ce docker-ce-cli containerd.io
apt-cache madison docker-ce

#sudo usermod -aG docker $(user)
#newgrp docker


### Install kubelet kubeadm kubectl
apt-get update && apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
echo  "deb https://apt.kubernetes.io/ kubernetes-xenial main">/etc/apt/sources.list.d/kubernetes.list
apt-get update && apt-get install -y kubelet="1.14.10-00" kubeadm="1.14.10-00" kubectl="1.14.10-00"
apt-mark hold kubelet kubeadm kubectl
```
## k8s cluster creator
 ```bash
 sudo kubeadm init --service-cidr 10.96.0.0/12 --pod-network-cidr 10.244.0.0/16 --apiserver-advertise-address 0.0.0.0
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
 ``` 
## k8s setting
```bash
#CNI 
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl taint node --all node-role.kubernetes.io/master:NoSchedule-
#ngress-controller 
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-0.32.0/deploy/static/provider/baremetal/deploy.yaml
```
## nodepord range
```bash 
#sudo vim /etc/kubernetes/manifests/kube-apiserver.yaml
#加入--service-node-port-range=1-65535
sudo sed -i '14 i\    - --service-node-port-range=1-65535' /etc/kubernetes/manifests/kube-apiserver.yaml
```
