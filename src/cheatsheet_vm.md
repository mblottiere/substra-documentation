# Quick deploy on Ubuntu 20.04 VM

> /!\ This configuration is totally not suitable for production environment!

```sh
# Check the machine
uname -a

# System updates
sudo apt update && \
	sudo apt upgrade -y && \
	sudo apt autoremove --purge -y && \
	sudo apt autoclean
    
# Docker: https://docs.docker.com/engine/install/ubuntu/
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common \
    socat # needed by k8s/skaffold in virtualized context
    
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
   
sudo apt-get update && \
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    
sudo groupadd docker
sudo usermod -aG docker $USER

# Minikube v1.9.2: https://minikube.sigs.k8s.io/docs/start/
curl -LO https://github.com/kubernetes/minikube/releases/download/v1.9.2/minikube-linux-amd64 && \
    sudo install minikube-linux-amd64 /usr/local/bin/minikube && \
    rm -rf minikube-linux-amd64

# Kubectl v1.16.7: https://github.com/kubernetes/kubectl/releases/tag/v0.16.7
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - && \
    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update && \
    sudo apt-get install -y kubectl=1.16.7-00 -V

# Helm v2.16.1: https://github.com/helm/helm/releases/tag/v2.16.1
curl -LO https://get.helm.sh/helm-v2.16.1-linux-amd64.tar.gz && \
    tar xzvf helm-v2.16.1-linux-amd64.tar.gz && \
    sudo mv linux-amd64/tiller linux-amd64/helm /usr/local/bin/ && \
    rm -rf helm-v2.16.1-linux-amd64.tar.gz linux-amd64
 
# Skaffold v1.8.0: https://skaffold.dev/
curl -Lo skaffold https://storage.googleapis.com/skaffold/releases/v1.8.0/skaffold-linux-amd64 && \
    chmod +x skaffold && \
    sudo mv skaffold /usr/local/bin
    
# k9s v0.22.0
mkdir k9s && cd k9s && \
    curl -L0 https://github.com/derailed/k9s/releases/download/v0.22.0/k9s_Linux_x86_64.tar.gz && \
    tar xzvf k9s_Linux_x86_64.tar.gz && \
    sudo mv k9s /usr/local/bin && \
    rm -rf k9s

# Minikube config
sudo minikube start --vm-driver=none --kubernetes-version='v1.16.7'
# In case of HOST_JUJU_LOCK_PERMISSION: sudo sysctl fs.protected_regular=0
sudo minikube status
sudo minikube addons list
sudo minikube addons enable ingress && \
    sudo minikube addons enable ingress-dns && \
    sudo minikube addons enable helm-tiller

# Helm init
sudo helm init && \
    sudo kubectl get pods --namespace kube-system | grep tiller

# Add bitnami repo
sudo helm repo add bitnami https://charts.bitnami.com/bitnami

# On the server, to test locally
echo "$(sudo minikube ip) substra-backend.node-1.com substra-frontend.node-1.com substra-backend.node-2.com substra-frontend.node-2.com" | sudo tee -a /etc/hosts

# On your local machine, set the server ip in /etc/hosts
echo "<SERVER-IP> substra-backend.node-1.com substra-frontend.node-1.com substra-backend.node-2.com substra-frontend.node-2.com" | sudo tee -a /etc/hosts
 
# Clone the repos & checkout the correct version: https://github.com/SubstraFoundation/substra#compatibility-table
mkdir substra && cd substra
git clone https://github.com/SubstraFoundation/substra.git --branch 0.6.0
git clone https://github.com/SubstraFoundation/hlf-k8s.git --branch 0.0.12 # or checkout fe33fc0 ¯\_(ツ)_/¯
git clone https://github.com/SubstraFoundation/substra-frontend.git --branch 0.0.17
git clone https://github.com/SubstraFoundation/substra-backend.git --branch 0.0.19

# Fire up: skaffold run
cd hlf-k8s && sudo skaffold run
cd ../substra-backend && sudo skaffold run
cd ../substra-frontend && sudo skaffold run

# Quick test on backend node-1
curl substra-backend.node-1.com/readiness
```

