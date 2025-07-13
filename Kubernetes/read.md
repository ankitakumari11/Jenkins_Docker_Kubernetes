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
- go to url: https://kubernetes.io/docs/concepts/cluster-administration/addons/ -->click "Weave Net "  ----->  https://github.com/rajch/weave#using-weave-on-kubernetes
```
kubectl apply -f https://reweave.azurewebsites.net/k8s/v1.29/net.yaml
```
- if above not working then again restart the kubelet and containerd:
```
root@ip-172-31-94-140:~# systemctl restart kubelet
root@ip-172-31-94-140:~# systemctl restart containerd
root@ip-172-31-94-140:~# kubectl apply -f https://reweave.azurewebsites.net/k8s/v1.29/net.yaml
```
- Now it will start
              <img width="1888" height="370" alt="image" src="https://github.com/user-attachments/assets/bce38e64-0b34-464e-9365-afdc909d2adf" />
- `kubectl get pods --all-namespaces` , now u will all pods are ready
              <img width="1898" height="550" alt="image" src="https://github.com/user-attachments/assets/e4809114-102a-4a9e-af6f-dff80c8a3bf1" />


### STEPS FOR WORKER NODES

- `kubectl get pods` (if doesn't work then restart , if then also then ignore)
- `rm /etc/containerd/config.toml`
- `systemctl restart containerd`
- `kubeadm reset`
- run below command to join this worker node with the control plane (master node) , u need to copy this command from kubeadm init output of master plane:
```
kubeadm join 172.31.80.183:6443 --token rsfa3d.b94tl7rq876xh7t7 \
        --discovery-token-ca-cert-hash sha256:a024f13c9d0bf1ed63185f56462c83442a294f62dcd3ab3774f51249f8c68b47
```
- Now the worker node is joined to master node.

### MASTER NODE

- Again go to master node and now check the nodes
```
root@ip-172-31-80-14:~# kubectl get nodes
NAME              STATUS   ROLES           AGE     VERSION
ip-172-31-80-14   Ready    control-plane   40m     v1.33.2
ip-172-31-93-12   Ready    <none>          2m50s   v1.33.2
```
- now if the above code shows not ready then again run:(the above shows the slave node too now).  
- Now since master and slave/worker nodes are ready now. We can go further to create pods for container. We will create pods using **Kind:pod** in yml.

| Term                | Definition                                                                 | Context                        |
|---------------------|----------------------------------------------------------------------------|--------------------------------|
| `kind` (CLI tool)   | A CLI tool named **"Kubernetes IN Docker"** used to create local clusters inside Docker containers. Ideal for testing and CI environments. | Local Kubernetes cluster setup |
| `kind: Pod`         | A field in a Kubernetes YAML manifest that tells Kubernetes to create a **Pod**, the smallest deployable unit that runs containers. | Kubernetes YAML definition     |
| `kind: Deployment`  | A field in a Kubernetes YAML manifest that tells Kubernetes to create a **Deployment**, which manages Pods by handling scaling, rolling updates, and self-healing. | Kubernetes YAML definition     |

- Create a yml file- vi pod.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: ngnix-prod
  labels:
    env: prod
    version: 1.2.3
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
```
- *Here we are creating a pod named nginx-prod , and inside that pod we are creating a container named nginx-container made from image nginx*  
- Now apply the yml file:  
`kubectl apply -f pod.yml`
                 <img width="1894" height="541" alt="image" src="https://github.com/user-attachments/assets/46a537a8-519b-4bbf-951b-c60005d73601" />

> [!NOTE]
> - Creating Pods directly using kind: Pod in on-premises Kubernetes (or any Kubernetes cluster) is not recommended for production, because if the Pod crashes or the node goes down, Kubernetes does not automatically recreate it.
> - Instead, it's better to use a Deployment object (defined using kind: Deployment in a YAML file). A Deployment ensures that the desired number of Pod replicas are always running.
> - This applies to both on-prem Kubernetes and managed clusters like EKS (Amazon Elastic Kubernetes Service).
> - In EKS, for example, you can define a deployment.yaml file with multiple replicas. If any Pod fails, Kubernetes will automatically reschedule a new one, and you can also enable auto-scaling to scale Pods up or down based on load.


#### 🚀 Why Use EKS Instead of On-Prem Kubernetes?

| **Factor**              | **EKS (Amazon EKS)**                                           | **On-Prem Kubernetes**                                |
|-------------------------|----------------------------------------------------------------|--------------------------------------------------------|
| **Setup & Maintenance** | ✅ AWS manages control plane, networking, upgrades              | ❌ You manage everything (complex & manual)            |
| **High Availability (HA)** | ✅ Built-in multi-AZ HA, fault-tolerant                     | ❌ You must manually configure HA                      |
| **Security & Compliance** | ✅ Integrated with IAM, VPC, private endpoints              | ❌ You build security from scratch                     |
| **Scaling**             | ✅ Auto-scaling of nodes and pods                              | ❌ Manual setup and tuning required                    |
| **Monitoring & Logging**| ✅ CloudWatch, X-Ray, etc. integrated easily                   | ❌ You must integrate & maintain your own              |
| **Cost**                | 💰 Pay-as-you-go (no hardware cost)                            | 💻 High upfront cost (hardware, power, etc.)           |
| **Upgrades**            | ✅ Simple version upgrades via console or CLI                  | ❌ You must upgrade Kubernetes manually                |
| **Disaster Recovery**   | ✅ Backup options via EBS snapshots, multi-region              | ❌ Needs in-house DR planning                          |
| **DevOps Integration**  | ✅ Easy CI/CD integration with AWS/GitHub                      | ❌ You configure CI/CD pipelines manually              |
| **Time to Deploy**      | ⚡ Minutes                                                     | 🕒 Days or weeks                                       |


## EKS- CLUSTER







