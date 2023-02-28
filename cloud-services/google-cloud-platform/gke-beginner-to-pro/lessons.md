# Google Kubernetes Engine (GKE): Beginner to Pro

## Intro to Containers
### In the beginning was the server
#### Deploying applications on physical servers
- Get financial approval, file a purchase order
- Take delivery, unbox it, dispose of the packaging
- Lift and rack the server - make sure you have the right cage nuts!
- Cable up and power network
- Install the operating system
- Install the application and dependencies

#### As the application grows
- Buy more servers
- Repeat all the previous steps 
- Lead time is in the order of months
- Large capital expenditure

#### When things go wrong?
- Hard disks fail
- System boards short out
- High availability
- Off-peak utilization = wasted resources

#### Virtualization
- Made popular by VMWare, Hyper-V and KVM
- Abstract the hardware away
- Quickly deploy virtual machines
- VMs are isolated and can be portable
- Better hardware utilization

#### Virtual problems
- Single operating system, libraries and dependencies per VM
- Difficulty separating applications
- Hardware utilization suffers
- No automation problems solved
- Complex configuration management systems

### Containers to the rescue
With containers the operating system is abstracted away

#### Benefits of using containers
- Write once, run anywhere
- Easily deploy to dev, test, production
- Easily deploy to GKE, VMs, bare metal
- Packaged applications speed development
- Make changes fast
- Promote microservices

#### Docker
- Docker created a fantastic toolset around Linux containerization features
- Developer-friendly container creation and deployment
- Tools for handling container images and registries
- Swarm - Docker's own orchestration system
- Runtime and image specificatiosn managed by the Open Container Initiative

#### Dockerfile
- Simple text configuration file
- Use existing images as buildiing blocks
- Add files, run commands, and perform other actions
- Specifiy what should be running when the container runs

#### Basic Dockerfile
<pre><code>
FROM ubuntu:18:04
COPY . /app
RUN make /app
CMD python /app/app.py 
</code></pre>

- Inherit from an Ubuntu image
- Copy everything in local directory to /app in the image
- Run make to compile our application
- When the container runs, execute the application
- file name is "Dockerfile"

#### Building the image
<pre><code>
docker build -t myapp .
</code></pre>

#### Running containers
<pre><code>
docker ps
docker stop container-name
docker logs container-name
docker rm

docker images
docker tag myapp gcr.io/myapp
docker push gcr.io/myapp
docker rmi
</code></pre>

- View running and stopped containers
- Stop, view logs, or delete containers

- view local images
- Tag and push to remote registries
- Remove local images

#### Containers in a nutshell
- An efficient, developer-friendly way of packaging an application
- Guaranteed consistency - write once, run everywhere
- Agile creation and deployment
- Isolated, elastic microservices

#### What are containers good for?
- Stateless microservices
- Frontends
- Backends
- Applications that need horizontal elasticity

#### What are containers bad for?
- Applications that write to local disk
- Non-scaling resource - intensive monoliths
- Applications that require manual intervention to install
- Databases (unless you know what you're doing)

## Intro to GKE
Observing logs with kubectl or though the Cloud Console may help you diagnose a problem with your Pod.  You can also connect to a running shell inside a Pod to help you troubleshoot any issues.

### The Anatomy of a cluster
#### A Kubernetes Cluster
- One or more masters
- One or more nodes
- Master components provide the control plane
- they make decisions (eg. about scheduling)
- Node components provide the runtime environment
- they are the resources of the cluster


#### Kubernetes Master
- API-Server 
  - Frontend of the control plane.  It exposes the api to all the master functions.  Everytime you communicate with the master is through the API

- etcd 
  - Kubernetes database.  Holding all the configurations and state.  Key-Value store

- Scheduler 
  - Responsible for scheduling workloads on nodes.

- Cloud Controller Manager 
  - Allows Kubernetes to work with cloud platforms.  Handles Networking and Load Balancing

- Kube Controller Manager 
  - Handles controllers in the cluster

#### Kubernetes Node
- kubelet
  - Agent for Kubernetes. Communicates with the control plane and takes instructions such as deploying containers when told to. 
- kube-proxy
  - Responsible for handling network connections in and out of the node
- Container Runtime
  - The node will run docker as a container runtime

#### How to build a Kubernetes cluster
- The Hard Way
  - Provision virtual machines for your master and nodes
  - Install Kubernetes, create a network overlay, set up CA
- The Easy Way
  - Use GKE

Google Kubernetes Engine provisions and manages the underlying cloud resources automatically.

#### [Learn More on GKE](https://cloud.google.com/kubernetes-engine)

#### GKE: Fully Managed Kubernetes
The cluster master runs the Kubernetes control plane processes, including the Kubernetes API server, scheduler, and core resource controllers.  This is set up and run for you by the GKE managed service when you create a new cluster.
- Creates Masters and Nodes for you
- Uses a Container-Optimized operating system
- Built-in automatic upgrades and repairs
- Resource control and cluster scaling
- Integrated with other GCP products and services

You don't even touch the master control plane.  It's all managed by Google

#### Interacting with GKE
- Google Cloud Console
- Command line
- kubectl
  - Create and manage the lifecycle of Kubernetes objects
  - Communicates with the API Server

### Forget containers, meet Pods!
Pods are the smallest, most basic deployable objects in Kubernetes and GKE.  Even if you deploy a container to GKE directly from Google Container Registry, it must exist inside a Pod in order to be scheduled and run.
Pods can contain more than one container. share local environment.  non app containers are sidecars

In Kubernetes, a Pod is the smallest deployable unit that represents a single instance of a running process in a cluster. A Pod encapsulates one or more containers, storage resources, and a unique network IP, providing a runtime environment for running a specific set of containers.

A Pod is designed to run a single instance of a specific application or process, which can be composed of one or more tightly coupled containers that share the same network namespace, IPC namespace, and host name. This means that all the containers within a Pod share the same IP address and port space, making it easy for them to communicate with each other using local inter-process communication (IPC).

Pods are generally managed by a higher-level Kubernetes object called a controller, such as a Deployment or a StatefulSet. The controller is responsible for ensuring that the desired number of replicas of the Pod is running, and it can handle tasks such as scaling, rolling updates, and self-healing to ensure that the application is highly available and resilient.

Pods provide a flexible and powerful abstraction for managing containerized applications in Kubernetes. By using Pods, you can create, update, and scale your application in a consistent and declarative manner, while also providing a secure and isolated runtime environment for your containers.


#### Pods are Kubernetes objects
- A logical application-centric unit for hosting containers
- Pods contain 1 or more tightly-coupled containers
- Example design patterns for pods:
  - A monolith and a web server
  - An application and a database proxy
  - An application and an adapter to process its output
- Pods should be designed around a single application

#### Creating a Pod
- Create objects dynamically with kubectl
- Create objects declaratively
- Create specification files in YAML
- Apply the declaritive configuration
- Kubernetes attempts to match the live state to the declared state.

### Example pod specification file
<pre><code>
apiVersion: v1
kind: Pod
metadata:
  name: myapp
spec:
  containers:
  - name: myapp
    image: myapp
  - name: nginx-ssl
    image: nginx
</code></pre>
Once the definition file is created tell Kubernetes to create it
<pre><code>
kubectl apply -f my-file.yaml
</code></pre>

## ReplicaSets and Deployments
### ReplicaSets
Deployments allow you to perform rolling updates, creating a ReplicaSet of Pods with the new container image.  Old Pods are not removed until a sufficient number of new Pods are Running, and new Pods are not created until a sufficient number of old Pods have been removed.
- A Pod is a logical application-centric unit of containers
- A ReplicaSet is a Kubernetes object that provides;
  - A stable set of Pod replicas
  - A specified number of Pods
  - Logic to ensure availability
- Not recommended to create ReplicaSets on their own: for that we use a different tool

### Deployments
A Kubernetes Deployment manages a set of identical pods, ensuring that the desired number of replicas is running at all times. It allows you to define the desired state of your application and roll out updates or rollbacks to the application in a controlled manner. The Deployment also provides features such as scaling, rolling updates, and self-healing capabilities to ensure that your application is highly available and resilient to failures.
- An object for logically managing Pods and ReplicaSets
- Desired state is enforced by the Controller
- Logic for updating, rolling back and scaling deployments
- Proportional scaling and error checking for rollouts
- The preferred object for deploying compute workloads


Increasing the number of replicas in the Deployment spec before the Pod spec will add more pods to the current Replicaset.  You can also use the kubectl scale command.

#### Creating a deployment with YAML
<pre><code>
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    containers:
    - name: nginx
      image: nginx:1.7.9
      ports:
      - containerPort: 80
</code></pre>
If a change is made to pod specifications a new ReplicaSet is created and the old one is removed

### Accessing Applications

#### Services
A Kubernetes Service provides a stable IP address and DNS name for a set of pods, allowing other parts of your application to connect to them. A Service defines a logical set of pods and the policies to access them. It enables load balancing and service discovery for the pods behind it, providing a way to access the application from outside the cluster or between different services within the cluster.
- A service exposes a set of Pods to the network
- A service assigns a fixed IP to you Pod replicas
- Three main types of Service
  - ClusterIP
  - NodePort
  - LoadBalancer

#### Selectors
A service identifies its member Pods with a selector.  For a Pod to be a member of the Service, the Pod must have all the labels specified in the selector. A label is an arbitrary key/value pair that is attached to an object.
- Labels are key-value pairs in object metadata
- Selectors search for groups of labels
  - app-nginx
- Pods that match selectors become part of the Service

#### Service YAML
<pre><code>
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector: nginx
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
</code></pre>
Add selector specifications to, for example separate test and prod 

## Deploying Applications


### Stateless application example
Frontend Deployment

LoadBalancer Service

### ClusterIP Service
Redis Master Deployment

Redis Slave Deployment

### Pod reliability with health checks
#### Liveness Probes
- A check performed by kubelet
- Built-in monitoring
- Can check HTTP endpoint, TCP socket, or run a command
- A failed probe will cause a Pod to be replaced

#### A liveness probe in the YAML
<pre><code>
apiVersion: v1
kind: Pod
metadata:
  name: liveness-test
spec:
  containers:
  - name: liveness-test
    image: k8s.gcr.io/liveness
    args:
    - /server
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 3
</code></pre>

#### Readiness Probes
A pod with containers reporting that they are not ready does not receive traffic through Kubernetes Services.  A custom Readiness Probe can be useful when a container needs to perform additional actions after startup.
- A similar check performed by kubelet
- Define when a Pod is ready to start serving
- Buys time for Pods that must perform startup tasks
- Traffic won't be directed to a Pod until it is ready
- Use in conjunction with a Liveness Probe

#### A Readiness Probe added to the previous YAML
<pre><code>
apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  containers:
  - name: goproxy
    image: k8s.gcr.io/goproxy:0.1
    ports:
    - containerPort: 8080
    readinessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 30
    livenessProbe:
      tcpSocket:
        port:8080
      periodSeconds: 10
</code></pre>
    
#### Probe Configuration
- Probes are performed by a handler
  - ExecAction
  - TCPSocketAction
  - HTTPGetAction
- initialDelaySeconds, periodSeconds, timeoutSeconds
- successThreshold, failureThreshold

All pods start in a pending state
when all containers are up the change to running

if a pod doesn't start successfully it reaches a failed state
if the scheduler can't determine state it could have lost communication to the pod.

### Accessing External Services
Easiest way to reach services outside pods
#### Service Endpoints
- Create a service object with no Selector
- Kubernetes looks for an Endpoint with the same name
- Endpoints map exernal services to Service Objects
- Service discovery via internal DNS

#### Service object definition no selector
<pre><code>
apiVersion: v1
kind: Service
  metadata: mongo
spec:
  type: ClusterIP
  ports:
  - port: 27017
    targetPort: 27017
</code></pre>

#### Endpoint object definition
<pre><code>
apiVersion: v1
kind: Endpoints
metadata:
  name: mongo
subsets:
  - addresses:
    - ip: 10.240.0.4
    ports:
    - port: 27017
</code></pre>

- Services can also map to external FQDNs
- use ExternalName type
- No Endpoints required

Example
<pre><code>
apiVersion: v1
kind: Service
  metadata: mongo
spec:
  type: ExternalName
  externalName: db12.mymdb.com
</code></pre>

#### Multiple endpoints
<pre><code>
apiVersion: v1
kind: Endpoints
metadata: 
  name: rabbitmq
subsets: 
  - addresses:
    - ip: 10.245.0.1
    ports:
    - port: 31000
  - addresses:
    - ip: 10.245.0.2
    ports:
    - port: 31000
</code></pre>

- Endpoints can map to multiple IPs
- The Service will round robin to each IP

#### Sidecars
- Sidecar provies the connection to the external service
- Application container accesses the service on localhost / 127.0.0.1
- Useful for proxies, such as MYSQL Proxy for Cloud SQL

### Volumes and Persistent Storage
#### Volumes
- Container storage is ephemeral
- A volume is an independent object
- A directory mounted in a container to access files
- Many different types - some of which are persistent

#### Common volume types
- emptyDir
  - Scratch-space that can be shared by multiple containers in the same Pod.  Deleted forever when a Pod is removed from a node
- gcePersistentDisk
  - A GCP persistent disk.  Must be created beforehand, can be prepopulated with data, and mounted read-only by multiple consumers.  Will only be unmounted when a Pod is removed.
- PersistentVolumeclaim
  - Used to mount PersistentVolume into a Pod - a way to "claim" durable storage. Requires a matching PersistentVolume object

#### Persistent Volumes
PersistentVolume resources are used to manage durable storage in a cluster.  On GKE, PersistentVolumes are typically backed by Compute Engine persistent disks.  but most of the time, you don't need to directly configure PersistentVolume objects or create Compute Engine persistent disks.  Instead, you can create a PersistentVolumeclaim and Kubernetes automatically provisions a persistent disk for you. 
- PersistentVolume defines a piece of storage in the cluster
- Manually or dynamically provisioned using plugins
- Configured with a Storage Class
- PersistentVolumeClaim is a request to consume a PersistentVolume
- Access Modes define how a volume may be accessed by multiple consumers.

#### Sample YAML for PersistentVolume
<pre><code>
apiVersion: v1
kind: PersistentVolume
metadata:
  name: myapp-pv
spec:
  storageClassName: ""
  capacity:
    storage: 10G
  accessModes:
    - ReadOnlyMany
  gcePersistentDisk:
    pdName: myapp-disk
    fsType: ext4
</code></pre>
- Manually define a PersistentVolume using an existing GCP persistent disk
- Persistent disk must contain a filesystem

To consume a volume create a PersistentVolumeClaim

<pre><code>
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myapp-pvc
spec:
  accessModes:
    - ReadOnlyMany
  resources:
    requests:
      storage: 10G
</code></pre>

- Manually define a PersistentVolumeClaim
- Kubernetes matches the claim to an available PersistentVolume object

Once the claim is made nothing else can claim the PersistentVolume Object

Alternatively - make a claim without creating a volume beforehand

GKE dynamically creates a disk and volume using the Storage Class provisioner

<pre><code>
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myapp-dynamic
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 30Gi
</code></pre>

Consume the volume claim when defining volumes in a Pod spec
<pre><code>
....
volumes
- name: myapp-vol
  persistentVolumeClaim:
    claimName: myapp-dynamic
    readOnly: true
</code></pre>

Optionally we can define different storage classes
- GKE default storage class uses standard persistent disks
- Custom Storage class objects change the parameters

<pre><code>
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gcp-ssd
provisioner: kubernetes.io/gce-pod
parameters:
  type: pd-ssd
  replication-type: none
</code></pre>

#### Access Modes
- ReadWriteOnce: a single node can mount the volume read/write
- ReadOnlyMany: any node can mount the volume read-only
- ReadWriteMany: any node can mount the volume read/write
  - Note: This mode is not supported by GCP persistent disks

#### Constraints
- Deployments are designed for stateless applications
- All replicas will share the same PersistentVolumeClaim
- Volume mode must be ReadOnlyMany
- Deployment strategy may cause volume deadlocks

#### Alternatives
- Abstract away your data!
- Object storage (GCS) and managed databases
- Separate file service in GKE or GCE
- Cloud Filestore

### ConfigMaps and Secrets
#### Secrets 
- Designed to obfuscate the storage of sensitive data
- Consumed as environment variables or volumes
- Secrets are encoded only - not encrypted by default
- Secrets can be encrypted with Cloud KMS (beta?)
- Can also use something like Hashicorp Vault

#### ConfigMaps
ConfigMaps allow you to decouple configuration artifacts from image content to keep containerized applications portable.  ConfigMaps can be mounted as files, directories of files, or applied directly to container environment variables.
- Decouple configuration from image content
- Created from files, directories or literal key-value pairs
- Values can be referenced as environment variables
- ConfigMap data may also be mounted as a Volume
  - allows containers to access configuration files without changing the underlying image

Basic ConfigMaps can be defined in YAML
<pre><code>
apiVersion: v1
kind: ConfigMap
metadata: 
  name: myconfig
data:
  make: ford
  model: prefect
  color: blue
</code></pre>

Keys and values can be referenced and passed to environment variables inside the container
Use individual keys to assign values
Specify the Config map and key to retrieve tha value

<pre><code>
....
containers:
  - name: my-container
    image: my-image
    env:
      - name: CAR_MAKE
        valueFrom:
          configMapKeyRef:
            name: myconfig
            key: make
      - name: CAR_MODEL
        valueFrom:
          configMapKeyRef:
            name: myconfig
            key: model
</code></pre>

Alternatively map all keys and values as environment variables inside a container.
For convention, key names should be capitalized

<pre><code>
...
containers:
  - name: my-container
    image: my-image
    envFrom:
    - configMapRef:
      name: myconfig
</code></pre>

ConfigMaps can also store standard configuration files
In YAML everything after the pipe | symbol is treated as a string
<pre><code>
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-nginx-conf
data:
  nginx.conf: |
    user nginx;
    worker_preocesses 3;
    server {
      listen 80
      server name _;
      location / {
        etc. etc
      }
    }
</code></pre>

ConfigMaps are then used as Volume objects
They are mounted in the container filesystem in the same way as any other Volume.
<pre><code>
...
containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: nginx-config
      mountPath: /etc/nginx
volumes:
  - name: nginx-config
    configMap:
      name: my-nginx-config
</code></pre>

### Deployment Patterns
#### Stateless Application Updates
- Rolling updates
- Canary Deployments
- Blue-Green Deployments

#### Rolling updates
The RollingUpdate strategy allows you to confidently roll out a new version of an applications.  Defining a threshold ofr the maximum number of unavailable Pods will stall a rollout if new Pods do not become ready within a certain time, potentially catching any issues with the application update.  A surge policy will allow slightly more Pods to be running than normal, so that the new rollout can be attempted without removing all the existing Pods.  Manual Pod management is never required, and failing to apply the updated manifest to the cluster will result in no changes at all.


- Gradually replace Pods with an updated spec
- Control how many additional Pods may be created at a time
- Specify threshold for failed Pods

#### Canary Deployments
The Canary deployment pattern creates a new Deployment for your updated application with a smaller number of Pods (usually 5-10 percent of the total), but a single Service may direct traffic to either Deployment.  Canary deployments can also be automated with a 3rd party tool called Spinnaker.
- Combines multiple Deployments with a single Service
- Deploy updates to a small subset of traffic
- Testing with production traffic

#### Canary deployments 
Prod version with 90 replicas
<pre><code>
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-prod
spec:
  replicas: 90
  template:
    metadata:
      name: frontend
      labels:
        app: frontend
        env: prod
    spec:
      containers:
      - name: frontend
        image: frontend:v1
</code></pre>
Canary version with 10 replicas
<pre><code>
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-canary
spec:
  replicas: 10
  template:
    metadata:
      name: frontend
      labels:
        app: frontend
        env: canary
    spec:
      containers:
      - name: frontend
        image: frontend:v2
</code></pre>

#### Blue-Green Deployments
The Blue/Green deployment pattern maintains 2 separate Deployments at all times.  You can update either and switch a Service between them if there is a problem with an update and you wiszh to revert to the "last good" state. Blue/Green, Red/Black and A/B deployments all mean the same thing.
- Maintain two versions of your application deployment
- Switch traffic from blue to green with Service selector
- All traffic immediately sent to new deployment
- Switch back in the event of problems

### Autoscaling all the things
#### Automatically scaling deployments
- Horizontally scales Pods to increase capacity when demand is high
  - HorizontalPodAutoscaler
- Vertically scale Pods to increase resources assigned to individual Pods
  - Vertical Pod Autoscaler
- Scale cluster nodes when demand is so high that extra VMs are required
  - Node-pool cluster autoscaling in GKE

#### Horizontal Pod Autoscaler
- Auto-scales the number of Pod replicas in a ReplicaSet/Deployment
- cPU and Memory thresholds are observed by GKE to trigger scaling
- Custom, multiple and external metrics also supported
- Stackdriver metrics can also be used

A HorizontalPodAutoscaler object can scale the number of Pods available in a ReplicaSet automatically, up or down, based on load or custom metrics.  In addition, the GKE cluster autoscaler can add GKE nodes when demand is very high and more nodes are required to scale the extra Pods.  this is best used with specific nodpools, tailored to the type of workload in the deployment.

#### HPA YAML example
<pre><code>
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx
  minReplicas: 3
  maxReplicas: 6
  targetCPUUtilizationPercentage: 60
</code></pre>

#### Vertical Pod Autscaler
- Automatically recommends or applies CPU and RAM requests per Pods
- More suited for stateful deployments
- Cannot work alongsie Horizontal Pod Autoscaler
- Currently in Beta in GKE

#### VPA YAML example
when pod changes are made the pods are restarted
<pre><code>
apiVersion: autoscaling.k8s.io/v1beta2
kind: VerticalPodAutoscaler
metadata:
  name: myapp-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp-deployment
  updatePolicy:
    updateMode: "auto"
</code></pre>

#### Cluster Autoscaler
- Automatically grow or shrink your GKE cluster based on demand
- New nodes added when Pods don't have enough capacity to run
- Underutilized nodes automatically deleted
- Works best with Node Pools
- Group VM nodes of a specific type together - eg. High CPU or High RAM

#### Best Practices
- Design node pools around the requirements of specific workloads
- Enable cluster autoscaling and horizontal Pod autoscaling
- For example:
  - Run CI/CD, ancillary services on a single fixed node pool
  - Run web frontend workloads on a generic autoscaling node pool
  - run RAM intensive Java applications in a high RAM instance-type node pool

You can add additional custom node pools to a GKE cluster tailored to the specific requirements of a workload, then target that workload Deployment to a specific node pool. For example, an application that consumes a lot of CPU but an average amount of RAM can be deployed to a node pool running high CPU instances.



## Advanced GKE Operations
### Helm: The Kubernetes Package Manager
#### What is Helm
- Packages Kubernetes object manifests and configuration in a Helm chart
- Maintains the lifecycle of a deployment to GKE
- Installs, updates and can delete applications
- Public repos of helm charts for popular software
- Other manaifest-management solutions are available such as Kustomize

#### Using Helm
- Install the helm tool 
- Search for software in Artifact Hub
- Add the necessary Helm repository
  - helm repo add bitnami https://charts.bitnami.com/bitnami
- Install the helm chart
  - helm install my-wordpress bitnami/wordpress


#### Example Helm Chart
- Chart metadata, including authors and sources, as well as dependencies
- Customizable configuration specific to this deployment, such as image versions, resource requests and ingress options
- Schema definition for values.yaml
- Kubernetes YAML manifest, templated with Go, allowing for the dynamic use of configuration values
<pre><code>
wordpress/
  Chart.yaml
  LICENSE
  README.md
  values.yaml
  values.schema.json
  templates/
</code></pre>

When you run the helm install command
<pre><code>
helm install my-wordpress bitnami/wordpress -f values.yaml
</code></pre>
Helm uses the chart and merge it with local values.yaml
Will then connect with the api to create the necessary objects
A deployed Helm chart is called a releas
Query the release status with 
<pre><code>
helm status my_release_name
</code></pre>

Helm can delete released it creates
Cleanly delete all the objects that were deployed in the release 
NOTE: Sometimes system volumes can be left over.
<pre><code>
helm delete my_release_name
</code></pre>

### Advance Ingress Control
#### Ingress
- A more customizable way to expose traffic than a cloud load balancer
- An Ingress object configures access to services from outside the cluster
- Designed for HTTP and HTTPS services
- Can provide SSL, name-based, and path-based routing

Once we've designed Ingress resources, set up the Ingress Controller

#### Ingress Controller
- Routes traffic to services based on Ingress definitions
- Usually frontend by a cloud load balancer
- Consolidates your routing through a single resource
- NGINX Ingress Controller deployment is very common
- Other ingress controllers are avaiable

#### NGINX Ingress Controller
- Easily installed via Helm
- Run as a Deployment or DaemonSet
- Supports autoscaling
- Supported by the Kubernetes Project

### Highly Available Clusters and Workloads

#### Regional Persistent Storage
- GCP persistent disks are zonal resources
- Stateful Deployments can schedula a Pod and disk in the same zone
- GCP regional persistent disks replicate across two zones
- Use nodeAffinity and nodeSelectorTerms
- Schedule Pods and disks in the same zone

#### NodeAffinity
Kubernetes NodeAffinity is a feature that allows you to constrain the scheduling of a Pod to a subset of nodes based on labels that are attached to the nodes. This means that you can specify rules that determine which nodes a Pod can be scheduled on, based on attributes such as hardware capabilities, geographic location, or other custom properties.

NodeAffinity is defined using a set of rules that are applied to the labels of nodes in your cluster. These rules can be expressed in terms of:

- RequiredDuringSchedulingIgnoredDuringExecution: These rules indicate that a Pod can only be scheduled on nodes that satisfy the specified labels. If no nodes satisfy the labels, the Pod will remain unscheduled.

- PreferredDuringSchedulingIgnoredDuringExecution: These rules indicate that a Pod should be scheduled on nodes that satisfy the specified labels, but if no nodes satisfy the labels, the Pod can still be scheduled on other nodes.

NodeAffinity can be used in conjunction with other scheduling features, such as PodAffinity and PodAntiAffinity, to create complex scheduling rules for your Kubernetes cluster. With NodeAffinity, you can ensure that your workloads are running on the most appropriate nodes, which can improve performance and reduce resource contention.


#### nodeAffinity Yaml
<pre><code>
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoreDuringException:
    nodeSelectorTerms:
      - matchExpressions:
        - key: "failure-domain.beta-kubernetes.io/zone"
          operator: In
          values: ["us-east1-b", "us-east1-c"]
</code></pre>

#### Multi-cluster Ingress
- Replicate application deployments across GKE clusters
- Distrubute workloads to reduce blast radius
- kubemci creates a Global Google Cloud Load Balancer
- Direct traffic to worldwide regions based on lowest latency
- Currently in beta with no SLA

### Running a secure GKE Cluster
#### Cloud Native Security
- Cloud
  - VPC and IAM configuration, other GCP resources
- Cluster
  - Securing your GKE cluster
- Container and code
  - Trusted container images, secure code, versions and updates

#### Securing your cluster
- Role-based access control(RBAC)
- Namespaces and resource restrictions
- Pod security policies
- Network policies
- Workload identity

#### Role-Based Access Control
- Granular method of regulating access to cluster resources
- Can be applied at a namespace or cluster level
- Grantas a set of actions to specific API groups and resources
- Can be used with Pod service accounts

#### RBAC example in YAML
- Grants get and list verbs on the pods and pods/log resources
- No API group restrictions specified
- Only applied to the default namespace
<pre><code>
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-and-pod-logs-reader
rules:
- apiGroups: [""]
  resources: ["pods, "pods/logs"]
  verbs: ["get", "list"]
</code></pre>

#### ClusterRole example in YAML
- Grants get, list and watch verbs on the services, endpoints and pods resources
- No API Group restrictions specified
- A ClusterRole applies across all namespaces

<pre><code>
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: monitoring-endpoints
rules:
- apiGroups: [""]
  resources: ["services", "endpoints", "pods"]
  verbs: ["get", "list", "watch"]
</code></pre>

#### Namespaces
- Virtual clusters used to isolate resources for multiple teams or projects
- Can divide cluster resources with resource quotas
- default and kube-system
- A scope of resource names
  - When using the clusters internal DNS will notice the dns name 
    - myservice.mynamespace.svc.cluster.local

#### Defining a Namespace in a YAML
- Reference the namespace in object metadata
<pre><code>
apiVersion: v1
kind: Namespace
metadata:
  name: team-rocket
</code></pre>

When working with multiple namespaces remember to specify namespace working with

<pre><code>
kubectl apply mypod.yaml
kubectl -n team-rocket get pods
kubectl -n kube-system get pods
</code></pre>

#### Pod Security Policies
- Set of controls that locks down security-sensitive aspects of a Pod specifcation
- Policies define conditions that a Pod spec must meet in order to be scheduled
- Enforced by the optional admission controller
- Requires the creation of a Role or ClusterRole with permission to use the policy
- Requires a user or service account to be bound to the Role
- By default, Pods will not be scheduled if you enable the administion controller and don't take the extra role / account steps 

#### Basic example of security policy YAML
- Prohibits the scheduling of Pods that contain privileged containers
- Some fields are required for any PodSecurityPolicy

<pre><code>
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: noprivpods
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
</code></pre>
Once the admission controller is enabled you have to evoke a security policy for any pod action.


- Default cluster user does not operate with a Role or ClusterRole that is permitted to us a PodSecurityPolicy

#### Network Policies
- An object that defines network ingress and egress rules for Pods
- Use selectors to restrict incoming and outgoing traffic
- Can isolate traffic between namespaces
- Reduce risk of application vulnerabilities affecting other cluster services
- Should be enabled when the GKE cluster is built

#### Sample NetworkPolicy object YAML
- Policy is applied to all Pods matching: app=login-service
- ONly permits ingress from Plods matching: app=frontend-service
<pre><code>
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: login-service-from-fe
spec:
  policyTypes:
  - Ingress
  podSelector:
    matchLabels:
      app: login-service
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend-service
</code></pre>

As well as selectors pod numbers can also be specified
- Policy is applied to all Pods matching: app=restricted
- Only permits egress on TCP and UDP ports 53

<pre><code>

kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: outbound-dns-only
spec:
  policyTypes:
  - Egress
  podSelector:
    matchLabels:
      app: restricted
  egress:
  - ports:
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
</code></pre>

#### Workload Identity
- A secure way for GKE workloads to consume GCP services
- Compute Engine default service account used by GKE nodes
- Map custom GCP service accounts to GKE workloads
- Kubernetes service accounts can also be mapped to GCP service accounts
- Currently in beta

### Enchancing cluster nodes with DaemonSets

#### DaemonSets
- A workload object for managing groups of replicated Pods
- One Pod per Node model - across the entire cluster, or a subset of nodes
- If new nodes are added, new Pods are automatically created
- Very specific Use Cases

#### Use Cases
- Any scenario where workloads require access to a service on every node
- Storage daemons
- Logs collection servicess
- Monitoring daemons
- Custom Drivers

#### Example daemonset for fluentd YAML
<pre><code>
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
spec:
  selector:
    matchLabels:
      name: fluentd
  template:
    metadata:
      labels:
        name: fluentd
    spec:
    nodeSelector: 
      type: prod
    containers:
    - name: fluentd
      image: gcr.io/google-containers/fluentd-elasticsearch:1.20
</code></pre>

### Stateful applications and workloads
#### Stateless vs Stateful
- Stateless
  - Pods can be added, removed or restarted at will
  - Individual Pods have no data to persist, or concept of state
- Stateful
  - Pods have data to store and need a persistent identity
  - The deployment and scaling of Pods must be logically managed


#### StatefulSets
- Manages Pods based on a container spec, much like a Deployment
- Maintains an identity for each deployed Pod
- Guarantees the ordering and uniqueness of Pods
- Stable network identity and persistent storage
- Ordered, graceful deployment, scaling and updates


#### Example Manifest for Stateful Object
<pre><code>
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: pgsql
spec:
  selector:
    matchLabels:
      app: pgsql
  replicas: 3
  serviceName: pgsql
  template:
    metadata:
      labels:
        app: pgsql
    spec:
      containers:
      - name: pgsql
        image: example.gcr.io/postgres:9.6
        ports:
        - containerPort: 5432
          name: pgsql
        volumeMounts:
        - name: pgdata
          mountPath: /var/lib/pgsql
</code></pre>
NOTE: some specs left out of this for simplicity

#### Pod Management Policies
- Ordered Pod management is the default
  - The scheduler won't spin up or spin down a Pod until the action on the previous node is completed.
- Parallel Pod management is an option
  - Will deploy all Pods in a stateful set rather than one by one while still maintaining their individual identity, and storage persistence
#### Upload Strategies
- Rolling updates delete and recreate Pods in descending order
- The scheduler waits for a Pod to be running and ready before proceeding
- Updates can also be partitioned
- Manual updates can be enforced with the OnDelete strategy
  - Updating a pod state will cause no action to take place until the pod is manually deleted which will force the scheduler to recreate it.

### Finite Tasks and Init containers
#### Jobs and CronJobs
- A workload object that represents a finite task and manages it to completion
- A non-parallel job usually starts one Pod and is complete when the Pod terminates successfully
- A parallel job may have a fixed completion count running multiple Pods to completion
- Parallelism is configurable
- Jobs can be scheduled to be reoccuring with CronJobs

#### YAML manifest for simple single, non-parallel job
<pre><code>
apiVersion: batch/v1
kind: Job
metadata:
  name: print-pi
spec:
  template: 
    metadata:
      name: print-pi
    spec:
      containers:
      - name: print-pi
        image: perl
        command: ["perl"]
        args: ["-Mbginum=bpi", "-wle", "printbpi(2000)"]
      restartPolicy: Never
</code></pre>
#### YAML manifest for batch script
<pre><code>
apiVersion: batch/v1
kind: Job
metadata:
  name: process-uploads
spec:
  completions: 8
  template:
    metadata:
      name: process-uploads
    spec:
      containers:
      - name: processor
        image: gcr.io/example-project/processor:1.12
        command: ["run"]
        args: ["--from-bucket", "mybucket", "--num", "500"]
      restartPolicy: Never
</code></pre>


#### YAML CronJob Manifest
<pre><code>
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: process-uploads-schedule
spec:
  schedule: "0 0 * * 6"
  jobTemplate:
    spec:
      template:
        metadata:
          name: process-uplaods
        spec:
          containers:
          - name: processor
            image: gcr.io/example-project/processor:1.12
            command: ["run"]
            args: ["--from-bucket", "mybucket", "--num", "500"]
          restartPolicy: Never
</code></pre>

#### Init Containers
In Kubernetes, Init containers are special containers that run and complete their execution before the main containers in a Pod are started. Init containers are used to perform setup and initialization tasks that are necessary for the main containers to run correctly. These tasks may include downloading data, setting up credentials, or running specific scripts.

Init containers share the same Pod specification with the main containers, but are defined in a separate list within the Pod specification. They are executed in the order they are defined, and each Init container must complete successfully before the next one starts. If an Init container fails to complete successfully, Kubernetes will retry it with an exponential backoff until it succeeds.

Init containers have the same lifecycle as the main containers in a Pod, but their exit codes do not affect the state of the Pod. If an Init container fails, Kubernetes will not start the main containers, and will retry the Init container until it succeeds. Once all Init containers have completed successfully, Kubernetes will start the main containers in the Pod.

Init containers are a powerful feature in Kubernetes that allow you to perform complex initialization tasks and ensure that your application is properly set up before it starts running. By using Init containers, you can simplify your application's setup process, reduce the risk of errors, and make your application more reliable and robust.

- Part of the containers array in a Pod spec
- Execute before application or other containers in a Pod
- Init containers run in order and to completion
- Can separate custom -start-up code from an application image
- Can container logic that delays the start-up of an application

#### Init container YAML manifest
<pre><code>
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: myapp
    image: gcr.io/example-project/myapp
    volumeMounts:
    - name: myapp-files
      mountPath: /data
  initContainers:
  - name: init-files
    image: alpine/git
    command: ['git', 'clone', 'https://github.com/myapp-files/git.git /data']
    volumeMounts:
    - name: myapp-files
      mountPath: /data
  - name: init-mysql
    image: busybox
    command: ['sh', '-c', 'until nslookup mysql; do echo waiting for mysql; sleep 2; done;']
</code></pre>
App won't be allowed to start until instances have been run and completed successfully in order



