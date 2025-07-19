# Kubernetes
  
It is a container orchestration tool which is used to manage containers.There are 2 methods to build containers:  
1. On-premise K8s (open source) : Create a vm and setup K8 on it from beginning.
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
Minikube is a tool that lets you run a single-node Kubernetes cluster locally on your laptop or desktop where the master and worker processes are on the same machine..It’s perfect for:  
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

1. Create EC-2 instance (t2-medium)(ubuntu).
2. Go to IAM ->create user ->administrator policy
3. Go to that user -> access key->generate->CLI->Create key
4. Take connection to your vm
5. Download AWS CLI using url: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
apt-get update
apt-get install unzip
unzip awscliv2.zip
sudo ./aws/install
```
7. `aws configure`
```
AWS Access Key ID [None]: AKIAQ54TZPMGISXOEV7P
AWS Secret Access Key [None]: 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
Default region name [None]: us-east-1
Default output format [None]: json
```
> [!NOTE]
> **Typical Workflow**:  
> - Use eksctl to create the EKS cluster.
> - Use aws eks update-kubeconfig to configure kubectl.
> - Use kubectl to deploy and manage your applications.
  
7. Install Kubect using url: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/  
Purpose: Interact with Kubernetes clusters (any, including EKS).Use case:-  
- Deploy applications (e.g., kubectl apply -f app.yaml)
- Get status of resources (e.g., kubectl get pods, kubectl get svc)
- Manage Kubernetes objects like:Pods, Services, Deployments, Nodes, ConfigMaps, Secrets, etc.
- Run commands inside pods (e.g., kubectl exec, kubectl logs)
  
```
sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg # allow unprivileged APT programs to read this keyring
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list   # helps tools such as command-not-found to work correctly
sudo apt-get update
sudo apt-get install -y kubectl
```
8. Install **eksctl** on the server using url:https://eksctl.io/installation/
- Purpose: Create, manage, and delete EKS clusters on AWS.  
```
# for ARM systems, set ARCH to: `arm64`, `armv6` or `armv7`
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"

# (Optional) Verify checksum
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz

sudo install -m 0755 /tmp/eksctl /usr/local/bin && rm /tmp/eksctl
```
10. `eksctl version`
11. Create eks cluster:
```
eksctl create cluster --name eks-kotak --region us-east-1 --nodegroup-name kotak-workers --node-type t3.medium --nodes 3 --nodes-min 1 --nodes-max 3 --managed
```

   <img width="1920" height="139" alt="image" src="https://github.com/user-attachments/assets/31d12b82-9486-4b5f-888f-e52af8cdf163" />
  
12. Get cluster infor:
```
eksctl get cluster
```
  
   <img width="1897" height="103" alt="image" src="https://github.com/user-attachments/assets/9ed1fab0-2f5e-4082-9d32-118048f827a1" />  

  
13. run below update command. It is used to configure kubectl to connect to your EKS (Elastic Kubernetes Service) cluster.It updates your local kubeconfig file (typically at ~/.kube/config) so that the kubectl CLI can communicate with the EKS cluster named kotak-eks in the us-east-1 region.After running this, you can use:`kubectl get nodes` and all.
   
```
aws eks update-kubeconfig --name kotak-eks --region us-east-1
```
  
<img width="1920" height="79" alt="image" src="https://github.com/user-attachments/assets/aef2499d-5d4f-4cc1-875c-cf32601408a1" />
  
  
14. Check the nodes:`kubectl get nodes`
  
<img width="1920" height="134" alt="image" src="https://github.com/user-attachments/assets/03305b48-5bb1-4736-ba87-c01943e16573" />
               
15. Create Deployment.yml to create 4 replicas(pods) having a container each with ngnix image.
vi Deployment  
```
apiVersion: apps/v1              # Specifies the API version to use (apps/v1 is the stable version for Deployments)
kind: Deployment                 # Declares that this YAML is defining a Deployment object
metadata:                        # Metadata about the Deployment
  name: nginx-prod-deploy        # Name of the Deployment
  labels:                        # Labels to organize and identify the Deployment
    app: nginx-app-prod          # A custom label with key "app" and value "nginx-app-prod"
spec:                            # Desired state specification for the Deployment
  replicas: 6                    # Number of desired Pod replicas (6 Pods will be created)
  selector:                      # Tells the Deployment how to find which Pods it manages
    matchLabels:                 
      app: nginx-pod             # Matches Pods that have the label "app: nginx-pod"
  template:                      # Template for creating the Pods
    metadata:                    # Metadata for each Pod created by the Deployment
      labels:
        app: nginx-pod           # Each Pod will have this label; must match the selector above
    spec:                        # Specification of the Pod (what’s inside the Pod)
      containers:                # List of containers to run in the Pod
        - name: nginx-container  # Name of the container
          image: nginx           # Docker image to use for the container (nginx is pulled from Docker Hub)
          ports:
            - containerPort: 80  # Exposes port 80 inside the container (used by Nginx). Port 80 is open inside the container to serve web traffic.
```
16. Run the above deployment: `kubectl apply -f Deployment.yml`
  
  <img width="1891" height="80" alt="image" src="https://github.com/user-attachments/assets/c669a38c-7912-4e98-9055-7cf14f48698d" />

18. Get information about pods, deployments and service:
```
kubectl get pods
kubectl get deployments
kubectl get service
```

<img width="1913" height="397" alt="image" src="https://github.com/user-attachments/assets/2c81579d-8b82-4f2e-8ebb-dc300d1edb86" />


## SERVICE : NODEPORT

17. Create a NodePort service to :This is used to access your app outside the cluster , helps to expose pods.
- `vi service.yml`
  
```
apiVersion: v1
kind: Service
metadata:
    name: nginx-prod-service
    labels:
      app: nginx-app-prod-service
spec:
  selector:
    app: nginx-pod
  type: NodePort
  ports:
  - nodePort: 31000
    port: 80
    targetPort: 80
```

18. - `kubectl apply -f service.yml`
    - `kubectl get service`

<img width="1920" height="169" alt="image" src="https://github.com/user-attachments/assets/9b150dae-0753-4424-bc1d-9aa54e090a4c" />

19. `kubectl describe service nginx-prod-service`

<img width="1920" height="494" alt="image" src="https://github.com/user-attachments/assets/38966d12-45b4-4bec-b200-d5558706149c" />

> [!NOTE]
> - nginx-prod-service is a NodePort service.It has a Cluster IP: 10.100.226.252 (internal to the cluster).
> - Exposes port 80 internally, and maps it to Node port 31000.
> - So, if your node's public IP is x.x.x.x, you can access your app at:http://x.x.x.x:31000
  
20. `kubectl get pods -o wide`

<img width="1920" height="398" alt="image" src="https://github.com/user-attachments/assets/c9073dc0-25bd-4c55-98c2-8557e5317d1e" />

> [!NOTE]
> **About the Pod IPs (192.168.x.x)**:  
> - These are private IPs assigned to pods by the CNI (Container Network Interface) plugin (usually AWS VPC CNI in EKS).
> - The IPs come from the subnets of your VPC, just like EC2 instances.
> - These IPs are only reachable within the cluster (unless you set up special networking or expose via a service).
> - Pod IPs can change if the pod is recreated.
> - What it’s used for:
>   - This IP is used by other pods inside the cluster to communicate directly with it.Example: A frontend pod calls a backend pod via its pod IP.
>   - Kubernetes Services (like ClusterIP, NodePort) forward traffic to the pod’s IP.The Service acts like a load balancer, forwarding requests to the correct Pod IP and port. 

21. Now to go your worker nodes SG and add inbound rule from 0.0.0.0/0 on port 31000 and then try to access on : http://node-ip:31000

<img width="1920" height="474" alt="image" src="https://github.com/user-attachments/assets/d4377e24-b215-48cd-947a-a961b462ee5f" />

## SERVICE : LOADBALANCER  

21. We will deploy the gamutkart application on pods using docker images from docker-hub
22. Create deploy-gamutkart.yml file:
```
apiVersion: apps/v1
kind: Deployment
metadata:
    name: gamutkart-deploy
    labels:
      app: gamutkart-app
spec:
  replicas: 6
  selector:
    matchLabels:
      app: gamutkart-app
  template:
    metadata:
      labels:
        app: gamutkart-app
    spec:
      containers:
        - name: gamutkart-container
          image: dprasaddevops/gamutkart-img8
          ports:
          - containerPort: 8080
          command: ["/bin/sh"]
          args:  ["-c", "/root/apache-tomcat-9.0.85/bin/startup.sh; while true; do sleep 1; done;"]
```
23. Create service-gamutkart.yml
```
apiVersion: v1
kind: Service
metadata:
  name: gamutkart-prod
  labels:
    app: gamutkart
    env: prod
spec:
  type: LoadBalancer
  selector:
    app: gamutkart-app
  
  ports:
  - nodePort: 32000
    port: 8080
    targetPort: 8080
```
24. Apply both the .yml files
```
kubectl apply -f deploy-gamutkart.yml
kubectl apply -f service-gamutkart.yml
```

<img width="1920" height="58" alt="image" src="https://github.com/user-attachments/assets/a35159b2-c703-4ef6-9844-2d72ef77d234" />
<img width="1918" height="63" alt="image" src="https://github.com/user-attachments/assets/17485126-6394-4d1f-bb25-dee27587c978" />

25. Copy load balancer's address: `kubectl get service`
  
<img width="1915" height="237" alt="image" src="https://github.com/user-attachments/assets/e0a0fd87-b6c1-4795-8551-2415f82cf700" />
  
27. Open in the url: http://<lb address>:8080/gamutkart
  
<img width="1919" height="660" alt="image" src="https://github.com/user-attachments/assets/51e2bc48-5e20-476a-a003-823b2549aecf" />

> [!NOTE]
> **✅ Exposure Path (LoadBalancer):**
> - Your app container (in the pod) is running on port 8080.
> - The Service (gamutkart-prod) maps to those pods and listens on port 8080.
> - Because type: LoadBalancer is used:
> - AWS provisions an external ELB (Elastic Load Balancer).
> - ELB listens on port 8080 (defined by port:).
> - ELB forwards that to your Kubernetes Service → then to the container port (targetPort: 8080).
> - 📌 So, port 8080 is exposed publicly via:ELB:8080 → Service:8080 → Pod:8080
> - The nodePort: 32000 is not used in this flow — it only applies to type: NodePort.
  
# PORT FORWARDING
  
Port forwarding in Kubernetes is a mechanism that allows you to access services running inside a Kubernetes cluster from your local machine or another external system.

🧵 1. NodePort Service (e.g., NGINX)  
```
spec:
  type: NodePort
  selector:
    app: nginx-pod
  ports:
  - nodePort: 31000
    port: 80
    targetPort: 80
```
```
<YourBrowser> → NodeIP:31000 ─┐
                             ↓
                       Service Port 80
                             ↓
                        Pod Port 80 (nginx)
```
  
✅ Breakdown:  
- targetPort: 80 → nginx container is listening on port 80
- port: 80 → Service receives traffic on port 80 internally
- nodePort: 31000 → Exposes service on each worker node’s public IP:31000
- You access it with: http://<Node_Public_IP>:31000

🧵 2. LoadBalancer Service (e.g., Tomcat)  
```
spec:
  type: LoadBalancer
  selector:
    app: gamutkart-app
  ports:
  - nodePort: 32000   # Optional, AWS doesn’t use this for LB
    port: 8080
    targetPort: 8080
```
```
<YourBrowser> → AWS ELB:8080 ─┐
                             ↓
                       Service Port 8080
                             ↓
                     Pod Port 8080 (Tomcat)
```

✅ Breakdown:  
- targetPort: 8080 → Tomcat container listens on 8080
- port: 8080 → Service listens on 8080
- nodePort: 32000 → Mostly unused in LoadBalancer; still required in some cases by K8s but ELB doesn’t expose this
- You access it with:http://<External-ELB-DNS>:8080

🧠 Summary Table  
| **Keyword** | **Meaning**                                                                 |
|-------------|------------------------------------------------------------------------------|
| `targetPort` | The port on the container (pod) that your app listens on                   |
| `port`       | The port exposed by the Kubernetes Service itself(	Port exposed inside the cluster (for other pods))                          |
| `nodePort`   | (Only for NodePort & LoadBalancer) A port exposed on worker nodes(External access via <NodeIP>:<nodePort>)          |

## PORT FORWARDING Command
  
- Step 1: Use port-forward command
`kubectl port-forward service/gamutkart-prod 9090:8080`
- “Please forward my server’s localhost:9090 to the Service’s port 8080, which goes to the Pod’s containerPort 8080.”
- Step 2: Access the app from the same server:
  ```
  curl http://localhost:9090
  ```
- flow:
```
Your VM (localhost:9090)
     │
     ▼
[Kubernetes Port-Forward Tunnel]
     │
     ▼
Service: gamutkart-prod (port: 8080)
     │
     ▼
Pod (targetPort: 8080 → containerPort: 8080)
     │
     ▼
App running inside container
```

- `port-forward service/gamutkart-prod 9090:8080 --address 0.0.0.0`
  - ✅ By Default (without --address),When you run:
```
kubectl port-forward service/gamutkart-prod 9090:8080
```
  - Kubernetes binds the port only to 127.0.0.1 (i.e., localhost on the VM). So only if you are inside the vm then u can access the url like `curl http://localhost:9090` but u can't access from your local laptop's browser.
  - 🔓 With --address 0.0.0.0 `kubectl port-forward service/gamutkart-prod 9090:8080 --address 0.0.0.0` : Tells Kubernetes to bind to all network interfaces on the VM:
    - 127.0.0.1 (localhost)
    - 10.x.x.x (private IP)
    - <public-ip> (public IP if any)

- 🔁 What this means:
  - Now external machines (like your laptop) can access the VM's 9090 port — if allowed by security/firewall rules.
  - So this command:`curl http://localhost:9090 `(inside VM) ✅
  - `http://<VM-public-IP>:9090` (from browser on your laptop) ✅ (if port allowed)
