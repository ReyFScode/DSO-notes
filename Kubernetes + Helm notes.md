# **Kubernetes (K8s) Notes**
study set: [K8s Flashcards | Quizlet](https://quizlet.com/931874317/k8s-flash-cards/)
Kubernetes, commonly referred to as K8s, is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications. Kubernetes enables organizations to manage containerized workloads across a cluster of machines, providing flexibility, scalability, and resilience.

#add info on cgroups drivers / container runtimes [Configuring a cgroup driver | Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/configure-cgroup-driver/)  /   [Container Runtimes | Kubernetes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)

add crictl info




----
# **Installing Kubernetes (bare-metal, kubeadm) (for use with docker)**

#### Installation instructions:
This tutorial will focus on Debian based distros. Installing Kubernetes can be surprisingly daunting for newcomers, a lot of instructions online are overly convoluted, here's how to do it (you should probably make an ansible playbook to make installing it on all your Kubernetes nodes easy). If this doesn't work, installation docs are here, use most recent version: [Installing kubeadm | Kubernetes](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

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







---
# **Common errors**
-  !! Common error - *container runtime is not running*:  
	sometimes you may get a "container runtime is not running" error when starting/joing a kubernetes deployment, to remedy this you can run this command:  `rm /etc/containerd/config.toml && systemctl restart containerd`   After running the command retry your init command 

- !! LESS COMMON ERROR - *The connection to the server x.x.x.:6443 was refused - did you specify the right host or port?* 
	Even after making directory / exporting admin kube var.: not sure why this occurs, some say its a version specific bug. on master node run:
	1. `sudo -i `
	2. `swapoff -a `
	3. `systemctl restart kubelet`
	4. `exit`
	5. `strace -eopenat kubectl version`
	Then use try again, you can use strace without the flag to monitor system calls.
	or if you can reset the cluster and re-init run:
	`sudo kubeadm init ; sudo kubeadm init phase bootstrap-token`

- !! LESS COMMON ERROR - *ERROR Port-6443: Port 6443 is in use* **
	 To resolve run `sudo netstat -tulpn | grep 6443` and then run `kill -9 [pid]` replacing pid with the process ID of whatever is using port 6443 (usually kube-apiserver )




---
# **Key Kubernetes terms**

1. **Pod**: The smallest unit of deployment in Kubernetes, representing one or more containers that are scheduled together on the same host. **Replicas** are the amount of pods in a deployment NOT number of containers since a pod can be multiple grouped containers

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
    
19. **Control loop:** This is the name of the process that K8s uses to manage cluster state, the control loop is essentially constantly comparing the cluster state with the desired state and performing actions that ensure the cluster is always at its desired state.

20. **Manifests:** a yaml/json doc that describes the state of an object, can be anything e.g. deployment, configmap, secret, service, volume, etc. 



----
# **Kubernetes Architecture**

Kubernetes architecture consists of several components working together to manage containers and orchestrate application deployment.

**Here we will go over the Kubernetes components and compare them to docker swarm so you can better understand what they do:**

**Control Plane Components**:
    - **kube-apiserver**: This component in Kubernetes is analogous to the Docker Swarm manager in terms of functionality. Both serve as the entry point for cluster management operations and expose APIs for interacting with the cluster. They are responsible for receiving and handling requests to create, update, and delete resources in the cluster.
    - 
    - **etcd**: This distributed key-value store in Kubernetes serves as the cluster's backing store for all cluster data. In Docker Swarm, a similar function is performed by the built-in Raft consensus algorithm, which manages the cluster state and ensures consistency across manager nodes.
    - 
    - **kube-scheduler**: The Kubernetes scheduler is responsible for selecting an appropriate node for newly created pods based on resource requirements, affinity/anti-affinity rules, and other constraints. In Docker Swarm, container placement decisions are made by the built-in scheduler, which ensures optimal resource utilization across the cluster.
    - 
    - **kube-controller-manager**: Contains controllers for managing different types of resources. Each controller is responsible for ensuring that the current state of a resource matches its desired state.
		*Examples include of builtin controllers are:*
	    **ReplicaSet Controller**: Ensures that the desired number of Pod replicas are running.
	    **Deployment Controller**: Manages the rollout and updates of Deployments.
	    **Service Controller**: Handles the creation and management of Services, ensuring that they route traffic correctly.
	    **etc.** 
	    You can create custom controllers, this is part of creating operators, more on this in a below section.
    
**Node Components**:
    - **kube-proxy**: This component in Kubernetes maintains network rules on nodes to enable communication between pods and external traffic. It implements the Kubernetes service abstraction by managing network routing and load balancing. In Docker Swarm, similar functionality is provided by the built-in routing mesh, which ensures that incoming traffic is routed to the appropriate containers across the cluster.
    - **Kubelet**: more on this below

---

**There are also three important tools that are used to interact with/create K8s deployments:**

- **kubeadm**: kubeadm is a command-line tool used for bootstrapping Kubernetes clusters. It simplifies the process of setting up and configuring the control plane nodes and joining worker nodes to the cluster. It initializes the cluster control plane, including setting up the kube-apiserver, kube-controller-manager, kube-scheduler, kube-proxy, and etcd, Joins worker nodes to the cluster, configuring them to communicate with the control plane components,  and provides options for configuring various aspects of the cluster, such as networking, container runtime, and authentication.

- **kubectl**: kubectl is the command-line interface (CLI) for interacting with Kubernetes clusters. It allows users to manage and control Kubernetes resources, deploy applications, and monitor cluster operations.

 - **kubelet**: The kubelet is a node component, it is an agent that runs on each node in the Kubernetes cluster and ensures that containers are running in pods as expected. It interacts with the container runtime (e.g., Docker) to manage container lifecycle operations such as starting, stopping, and restarting containers. In Docker Swarm, a similar role is performed by the Docker Engine running on each node, which manages containers and executes tasks assigned by the Swarm manager.

In summary, kubeadm is used for cluster bootstrap and initialization, kubectl is used for cluster management and resource manipulation, and kubelet is responsible for managing containers on individual nodes and ensuring their health and availability.

#flesh_out--Most control plane components run as pods on the master node. To view control plane components run `kubectl get pods -n kube-system` on the control plane, you can use `describe resourcename` to get logs.






----
#  **Kubernetes essential commands**

**Creation/teardown commands:**
1. **Initialize K8s cluster via kubeadm**
```bash
kubeadm init
```
init cheatsheet: [kubeadm init | Kubernetes](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/)

2. **Join a worker to a K8s cluster**
```bash
kubeadm join [IP]:6443 --token 7fd89s0fds......
# This commmand is auto-generated by kubeadm init
```

3. **Re-display join command**
```bash
kubeadm token create --print-join-command
```

4. **Remove worker from K8s cluster, run from master node**
``` bash
kubectl get-nodes # get nodes in the cluster, note the name of the node(s) you wish to remove
kubectl drain [node-name] --ignore-daemonsets
kubectl delete node [node-name]

# NOTE kubectl delete can delete anything e.g. deployment/somedep, pod/somepod

```

5. **De-initialize control plane ( a node that kubeadm init was run on )**
```bash
kubeadm reset # should only be run after all nodes are deleted to ensure a clean cleanup
```

6. **Apply manifest .yml
```yaml
kubectl apply -f [mainfest name.yaml]

#reverse with 
kubectl delete -f [manifest_name.yaml]


 # OR you can do it in [object]/[name] format
kubectl delete deployment/test_deployment
```



**Cluster management commands:**
1. **Display Kubernetes Cluster backend Information**
```bash
kubectl cluster-info
```

2. **List objects in k8s cluster**
```bash
kubectl get nodes #gets nodes
kubectl get deployments #gets deployments

OR get multiple objects with comma seperation:   nodes,pods,deployments

-o wide #append to perform a verbose get, use this


get all with: 
  - kubectl get all
```

3. **Describe an object**
```bash
# describe gets details about a specific k8s object
kubectl describe object/name
#object can be pod/deployment/service/node...
```

4. ...
```yaml
...
```

5. **Enter a Kubernetes pod**
```bash
kubectl exec -it [pod_name] -- bash
# used to run a new process e.g. bash seperate from containers main entrypoint command (process)

kubectl attach -it [pod_name]
# used to attach to main entrypoint process
```

6. **...**
```bash
...
```

7. **Generate a deployment YAML / create a single container deployment**
```bash
#deployment.ymls can be very complex and hard to remember how to write, k8s can autogenerate the .ymls with the command, we can then modify this file to customize the deployment
kubectl create deployment [name] --image [something:something] -o yaml --dry-run=client> [somename].yml #--dry-run=client doesnt activate the deployment, just generates the yaml, if we omit this flag then the deployment will be created and go live in addition to the YAML being generated

# *! you can pass images with multiple flags or comma seperated e.g. --images nginx,mysql,ubuntu BUT only one port (must be added manually)

#if we omit the `-o --dry-run... >` section we can start a deployment with specified images
kubectl create deployment [name] --image [something:something]

some flags:
> --image
> --port
> --replicas

```

8. **Generate a single container pod / create a pod YAML**
```bash
#Pods, like deployments, can be defined via a yaml file as well, to generate a pod yaml we use kubectl run pass in pod specifications via flags like --image and then specify -o yaml + --dry-run=client
kubectl run [container/pod name] --image [something:something] -o yaml --dry-run=client > [somename].yml

#if we omit the `-o --dry-run... >` section we can run a single pod (Container), to connect we can add -it, similar to docker run (-it cant be used in conjunction with --dry-run=client -o yaml, it will immediately take you into the container instead of simply generating the pod manifest, to add -it functionality to a pod created via a manifest you must add stdin: true & tty: true to the spec section)
kubectl run -it [container/pod name] --image [something:something]

#---------------------------------------------------------------
#some other flags we can add to our pod/container (can be used with --dry-run/-o):
#---**NOTE: the supplied value after the flag is always capitalized---

--image-pull-policy IfNotPresent, Always, etc. # > specifies a pull policy the image (e.g. if not present only pulls the image if it cant be found locally)

--restart Always, Never, etc. # > specifies a restart policy for the pod

--port 80 # > specifies a container port to expose
+
--expose # actually exposes the port
= --port 80 --expose #used in conjunction

```

9. **Directly update a Kubernetes object through its manifest via your default text editor **
```yaml
kubectl edit [object: deployment, pod, etc.] [name]

# will open the objects properties in yaml format, allows you to make changes that apply when the file is saved & closed
```

10. **Scale a deployment, replica set, replication controller, or stateful set**
```bash
kubectl scale [object: deployment, stateful-set, etc.] [object_name] --replicas=<replica_count>

can also be set up as : 
kubectl scale --flags object/name (e.g deployment/nameofDeployment)

--------------------------------------------------------------------

Scale also allows users to specify one or more preconditions for the scale action.
If --current-replicas or --resource-version is specified, it is validated before the scale is attempted, and it is guaranteed that the precondition holds true when the scale is sent to the server.

Examples (taken from kubectl scale --help):
  # Scale a replica set named 'foo' to 3
  kubectl scale --replicas=3 rs/foo

  # Scale a resource identified by type and name specified in "foo.yaml" to 3
  kubectl scale --replicas=3 -f foo.yaml

  # If the deployment named mysql's current size is 2, scale mysql to 3
  kubectl scale --current-replicas=2 --replicas=3 deployment/mysql

  # Scale multiple replication controllers
  kubectl scale --replicas=5 rc/example1 rc/example2 rc/example3

  # Scale stateful set named 'web' to 3
  kubectl scale --replicas=3 statefulset/web

```

11. **Get the API Version for all k8s objects**
```bash
kubectl api-resources
```

12. **Map (forward) a port from a object to the host, this can be a pod, deployment, or even a service**
```yaml
kubectl port-forward [object_type]/[name] HostPort:ContainerPort
```

13. **generate a service manifest**
```yaml
kubectl create service [service_type] [name] --tcp=80:80 -o yaml --dry-run=client
```

14. **kubectl expose (generate a service manifest from a deployment/pod)**
```yaml
kubectl expose [pod or deployment]/[name] --port 80 --target-port 80 --type [type e.g. loadbalancer] -o yaml --dry-run=client
```

15. **get logs (output stream)**
```yaml
# you can get logs from services/pods/deployments, for most deployments this command will pull the logs from a single container within a pod

kubectl logs [object type: deployment,pod]/[name]

# the kubectl logs command defaults to pods so if you specify:
kubectl logs [name]
# it will default to searching for a pod with the matching name

# to get logs from a particular container within the pod use the -c flag
kubectl logs pods/somepod -c containerName  



```

16. **get events
```yaml
# you can use this command  to get cluster events, events are system occurances like failures, successes, creations, deletions, etc.

 # List recent events in the default namespace
  kubectl events
  
  # List recent events in all namespaces THIS IS PARTICULARLY USEFUL
  kubectl events --all-namespaces
  
  # List recent events for the specified pod, then wait for more events and list them as they arrive
  kubectl events --for pod/web-pod-13je7 --watch

  # List recent only events of type 'Warning' or 'Normal'
  kubectl events --types=Warning,Normal
Options



```


17. **update deployment image**
```yaml
#set image will change a deployments image
kubectl set image deployment/[deployment_name] old_image=new_image:tag

#e.g. kubectl set image deployment/nginx-deployment nginx=nginx:1.25

```




---
# Understanding Deployments vs Pods

### Pods

- A **Pod** is the basic unit of deployment in Kubernetes.
    
- It consists of one or more containers that:
    
    - Share the same network namespace (i.e., IP address and ports)
        
    - Can share storage volumes
        
- Pods are **ephemeral**:
    
    - If a Pod crashes, it does **not restart automatically** unless managed by a controller. (unless restartPolicy is always/OnFailure)
        
    - Not ideal for production use on their own. Practically never used beyond learning/debugging
        

### Deployments

- A **Deployment** is a Kubernetes controller that manages a set of Pods through a ReplicaSet.
    
- It ensures that the specified number of Pod replicas are running at all times.
    
- It supports:
    
    - Automatic restarts of failed Pods
        
    - Rolling updates
        
    - Rollbacks
        
    - Declarative updates and scaling
        

### Key Differences

| Feature            | Pod                                                 | Deployment                       |
| ------------------ | --------------------------------------------------- | -------------------------------- |
| Resource Type      | Basic container wrapper                             | High-level controller            |
| Restart on failure | No                                                  | Yes                              |
| Scaling            | Manual mostly, you can create a pod manifest though | Declarative (via replicas field) |
| Rolling Updates    | No                                                  | Yes                              |
| Use Case           | Testing, debugging                                  | Production workloads             |







---
# Understanding Services and Service Types and How They Relate to Deployments

### What is a Service?

- A **Service** is an abstraction that defines a logical set of Pods and a policy to access them.
    
- Since Pods are dynamic and their IPs can change, Services provide a **stable endpoint** for communication.
    
- Services use **label selectors** to target and load-balance traffic across a group of Pods.
    

### How Services Relate to Deployments

- A Deployment creates and manages Pods, and a Service exposes those Pods to other components inside or outside the cluster.
    
- The Service continuously discovers and forwards traffic to any Pods that match the defined selector.
    

### Service Types

|Type|Description|Scope|
|---|---|---|
|ClusterIP|Default. Exposes the Service on an internal cluster IP.|Internal|
|NodePort|Exposes the Service on a static port on each Node IP.|External (basic)|
|LoadBalancer|Provisions a cloud provider load balancer.|External (cloud only)|
|ExternalName|Maps a service name to an external DNS name.|DNS-based redirect|

### Use Cases

- **ClusterIP** is used for internal communication (e.g., between microservices).
    
- **NodePort** and **LoadBalancer** are used for exposing services to users or external systems.
    
- **ExternalName** is useful for referencing external APIs or databases using a consistent internal name.
    







---
# Understanding ConfigMaps and Secrets and How They Relate to Deployments

### ConfigMaps

- A ConfigMap is a key-value store for **non-sensitive** configuration data.
    
- Common use cases include storing environment settings, URLs, file paths, and feature flags.
    
- Keeps configuration **external to application code** for flexibility and reuse.
    

### Secrets

- A Secret is a similar key-value store, but intended for **sensitive data** such as:
    
    - API keys
        
    - Database passwords
        
    - TLS certificates
        
- Kubernetes stores Secrets in base64 format, and encryption at rest is recommended in production.
    

### Injecting into Pods

1. **As environment variables**
```
# env-demo.yaml
apiVersion: v1
kind: Pod
metadata:
  name: env-demo
spec:
  containers:
  - name: demo
    image: busybox
    command: ["sh", "-c", "env; sleep 3600"]


    env:
    - name: MODE
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: MODE
    - name: API_KEY
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: API_KEY

```
    
2. **As mounted volumes**
```
volumeMounts:
  - name: config-volume
    mountPath: /etc/config
volumes:
  - name: config-volume
    configMap:
      name: app-config

```
 
 Relation to Deployments:

- A Deployment defines the Pods, and ConfigMaps or Secrets are referenced in the Deployment's Pod template.
    
- This allows:
    
    - Updating configurations or secrets without modifying the Deployment or rebuilding container images
        
    - Environment-specific values to be injected into containers
        
    - Central management and versioning of configuration data










---
## Understanding Volumes in Deployments

### What is a Volume?

In Kubernetes, a **volume** is a directory that is accessible to containers in a Pod. It enables containers to:

- **Persist data across restarts** (unlike the container’s local ephemeral storage).
    
- **Share data** between multiple containers in the same Pod.
    
- **Mount configuration data**, secrets, or files from the host or an external source.
    
#### How Volumes Work in Deployments

A `Deployment` creates and manages Pods, and inside those Pods you can define volume mounts using:

- `volumes`: declares the volume (e.g., emptyDir, configMap, pvc).
    
- `volumeMounts`: specifies where to mount the volume inside the container.
    

Example:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp-container
        image: myapp:latest
        volumeMounts:
        - mountPath: /data
          name: mydata
      volumes:
      - name: mydata
        persistentVolumeClaim:
          claimName: mypvc

```
---

## Volume Types Commonly Used in Deployments

| Volume Type                   | Use Case                                  | Persistent? | Notes                                       |
| ----------------------------- | ----------------------------------------- | ----------- | ------------------------------------------- |
| `emptyDir`                    | Temporary storage (cleared on Pod delete) | ❌ No        | Good for caching or inter-container sharing |
| `hostPath`                    | Access host filesystem                    | ❌ No        | Not portable, risky in multi-node clusters  |
| `configMap` (as volume)       | Inject app config files                   | ❌ No        | For mounting config as files                |
| `secret` (as volume)          | Inject secrets as files                   | ❌ No        | Secure, supports file permissions           |
| `persistentVolumeClaim` (PVC) | Attach persistent storage                 | ✅ Yes       | Needed for databases or other stateful apps |

#### YAML Snippets for Each Volume Type

#### 1. `emptyDir`

```yaml
volumes:
  - name: cache-vol
    emptyDir: {}

containers:
  - name: app
    image: myapp:latest
    volumeMounts:
      - mountPath: /tmp/cache
        name: cache-vol
```

#### 2. `hostPath`

```yaml
volumes:
  - name: host-logs
    hostPath:
      path: /var/log
      type: Directory

containers:
  - name: logger
    image: busybox
    volumeMounts:
      - mountPath: /mnt/host-logs
        name: host-logs
```

#### 3. `configMap` (mounted as files)

```yaml
volumes:
  - name: config-vol
    configMap:
      name: app-config

containers:
  - name: app
    image: myapp:latest
    volumeMounts:
      - mountPath: /etc/config
        name: config-vol
```

#### 4. `secret` (mounted as files)

```yaml
volumes:
  - name: secret-vol
    secret:
      secretName: app-secret
      defaultMode: 0400

containers:
  - name: app
    image: myapp:latest
    volumeMounts:
      - mountPath: /etc/secret
        name: secret-vol
```

#### 5. `persistentVolumeClaim` (PVC)

```yaml
volumes:
  - name: data-vol
    persistentVolumeClaim:
      claimName: postgres-pvc

containers:
  - name: db
    image: postgres:15
    volumeMounts:
      - mountPath: /var/lib/postgresql/data
        name: data-vol
```




----
# **Understanding Kubernetes Manifests (part 1) (pods/deployments) **

In Kubernetes the state of resources (the services, CNIs, pods, deployments, configurations, etc.) are defined using YAML files called manifests. We can create most of these resource objects imperatively (manually, via CLI), but using a declarative (representation of state) approach allows   for reproducibility, ease of modification/collaboration, and for versioning purposes. 

The three types of manifests that you will interact with most frequently are deployment, pod, and service manifests. these manifests can be complex and if you're like me and your brain is already full of dockerfile/compose, ansible, terraform, jenkins, etc. syntax you may have trouble remembering how to create these files off the cuff.....but dont worry! Kubernetes provides the ability to generate formatted YAML files for these resources that you can then modify to your specifications and then apply. We can do this by specifying a create command `create` for deployments & `run` for pods, and appending `-o yaml --dry-run=client > something.yaml` to the command to specify that we only want to generate the respective yaml and that we want it piped to a file in the pwd.

**Lets start by generating a pod & deployment manifest and then we will learn how to configure the files manually to meet our specifications. Services are another beast and we will hit these in the next section.**

- (1) **run this command to generate a pod manifest:** `kubectl run Mypod --image ubuntu:latest -o yaml --dry-run=client > mypod.yaml`
***It will generate mypod.yaml:***
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: Mypod
  name: Mypod
spec:
  containers:
  - image: ubuntu:latest
    name: Mypod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

- (2) **now run this command to generate a deployment manifest:** `kubectl create deployment MyDeployment --image ubuntu:latest --image mysql:latest -o yaml --dry-run=client > mydep.yaml`
***It will generate mydep.yaml:***
```yaml
#-----------------------top level section -------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: MyDeployment
  name: MyDeployment
  
  #---------------spec 1 section (deployment configs)-------------------
spec:
  replicas: 1
  selector:
    matchLabels:
      app: MyDeployment
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: MyDeployment

  #---------------spec 2 section (pod configs)-------------------
    spec:
      containers:
      - image: ubuntu:latest
        name: ubuntu
        resources: {}
      - image: mysql:latest
        name: mysql
        resources: {}
status: {}
```



**^Lets examine the essential sections for manifests and then go over the above generated manifests to learn about them / some additional syntax you can use to customize them.**
#### Top level section (object definition)
There are three required top level lines for manifests:
- **line 1** - `apiVersion` : Manifests always have a top level section that starts with this. It indicates the API version that the object belongs to, this can be different for each object, to determine what API owns what object you can view it with `kubectl api-resources`

-  **line 2** - `kind` : This second line will tell k8s what the target object for the manifest is (e.g. pod, deployment)

 - **line 3** - `metadata:` : This is a non-optional section that is configured to assign information like names, labels, namespaces, timestamps, etc. to the object, you must always include a name: 
```yaml
metadata:
  name: something  # must always include this at a minimum
```
to define the name of the object, all other configurations in this section (for deployment/pod manifests, there are other required values for different manifests) are optional but can be quite helpful.

#### lower level section(s) (specification)
The `spec` section defines what Kubernetes should build and how it should be built

***lets look at the pod manifest first:***
```yaml
apiVersion: v1
kind: Pod
...
  
spec:    # <-- We are targeting this section
  containers:
  - image: ubuntu:latest
    name: conatainer1
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
Here it defines that the pod should include a single container that uses the ubuntu:latest image and has the name 'container1', the containers section is the only required information for this section. 
For further customization It also specifies:
 - an empty resources section (this section can be used to define resource requests and limits for the container), 
 - a dns/restart policy for the pod 
 - and the status section set to {} which allows Kubernetes the ability to populate this field with runtime information about the Pod's state.


***Now lets look at the deployment manifest:***
```yaml
apiVersion: apps/v1
kind: Deployment
...

spec:    # <-- We are targeting this section from spec to spec
  replicas: 1
  selector:
    matchLabels:
      app: MyDeployment
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: MyDeployment
        
    spec:...
```

The first thing you'll notice is that there are two specification (`spec`) sections, and a template section. one spec section is for the deployment configuration and one is for the pod configuration.

> In the first spec section, the one that defines the deployment configuration we can see:
- `replicas: 1` : this determines how many replicas of the pod to run, each deployment can only run one pod but we can specify how many instances of this pod to run on the cluster with this config.
- `selector:...` : this section defines how the deployment select pods to manage, it does this by using labels that can match pods to the deployment... #fleshout
- `strategy` :  defines the pod update strategy, {} tells k8s to use the default strategy

> The template section defines the pod templates, you need to specify the labels you want matched here, This is a non-optional section.

> In the last spec section we can see a similar setup to the pod manifest spec section we went through above, here is where we set up the pod specifications so just like above you need container declarations and configurations.



#### Optional specifications:

**Pods (applies to pod spec section in both pod & deployment manifests)**
```yaml
...
kind: pod or deployment
...
    spec:
      containers:
      - image: ubuntu:latest
        name: ubuntu
#-----------------------
		imagePullPolicy: IfNotPresent # specifies the image pull policy, IfNotPresent means that the image will look for the image localy first and if it's not present it will then reach out and attempt to pull the image.
#-----------------------
        stdin: true  
        tty: true     #stdin + tty can be used like the -dit flag in docker to keep the container in the pod running detached in the background able to be entered into a terminal
#-----------------------
        command: ["/bin/bash", "-c", "while true; do echo hi; done"] # command is equivalent to the entrypoint command in a Dockerfile, sets the default executable for the container, can be used to supply additional args but this is best set in the args command (below) (equivalent to dockerfile CMD, see notes on dockerfiles to better understand.
        args: ["while true; do echo hi; done"]
#-----------------------
	    ports:
	    - containerPort: 80   # specifies a port for the container to expose
#-----------------------
	  restartPolicy: Never # specifies a restart policy for the container, this means that if it shuts down the container will not restart
		 
        
```
#fleshout_the_image_pull_policy_section / flesh out syntax e.g. manifest entries begin with a lowercase with each following word capitalized ( restartPolicy)

**deployments**










----
# **Understanding Kubernetes Manifests (part 2) (services) **










---
# **contexts/namespaces

information on top level cluster namespaces here like kube-system, default, add cni namespace info...









---

# **Understanding Kubernetes deployment structure / best practices **
Yes, it is common practice to deploy individual services as separate Kubernetes (K8s) deployments instead of bundling them into one. This approach aligns with the principles of microservices architecture and provides several benefits:

1. **Isolation and Resilience**: Each service runs independently, which improves fault isolation. If one service fails, it does not affect the others.
    
2. **Scalability**: Services can be scaled independently based on their own demand, optimizing resource usage.
    
3. **Flexibility**: Each service can be deployed, updated, and managed separately, allowing for more agile development and deployment cycles.
    
4. **Security**: Isolating services can enhance security by limiting the attack surface and applying security policies at a more granular level.
    
5. **Resource Management**: Resource requests and limits can be set per service, providing better control over resource allocation.
    

In Kubernetes, each service typically has its own Deployment, which manages a set of replicated Pods. This setup also allows the use of other Kubernetes features like ConfigMaps, Secrets, and Services to manage configuration and communication between services.

----


# **Tutorial: Performing a basic Kubernetes deployment with kubeadm**

Here we will go through initializing a cluster. in this example we will use two machines (one master & one slave) that both have kubeadm, kubelet, and kubectl installed (see install guide above). Here are the steps to initialize a cluster & create a simple deployment. To follow along you should use two hosts (one master one worker, preferably Linux). We will be utilizing the calico CNI for cluster networking.


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
**Don't export the admin.conf path. I find that it can make cluster management difficult/break, simply run init as sudo and make the directory.**
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
          stdin: true
          tty: true
          ports:
            - containerPort: 80
          command: 
            - /bin/bash
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


---


# **Installing Kubernetes (ADVANCED)

##### Installation instructions:
This tutorial will focus on Debian based distros. 



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


# **Kubernetes Services in depth**
By default when you create a deployment/pod it will not be accessible externally, since Kubernetes is essentially an isolated network, you need to add some "networking" capabilities to the deployed object to allow external access/networking configuration, this is where services come in.

The first thing to get aquianted with is the port schema that these services use / understanding traffic flow when implementing a service ...






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

# **Kubernetes Operators

#### **What it is**
K8s operators are a more advanced concept. To understand operators it is important to know that kubernetes treats everything as an API object. Operators in kubernetes are a way to leverage this architecture in order to create extensions that extend k8s inherent functionality to comprehensively manage custom applications. 

That's a lot. to break that down lets first learn/refresh a couple key terms:
1. **Custom Resource Definitions (CRDs):** Operators use CRDs to define the custom resources (CRs) that represent the desired state of the application. For instance, a database operator might define a CRD for a database instance. CRDs allow us the use YAML manifests to define states for our resources (AKA objects).
   
2. **Controller**: At the core of an operator is a controller, which is a control loop that watches the state of your application through the Kubernetes API and makes changes as needed to maintain the desired state. It responds to events and changes in resources.
	**NOTE:** controller is NOT the control plane. The Kubernetes Control Plane consists of several components (like the API server, etcd, controller manager, and scheduler) that manage the overall state of the cluster. While controllers are part of the control plane (specifically the controller manager), the term "controller" in the context of CRDs usually refers to the specific logic you implement to manage the lifecycle of your custom resources.

#### **Why operators**
"Kubernetes can manage and scale [stateless applications](https://www.redhat.com/en/topics/cloud-native-apps/stateful-vs-stateless), such as web apps, mobile backends, and API services, without requiring any additional knowledge about how these applications operate. The built-in features of Kubernetes are designed to easily handle these tasks.

However, stateful applications, like databases and monitoring systems, require additional domain-specific knowledge that Kubernetes doesn’t have. It needs this knowledge in order to scale, upgrade, and reconfigure these applications....The function of the operator pattern is to capture the intentions of how a human operator would manage a service. A human operator needs to have a complete understanding of how an app or service should work, how to deploy it, and how to fix any problems that may occur.

The site reliability engineer or operations team typically writes the software to manage an application, but an operator is designed to take human operational knowledge and encode it into software to manage and deploy Kubernetes workloads while eliminating manual tasks."[What is a Kubernetes operator? (redhat.com)](https://www.redhat.com/en/topics/containers/what-is-a-kubernetes-operator#:~:text=A%20Kubernetes%20operator%20is%20an%20application-specific%20controller)

#### **How we craft operators**
"The Operator Framework is an open source project that provides developer and runtime Kubernetes tools, enabling you to accelerate the development of an operator.

The Operator Framework includes:

- **Operator SDK:** Enables developers to build operators based on their expertise without requiring knowledge of Kubernetes API complexities.
- **Operator Lifecycle Management:** Oversees installation, updates, and management of the lifecycle of all of the operators running across a Kubernetes cluster.
- **Operator Metering:** Enables usage reporting for operators that provide specialized services."


Operators always need a CRD and a controller component to work properly, these are written in GO.

For example we will use the operator sdk to create a template for the CRD (like running -o yaml --dry-run=client) and the controller comopnent but then we must define them further to tailor them to our application, example process:



Let's create a simple Kubernetes Operator using the **Operator SDK**. This example will illustrate a hypothetical `MyApp` application with a CRD.

**Step 1: Install Operator SDK**

You need to have the Operator SDK installed. You can follow the official [Operator SDK installation guide](https://sdk.operatorframework.io/docs/install-operator-sdk/) for your platform.

**Step 2: Create a New Operator**

```bash
operator-sdk init --domain=myapp.example.com --repo=github.com/example/myapp-operator
```

**Step 3: Create a CRD**

```bash
operator-sdk create api --group=app --version=v1 --kind=MyApp --resource --controller
```

This command generates the CRD and the controller for you.

**Step 4: Define the CRD**

In `api/v1/myapp_types.go`, define your custom resource schema:

```go
type MyAppSpec struct {
    Replicas int32 `json:"replicas"`
    Version  string `json:"version"`
}

type MyAppStatus struct {
    AvailableReplicas int32 `json:"availableReplicas"`
}
```

**Step 5: Implement the Controller Logic**

In `controllers/myapp_controller.go`, implement the reconciliation logic to handle the desired state:

```go
func (r *MyAppReconciler) Reconcile(ctx context.Context, req ctrl.Request) (ctrl.Result, error) {
    var myApp appv1.MyApp
    if err := r.Get(ctx, req.NamespacedName, &myApp); err != nil {
        return ctrl.Result{}, client.IgnoreNotFound(err)
    }

    // Your logic to manage the MyApp resource
    // Example: Ensure a Deployment is created/updated
    deployment := &appsv1.Deployment{
        ObjectMeta: metav1.ObjectMeta{
            Name:      myApp.Name,
            Namespace: myApp.Namespace,
        },
        Spec: appsv1.DeploymentSpec{
            Replicas: &myApp.Spec.Replicas,
            Selector: &metav1.LabelSelector{
                MatchLabels: map[string]string{"app": myApp.Name},
            },
            Template: corev1.PodTemplateSpec{
                ObjectMeta: metav1.ObjectMeta{
                    Labels: map[string]string{"app": myApp.Name},
                },
                Spec: corev1.PodSpec{
                    Containers: []corev1.Container{
                        {
                            Name:  "myapp",
                            Image: "myapp-image:" + myApp.Spec.Version,
                        },
                    },
                },
            },
        },
    }

    // Set MyApp instance as the owner and controller
    ctrl.SetControllerReference(&myApp, deployment, r.Scheme)

    // Create or Update the Deployment
    err := r.Client.Create(ctx, deployment)
    if err != nil && !errors.IsAlreadyExists(err) {
        return ctrl.Result{}, err
    }

    // Optionally, update status
    myApp.Status.AvailableReplicas = deployment.Status.AvailableReplicas
    r.Status().Update(ctx, &myApp)

    return ctrl.Result{}, nil
}
```

**Step 6: Deploy the Operator**

Use `make deploy` to build and deploy your operator to a Kubernetes cluster.

**Step 7: Create a Custom Resource**

You can create a custom resource based on your CRD:

```yaml
apiVersion: app.example.com/v1
kind: MyApp
metadata:
  name: myapp-sample
spec:
  replicas: 3
  version: "1.0"
```

Apply this to your cluster:

```bash
kubectl apply -f myapp-sample.yaml
```






----

# **Kubernetes Networking basics**

1










dive into flannel calico and weave / --pod-networ-cidr stuff like that
----

# **Advanced Kubernetes Topics**

1. **Horizontal Pod Autoscaler (HPA):** Automatically adjusts the number of replica pods in a deployment based on CPU or custom metrics.
   
2. **Ingress Controller:** Manages external access to services within a Kubernetes cluster, typically via HTTP or HTTPS.

3. **StatefulSets:** Manages the deployment and scaling of stateful applications, such as databases, with stable network identifiers and persistent storage.

----
# **AWS K8s (AWS EKS)**

1. **Horizontal Pod Autoscaler (HPA):** Automatically adjusts the number of replica pods in a deployment based on CPU or custom metrics.
   
2. **Ingress Controller:** Manages external access to services within a Kubernetes cluster, typically via HTTP or HTTPS.

3. **Persistent Volumes:** Storage resources provisioned independently of pod lifecycles, allowing data to persist across pod restarts.

4. **StatefulSets:** Manages the deployment and scaling of stateful applications, such as databases, with stable network identifiers and persistent storage.
https://k21academy.com/docker-kubernetes/amazon-eks-kubernetes-on-aws/#b
----

#### Additional Resources

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)
- [Kubernetes Tutorials and Examples](https://kubernetes.io/docs/tutorials/)
- [Kubernetes GitHub Repository](https://github.com/kubernetes/kubernetes)
  
  






  




---
---
---

  
# **Helm Notes**
## what is Helm?

In systems we often use packages, a "package" refers to a bundle of binaries/libraries/data and related resources that can be easily installed (often with a single command), managed, and used on a system.

Helm is a package manager tool used in Kubernetes. It operates similar to how you use a package manager like npm for JavaScript or apt for Debian-based Linux distributions.

With Helm, you can:
- Package **Kubernetes manifests** into reusable **charts**
    
- It adds **templating** so you can define variables (e.g., image tag, replicas)
    
- Easily perform **versioning, upgrades, and rollbacks**
    
- provide support for **dependency charts** (e.g., install Redis alongside your app)



> ✅ **Helm is (almost) to Kubernetes what Docker Compose is to Docker. This is a good way to begin your understanding**

| Concept                    | **Docker Compose**                                       | **Helm**                                              |
| -------------------------- | -------------------------------------------------------- | ----------------------------------------------------- |
| Definition                 | CLI + YAML tool for managing multi-container Docker apps | CLI + packaging system for Kubernetes apps            |
| File format                | `docker-compose.yml`                                     | `Chart.yaml` + `values.yaml` + templates              |
| Bundles multiple services? | ✅ Yes (e.g., web + db + cache)                           | ✅ Yes (e.g., Deployments, Services, ConfigMaps, etc.) |
| Declarative config         | ✅                                                        | ✅ (with template logic)                               |
| Parametrizable?            | Limited (`.env` or override file)                        | ✅ Highly — uses Go templating and values              |


---

## Installing Helm:
Helm can be installed easily from its binary run: `curl [desired release] -o [nameOfRelease] ; tar -zxvf [nameOfRelease] ; mv linux-amd64/helm /usr/local/bin/helm` 
get name of release here: https://github.com/helm/helm/releases
install docs here: https://helm.sh/docs/intro/install/


## Why Use Helm If You Can Bundle Everything in One Manifest?
you **can** technically bundle everything into a single YAML file — and for simple apps or dev use, that’s fine. But in **real-world production environments**, Helm solves many issues that single-file manifests can’t handle well:


| Problem With Single Manifest                  | How Helm Helps                                   |
| --------------------------------------------- | ------------------------------------------------ |
| **Hardcoding configs (image tags, replicas)** | Helm uses `values.yaml` to template and override |
| **No versioning or rollback**                 | Helm tracks releases and supports rollback       |
| **No dependency handling**                    | Helm can include subcharts (e.g., Redis)         |
| **Hard to upgrade cleanly**                   | Helm supports `upgrade` without downtime         |
| **Difficult to share or reuse**               | Charts can be published and reused               |
| **No lifecycle management**                   | Helm supports `install`, `uninstall`, `status`   |
| **Hard to organize large projects**           | Helm uses a structured directory layout          |
