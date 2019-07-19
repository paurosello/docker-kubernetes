Ubuntu 18.04 x64 

##############
Upgrade
##############
apt-get update && apt-get upgrade

##############
Containerd
##############
sudo apt-get install libseccomp2
wget https://storage.googleapis.com/cri-containerd-release/cri-containerd-1.2.7.linux-amd64.tar.gz
sudo tar --no-overwrite-dir -C / -xzf cri-containerd-1.2.7.linux-amd64.tar.gz
sudo systemctl start containerd

##############
Kubeadm
##############
apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
modprobe br_netfilter
echo 1 > /proc/sys/net/ipv4/ip_forward

##############
Kubeadm Master
##############

kubeadm init \
--pod-network-cidr 10.10.0.0/18 \
--cri-socket unix:///run/containerd/containerd.sock

##############
Kubectl
##############
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

##############
Network
##############
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

#############
Kubeadm Worker
#############
kubeadm join 165.22.199.82:6443 --token vwrbt5.znh64omg5eo75aic \
    --discovery-token-ca-cert-hash sha256:4c32456c68f518d28a7eef8dae65044cf87669cbaa09f85530898058ffefd2b7

#############
Pods Multiple nodes
#############
kubectl get pods -o wide -A