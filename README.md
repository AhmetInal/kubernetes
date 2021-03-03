# kubernetes
controller ve node yapılacak 

sudo apt-get update

sudo apt-get upgrade

sudo apt-get install docker.io -y

sudo systemctl start docker

sudo systemctl enable docker

sudo usermod -aG docker $USER

newgrp docker

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add

sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

sudo apt-get install kubeadm kubelet kubectl -y

------------------------birinci kısım--------------------------

sudo snap install multipass --classic --stable
multipass launch --name node1 --cpus 2 --mem 1024M --disk 5G
---bilinmesi gereken komutlar
	multipass start node1
	multipass list
	multipass shell node1
------------------------birinci kısım--------------------------

sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address=CONTROLLER IP(multipass list denildiğinde ortak verilen ip)
----Sonucuta çıkan tokeni not al, bu token node oluşturulduğu zaman o cluster katılmak için kullanılacak

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml

kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml  ----calismazsa alttaki

kubectl apply -f https://docs.projectcalico.org/v3.9/manifests/calico.yaml

kubectl get pods --all-namespaces
	dns ve calico gelmis ise sorun yok
-----------------------------Dashboard eklenmesi-------------------------------

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml

kubectl create clusterrolebinding dashboard-admin -n default \
> --clusterrole=cluster-admin \
> --serviceaccount=default:dashboard
kubectl get secret $(kubectl get serviceaccount dashboard -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode
----çıktıyı not al
kubectl proxy 
firefoxta 
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/ girildiğinde karşımıza çıkan seçimde token seçilip bir önceki adımda elde edilen tokeni oraya ekleyip girşi yapıyoruz
