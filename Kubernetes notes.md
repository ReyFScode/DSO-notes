# **Kubernetes (K8s) Notes**
Kubernetes, commonly referred to as K8s, is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications. Kubernetes enables organizations to manage containerized workloads across a cluster of machines, providing flexibility, scalability, and resilience.

#goThrough ayush kumar on linkedin to distill hsi learning materials
#add info on cgroups drivers / container runtimes [Configuring a cgroup driver | Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/configure-cgroup-driver/)  /   [Container Runtimes | Kubernetes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)


----

# **Key Kubernetes terms**

1. **Pod**: The smallest unit of deployment in Kubernetes, representing one or more containers that are scheduled together on the same host. 

2. **Node**: A physical or virtual machine in the Kubernetes cluster that runs containers.

3. **Cluster**: A set of nodes that are grouped together to run Kubernetes and the applications deployed on it.

4. **Deployment**: A Kubernetes resource that manages a set of identical pods, ensuring they are running and healthy.

5. **Service**: An abstraction that defines a logical set of pods and a policy by which to access them, typically providing load balancing for the pods.

6. **Namespace**: A way to divide cluster resources between multiple users (via resource quotas) or to partition resources by environment (such as development, staging, or production).

7. **Container**: Built from images, a lightweight isolated instance that includes everything needed to run a piece of software.

8. **Controller**: A Kubernetes resource responsible for managing a specific type of object, such as Deployments, ReplicaSets, or StatefulSets, ensuring that the desired state is maintained.

9. **ReplicaSet**: A Kubernetes resource that ensures a specified number of pod replicas are running at any given time.

10. **StatefulSet**: A Kubernetes resource used to manage stateful applications, providing guarantees about the ordering and uniqueness of pod deployment and scaling.

11. **Ingress**: A Kubernetes resource that manages external access to services within a cluster, typically acting as a layer 7 (HTTP) reverse proxy. It allows you to define rules for routing traffic to services based on host information and the path of incoming requests.

12.  **Egress:** In Kubernetes, Egress refers to the traffic that flows out of a cluster, from a pod to an external endpoint12. Egress traffic can be used to access external services such as databases, APIs, and other services running outside of the cluster

13. **PersistentVolume**: A piece of storage in the cluster that has been provisioned by an administrator, which can be mounted into a pod.

14. **Secret**: An object used to store sensitive information, such as passwords, OAuth tokens, and SSH keys, which can be mounted into a pod as a volume or exposed as environment variables.

15. **ConfigMap**: An object used to store configuration data in key-value pairs, which can be mounted into a pod as a volume or exposed as environment variables.

16. **DaemonSet**: A Kubernetes resource that ensures that all (or some) nodes run a copy of a specific pod, typically used for system daemons or log collectors

17.  **Contexts:** A context is essentially a named tuple that encapsulates this information. When you switch between contexts, you're instructing `kubectl` to use a different set of parameters to interact with different Kubernetes clusters. This allows users to seamlessly switch between multiple clusters without having to manually reconfigure `kubectl` each time. For example, if you have two Kubernetes clusters, `cluster-1` and `cluster-2`, each with its own API server endpoint and authentication details, you can define two contexts:
	- Context 1: `cluster-1-context` - Contains the parameters for accessing `cluster-1`.
	- Context 2: `cluster-2-context` - Contains the parameters for accessing `cluster-2`.
	Then, you can use `kubectl` commands with a specific context to interact with the corresponding cluster:

18. **CNI**: Container Networking Interface (CNI) plugins are essential components in Kubernetes clusters responsible for facilitating network communication between pods, enabling seamless connectivity both within and across nodes. They assign IP addresses to pods, enforce network policies, and manage traffic routing within the cluster. Each CNI plugin integrates with Kubernetes to provide networking capabilities tailored to specific requirements, such as scalability, performance, and security. Popular CNI plugins like Calico, Flannel, and Cilium offer diverse features and deployment options, allowing users to choose the most suitable solution for their cluster environment. By installing and configuring a CNI plugin, Kubernetes administrators ensure that pods can communicate effectively, enabling applications to function correctly within the cluster.


----

# **Kubernetes Architecture**

Kubernetes architecture consists of several components working together to manage containers and orchestrate application deployment.

**Here we will go over the Kubernetes components and compare them to docker swarm so you can better understand what they do:**

**Control Plane Components**:
    - **kube-apiserver**: This component in Kubernetes is analogous to the Docker Swarm manager in terms of functionality. Both serve as the entry point for cluster management operations and expose APIs for interacting with the cluster. They are responsible for receiving and handling requests to create, update, and delete resources in the cluster.
    - **etcd**: This distributed key-value store in Kubernetes serves as the cluster's backing store for all cluster data. In Docker Swarm, a similar function is performed by the built-in Raft consensus algorithm, which manages the cluster state and ensures consistency across manager nodes.
    - **kube-scheduler**: The Kubernetes scheduler is responsible for selecting an appropriate node for newly created pods based on resource requirements, affinity/anti-affinity rules, and other constraints. In Docker Swarm, container placement decisions are made by the built-in scheduler, which ensures optimal resource utilization across the cluster.
    - **kube-controller-manager**: This component in Kubernetes runs controller processes responsible for maintaining the desired state of the cluster. Similar functionality in Docker Swarm is provided by the built-in controller processes, such as the node controller and replication controller, which ensure that the cluster maintains the desired number of nodes and replicas of services.
        
**Node Components**:
    - **kube-proxy**: This component in Kubernetes maintains network rules on nodes to enable communication between pods and external traffic. It implements the Kubernetes service abstraction by managing network routing and load balancing. In Docker Swarm, similar functionality is provided by the built-in routing mesh, which ensures that incoming traffic is routed to the appropriate containers across the cluster.

**There are also three important tools that are used to interact with/create K8s deployments:**

- **kubeadm**: kubeadm is a command-line tool used for bootstrapping Kubernetes clusters. It simplifies the process of setting up and configuring the control plane nodes and joining worker nodes to the cluster. It initializes the cluster control plane, including setting up the kube-apiserver, kube-controller-manager, kube-scheduler, and etcd, Joins worker nodes to the cluster, configuring them to communicate with the control plane components,  and provides options for configuring various aspects of the cluster, such as networking, container runtime, and authentication.

- **kubectl**: kubectl is the command-line interface (CLI) for interacting with Kubernetes clusters. It allows users to manage and control Kubernetes resources, deploy applications, and monitor cluster operations.

 - **kubelet**: The kubelet is a node component, it is an agent that runs on each node in the Kubernetes cluster and ensures that containers are running in pods as expected. It interacts with the container runtime (e.g., Docker) to manage container lifecycle operations such as starting, stopping, and restarting containers. In Docker Swarm, a similar role is performed by the Docker Engine running on each node, which manages containers and executes tasks assigned by the Swarm manager.

In summary, kubeadm is used for cluster bootstrap and initialization, kubectl is used for cluster management and resource manipulation, and kubelet is responsible for managing containers on individual nodes and ensuring their health and availability.


----

# **K8s CNI plugins**

Kubernetes itself doesn't provide built-in networking capabilities. Instead, it relies on pluggable CNI (Container Network Interface) plugins to handle networking tasks such as pod-to-pod communication, IP address management, and network policy enforcement. CNI plugins are essential components that facilitate network communication between containerized workloads in a Kubernetes cluster. They are responsible for configuring network interfaces, managing IP addresses, and handling network policies within the cluster.

Some common CNIs:

1. **Calico**: Calico is a popular choice for Kubernetes networking. It provides network policy enforcement, IP address management, and secure communication between pods across nodes. Calico is often chosen for its scalability and support for large-scale deployments.

2. **Flannel**: Flannel is another widely used CNI plugin that offers a simple and easy-to-deploy networking solution for Kubernetes. It creates an overlay network that allows pods to communicate with each other regardless of the underlying network infrastructure. Flannel is known for its simplicity and reliability.

3. **Cilium**: Cilium is a modern CNI plugin that focuses on providing secure networking and observability for Kubernetes clusters. It utilizes eBPF (extended Berkeley Packet Filter) technology to implement network policies and enforce security rules at the kernel level. Cilium offers high performance and advanced features such as transparent encryption and HTTP/API-aware security.

4. **Weave Net**: Weave Net is a CNI plugin that offers a simple and flexible networking solution for Kubernetes clusters. It creates a virtual network that spans across nodes, allowing pods to communicate securely with each other. Weave Net also provides features like network segmentation, encryption, and automatic IP address management.

5. **Kube-router**: Kube-router is a CNI plugin that integrates networking and routing functionalities directly into Kubernetes. It offers dynamic routing, network policy enforcement, and service discovery capabilities. Kube-router is designed to be lightweight and efficient, making it suitable for both small and large Kubernetes deployments.

**Best practices and tips for Kubernetes CNI plugins:**

- **Evaluate based on use case**: Different CNI plugins have unique features and performance characteristics. Choose the one that best fits your requirements, considering factors such as scalability, security, and ease of management.

- **Ensure compatibility**: Make sure that the chosen CNI plugin is compatible with your Kubernetes version and other components in your cluster, such as the container runtime and operating system.

- **Implement network policies**: Utilize Kubernetes Network Policies to enforce security rules and restrict communication between pods based on labels, namespaces, and other criteria. This helps improve the overall security posture of your cluster.


----

# **K8s deployment types:**

...in progress

----

# **Installing Kubernetes (BASIC) (for use with docker)**

##### Installation instructions:
This tutorial will focus on Debian based distros. Installing Kubernetes can be surprisingly daunting, a lot of instructions online are overly convoluted, here's how to do it (you should probably make an ansible playbook to make installing it on all your Kubernetes nodes easy). If this doesn't work, installation docs are here, use most recent version: [Installing kubeadm | Kubernetes](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

**pre-flight configs**
Start by running this on all nodes to open the necessary ports for K8s communication: `sudo ufw allow 6443/tcp && sudo ufw allow 10250/tcp && sudo ufw allow 10255/tcp && sudo ufw allow 2379/tcp && sudo ufw allow 2380/tcp && sudo ufw allow 53/tcp && sudo ufw allow 53/udp` also ensure that the */etc/apt/keyrings* directory exists, if not make it with this command: `sudo mkdir -p -m 755 /etc/apt/keyrings`. Finally turn swapoff using this command: `sudo swapoff -a`

**Installation procedure**
1. Ensure docker is installed on all your Kubernetes nodes
2. Add the Kubernetes repository by running this command on all nodes: 
```shell
sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates curl gpg && curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg && echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list && sudo apt-get update
```

5. Now install kubernetes via apt and then use apt-mark to hold the packages (Ensures the packages cannot be upgraded, removed, or purged.):   `sudo apt install -y kubelet kubeadm kubectl && sudo apt-mark hold kubelet kubeadm kubectl`
6. Optionally you can set the kubectl service to start immediately at boot with:  `sudo systemctl enable --now kubelet` (you probably want this)

you can turn swap back on with:   `sudo swapon -a` , you can remove the hold from the packages with:   `sudo apt-mark unhold kubelet kubeadm kubectl`

**!! Common error - *container runtime is not running*:**  sometimes you may get a "container runtime is not running" error when starting/joing a kubernetes deployment, to remedy this you can run this command:  `rm /etc/containerd/config.toml && systemctl restart containerd`   After running the command retry your init command 


----

# **Installing Kubernetes (ADVANCED)

##### Installation instructions:
This tutorial will focus on Debian based distros. 



----

#  **Kubernetes essential commands**

**Creation/teardown commands:**
1. **Initialize K8s cluster**
```bash
kubeadm init
```
init cheatsheet: [kubeadm init | Kubernetes](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/)

2. **Join a worker to a K8s cluster**
```bash
kubeadm join [IP]:6443 --token 7fd89s0fds......
# This commmand is auto-generated by kubeadm init
```

3. Re-display join command
```bash
kubeadm token create --print-join-command
```

4. Remove worker from K8s cluster, run from master node
``` bash
kubectl get-nodes # get nodes in the cluster, note the name of the node(s) you wish to remove
kubectl drain [node-name] --ignore-daemonsets
kubectl delete node [node-name]

```

5. **De-initialize a node that kubeadm init was run on**
```bash
kubeadm reset # should only be run after all nodes are deleted to ensure a clean cleanup
```

6. **Apply deployment / delete deployment**
```
kubectl apply -f [deployment.yaml]
kubectl delete deployment [deployment name]
```

**Cluster management commands:**
1. **Display Kubernetes Cluster backend Information**
```bash
kubectl cluster-info
```

2. **List Nodes in cluster**
```bash
kubectl get nodes
kubectl get nodes -o wide #verbose get, use this
```

3. **List Pods
```bash
kubectl get pods 
kubectl get pods -o wide #verbose get, use this
```

4. **Describe Pod Details**
```bash
kubectl describe pod <pod_name>
```

5. **Describe active deployments**
```
kubectl get deployments
```

6. **Enter a kubernetes pod**
```bash
kubectl exec -it [pod_name] -- bash
# -- isnt needed as of this document but soon will be required
```

8. **Create or Apply a Configuration**
```bash
kubectl apply -f <configuration.yaml> # can be deployments, CNI manifests, etc. kubectl apply is a very versatile command
```

8. **Scale a Deployment**
```bash
kubectl scale deployment <deployment_name> --replicas=<replica_count>
```



****

# **Tutorial: Performing a basic Kubernetes deployment**

In kubernetes the state of a deployment (the services, configurations, etc.) are defined using YAML files called deployment manifests. Before we create this file we must initialize a cluster. in this example we will use two machines (one master & one slave) that both have kubeadm, kubelet, and kubectl installed (see install guide above). Here are the steps to initialize a cluster & create a simple deployment. To follow along you should use two hosts (one master one worker, preferably Linux). We will be utilizing the calico CNI for cluster networking.


1) On the master node we initialize the control plane using `kubeadm init` , we need to specify a pod network CIDR for the cluster to use (more on that in the networking section):  
```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
 # this should be a safe non-conflicting pod network to use, default ip/cidr is: sudo kubeadm init --pod-network-cidr=10.244.0.0/16
# for info about non-conflicting IPs use this: https://www.techtarget.com/whatis/definition/RFC-1918
```
Make a note of the token provided by this command! you must use it to join nodes to the cluster. If you lose it you can regenerate it with this command: `sudo kubeadm token create --print-join-command`

 2)  Now you must run these commands as a regular user (not in a sudo shell!), these commands are provided by kubeadm init:
 ```bash
mkdir -p $HOME/.kube # Creates a .kube directory in the user's home directory if it doesn't already exist.

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config # the admin.conf file is generated by kubeadm during the initialization of a Kubernetes cluster. It's a configuration file used in Kubernetes administration. It typically contains authentication credentials and configuration settings necessary for administrative access to a Kubernetes cluster. It allows once to interact with the cluster using tools like kubectl, the Kubernetes command-line interface.

sudo chown $(id -u):$(id -g) $HOME/.kube/config # Changes the ownership of the config file in the .kube directory to the current user.
```
Don't export the admin.conf path.
After you make the directory be sure to execute all kubectl commands as the regular user, don't use a sudo shell. You will have configured k8s to run under the current user with step #2. 

3) Now we will move to the next step, downloading the calico CNI for use by our cluster, run the below steps on the master node:
	Begin by downloading the calico manifest file (manifests are YAMLs that define installations) with this command: 
	`cd /tmp ; wget https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/calico.yaml`
	Now navigate to the /tmp directory and apply the manifest to your kubernetes host with this command: 
	`kubectl apply -f calico.yaml`

 4)  Now run `kubectl cluster-info; kubectl get nodes` to validate that your cluster is initialized and that your master node is ready.
 
 5) Now that your cluster is initialized we need to join a worker, navigate to your other host and run the join command provided by `kubeadm init`.

6) Finally we are ready to perform our first deployment (more on deployments below), for this we are just going to deploy a simple ubuntu container deployment with 3 containers (replicas). Start by creating a file on the master called `deployment.yml` add this content to it:
```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ubuntu-deployment
  labels:
    app: ubuntu
spec:
  replicas: 3
  selector:
    matchLabels:
      app: ubuntu
  template:
    metadata:
      labels:
        app: ubuntu
    spec:
      containers:
        - name: ubuntu_container
          image: ubuntu:latest
          ports:
            - containerPort: 80
          args: ["/bin/bash"]
```
Now navigate to the directory that you made it in and run:   `kubectl apply -f deployment.yml`
Your deployment should now be live! check on it with these commands: `kubectl get deployments` & `kubectl get pods`

7) Finally lets teardown this deployment and cluster, run the below commands in order (on the master node):
```bash 
kubectl delete deployment ubuntu-deployment # deletes the deployment
kubectl get nodes # shows nodes (hosts) make note of all the non control-plane nodes (worker) in the cluster
kubectl cordon [name_of_worker] # marks node so no new pods can be run on it
kubctl drain [name_of_worker] --ignore-daemonsets #d rains all pods from the node
kubectl delete node [name_of_worker] # deletes a node from the cluster
kubectl drain [name_of_master_node] # drains master node
sudo kubeadm reset; rm -r $HOME/.kube # resets kubeadm and deletes .kube directory
```
Now your cluster should be torn down! all done!



----

# **Kubernetes Deployments/Services in depth**

**In Kubernetes, we can deploy deployments or services:**
In depth on deployments v services: [Kubernetes 101 for Beginners: Deployments vs. Services Unraveled - howtouselinux](https://www.howtouselinux.com/post/understanding-kubernetes-deployments-vs-services#:~:text=Understanding%20Kubernetes%3A%20Deployments%20vs%20Services%201%20Deployment%20Purpose%3A,by%20which%20to%20access%20them.%203%20Key%20Differences)

- **Deployment:** A Deployment is responsible for managing stateless applications and services by deploying a specified number of pod replicas (containers) and ensuring they are running as defined.

- **Service:** A Service in Kubernetes is an abstraction that defines a logical set of pods (often spanning multiple Deployments or ReplicaSets) and a policy by which to access them.

...in progress
#### Creating a Deployment Document

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

#### Exposing Deployments via a Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```



----

# **Kubernetes Networking basics**


dive into flannel calico and weave / --pod-networ-cidr stuff like that
----

# **Advanced Kubernetes Topics**

1. **Horizontal Pod Autoscaler (HPA):** Automatically adjusts the number of replica pods in a deployment based on CPU or custom metrics.
   
2. **Ingress Controller:** Manages external access to services within a Kubernetes cluster, typically via HTTP or HTTPS.

3. **Persistent Volumes:** Storage resources provisioned independently of pod lifecycles, allowing data to persist across pod restarts.

4. **StatefulSets:** Manages the deployment and scaling of stateful applications, such as databases, with stable network identifiers and persistent storage.

----
# **AWS K8s (AWS EKS)**

1. **Horizontal Pod Autoscaler (HPA):** Automatically adjusts the number of replica pods in a deployment based on CPU or custom metrics.
   
2. **Ingress Controller:** Manages external access to services within a Kubernetes cluster, typically via HTTP or HTTPS.

3. **Persistent Volumes:** Storage resources provisioned independently of pod lifecycles, allowing data to persist across pod restarts.

4. **StatefulSets:** Manages the deployment and scaling of stateful applications, such as databases, with stable network identifiers and persistent storage.

----

#### Additional Resources

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)
- [Kubernetes Tutorials and Examples](https://kubernetes.io/docs/tutorials/)
- [Kubernetes GitHub Repository](https://github.com/kubernetes/kubernetes)