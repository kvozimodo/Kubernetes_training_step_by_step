# Kubernetes_training_step_by_step
How to up k8s 
# Step 1 start machines
vagrant up
# Step 2 do fill hostsfile
vim /etc/hosts
# Step 3 disable selinux
sudo setenforce 0 

sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
# Step 4 disable swapping
sudo swapoff -a
# Step 5 enable iptables see traffic
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf

br_netfilter

EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf

net.bridge.bridge-nf-call-ip6tables = 1

net.bridge.bridge-nf-call-iptables = 1

EOF

sudo sysctl --system
# Step 6 add kubernetes repo
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo

[kubernetes]

name=Kubernetes

baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch

enabled=1

gpgcheck=1

repo_gpgcheck=1

gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

exclude=kubelet kubeadm kubectl

EOF
# Step 7 install kubernetes
sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
# Step 8 enable kubelet.service
sudo systemctl enable --now kubelet
#Step 9 start Docker
sudo systemctl start docker.service
# Step 10 start kubelet
sudo systemctl start kubelet
# Step 11 start kluster on master
sudo kubeadm init --apiserver-advertise-address={{your.master.ip}}
# Step 12 prepare to use cluster
mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config

or do next by root

export KUBECONFIG=/etc/kubernetes/admin.conf
# Step 13 join kluster on minion
kubeadm join --token <token> <control-plane-host>:<control-plane-port> --discovery-token-ca-cert-hash sha256:<hash>

if you forgot token use

kubeadm token list

if you have no token use

kubeadm token create

If you don't have the value of --discovery-token-ca-cert-hash use

openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
