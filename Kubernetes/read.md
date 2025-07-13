# Kubernetes
  
It is a container orchestration tool which is used to manage containers.There are 2 methods to build containers:  
1. On-premise K8s: Create a vm and setup K8 on it from beginning.
2. Managed K8s: AWS will provide ready-made Kubenetes as a service called EKS(elastic kubernetes service).
   
      <img width="1024" height="1024" alt="ChatGPT Image Jul 9, 2025, 02_28_56 AM" src="https://github.com/user-attachments/assets/2c0f6dc5-4ff4-40dc-a8d9-4d528432c1a8" />

## On-premise Kubernetes

🛠️ What is kubeadm?  
kubeadm stands for Kubernetes Admin.kubeadm is a tool provided by Kubernetes that helps you easily set up and manage a Kubernetes cluster on your own servers (VMs or bare metal).   
  
✅ Use cases of kubeadm  

| Action                          | Command         | Purpose                                        |
|---------------------------------|-----------------|------------------------------------------------|
| Initialize a new cluster        | `kubeadm init`  | Sets up a control plane node (master node)     |
| Join a node to the cluster      | `kubeadm join`  | Adds worker nodes to the cluster               |
| Reset a cluster setup           | `kubeadm reset` | Cleans up all changes made by `kubeadm`        |
| Generate certificates and config| `kubeadm config`| Prepares configs and security artifacts        |

🚀 What is Minikube?  
Minikube is a tool that lets you run a single-node Kubernetes cluster locally on your laptop or desktop.It’s perfect for:  
- Learning Kubernetes concepts
- Testing applications locally before deploying to real clusters
- Practicing Kubernetes commands (kubectl, deployments, services, etc.)

### COMMON STEPS FOR BOTH MASTER AND WORKER NODES

- Create a ec2 instance (t2-medium , ubuntu) - 1 for master and 1 for server.
- `apt-get update`
- Install docker: https://docs.docker.com/engine/install/ubuntu/
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
- `apt-get install openjdk-17-jdk`
  
- Install kubeadm from section *Installing kubeadm, kubelet and kubectl* : https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
```
sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet
```

{this above command will freeze the kube versions}
- `kubeadm init` (now some error comes , so run below commands)
```
$ rm -f /etc/containerd/config.toml
$ systemctl restart containerd
$ kubeadm reset
$ kubeadm init
```
  
<img width="1791" height="684" alt="image" src="https://github.com/user-attachments/assets/efe87933-1f91-48a6-8705-74800d11a0ad" />
  
- Now copy the output of  `kubeadm init` of master server and keep it with you.
```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.80.183:6443 --token rsfa3d.b94tl7rq876xh7t7 \
        --discovery-token-ca-cert-hash sha256:a024f13c9d0bf1ed63185f56462c83442a294f62dcd3ab3774f51249f8c68b47
```
- Hopefully you have followed the above steps for both master and worker nodes.

### STEPS FOR MASTER NODE
  
- run below commands now on master node to initialise master plane:
```
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
- `systemctl restart kubelet` 
- `systemctl restart containerd`
- `kubectl get nodes` - if error comes then again run the above 2 restart commands.
            <img width="1889" height="237" alt="image" src="https://github.com/user-attachments/assets/881c6a63-127d-4dff-8128-db9ba7f33c25" />

- Now see the above node is master plan itself (u can see the ip)  
- `kubectl get pods`
             <img width="1434" height="81" alt="image" src="https://github.com/user-attachments/assets/7ffbb783-4bcf-42b3-9cc8-6eb701307025" />

- `kubectl get pods --all-namespaces`
             <img width="1785" height="262" alt="image" src="https://github.com/user-attachments/assets/425a8b15-b963-4c0a-8f09-58c82543a7f4" />
  
- now we need to start these 2 services first : coredns
- 



