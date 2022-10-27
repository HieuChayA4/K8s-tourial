# K8s-tourial

## Basic Term
- Kubernetes is an **application orchestrator**, orchestrates containerized cloud-native microservices apps
- An orchestrator is a system that *deploys* and *manages* applications. It can deploy your applications and dynamically respond to changes
- A *containerized application* is an app that runs in a container

![](https://i.imgur.com/PJvLZvs.png)

- A cloud-native application is one that’s designed to meet cloud-like demands of auto-scaling, self-healing, rolling updates, rollbacks and more. Run anywhere that have Kubernetes

 ![](https://i.imgur.com/El4dHWU.png)

- A microservices app is built from lots of independent small specialised parts that work together to form a meaningful application
- Each microservice runs as a container

![](https://i.imgur.com/XC6RIxT.png)

:point_right: Kubernetes deploys and manages (*orchestrates*) applications that are packaged and run as containers (*containerized*) and that are built in ways (*cloud-native microservices*) that allow them to scale, self-heal, and be updated in-line with modern cloud-like requirements.

## Kubernetes and Docker
- Docker has tools that build and package applications as container images.
- Kubernetes operates at a higher level providing orchestration services such as self-healing, scaling and updates.
![](https://i.imgur.com/Jrckkgb.jpg)

:hugs: *"Hey Kubernetes, here’s an app. Please deploy it and make sure it keeps running…"*"

## Kubernetes principles of operation
At the highest level, Kubernetes is two things:
• A cluster to run applications on
• An orchestrator of cloud-native microservices apps

A Kubernetes cluster: 
- Control Plane : exposes the API, scheduling, auto-scaling, and zero-downtime rolling updates, records the state of the cluster, ... 
- Nodes         : Executing user applications.

## Control plane
- A Kubernetes control plane node is a server running collection of system services that make up the control plane of the cluster.

### The API server
- **All communication, between all components, must go through the API server** *– all roads lead to the API Server.*
- YAML files (*manifests*) ->  desired state of an application -> container images, ports, Pods
- Request -> API server -> authenticated -> YAML validated -> work sheduled (1).

### The cluster store
- The cluster store is the only *stateful* part of the control plane and persistently stores the entire configuration and state of the cluster - *no cluster store, no cluster.*
- The cluster store is currently based on etcd 
- Etcd: A highly available Key-value Storage used to store the entire configuration, state, and metadata of the Kubernetes Cluster.

### The controller manager and controllers

- The controller manager is a controller of controllers, spawns all the independent controllers and monitors them.
-> Ensure the observed state of the cluster matches the desired state
  - Obtain desired state
  - Observe current state
  - Determine differences
  - Reconcile differences
- Each controller does it own jobs

### The Scheduler
- (1) -> The Scheduler -> filters out nodes incapable -> ranks the capable nodes -> 

### The cloud controller manager

-  Cluster on a supported public cloud platform, such as AWS, Azure, GCP, ... -> control plane will be running a *cloud controller manager*

![](https://i.imgur.com/MfYhI3o.png)

## Worker nodes

- Nodes are servers that are the workers of a Kubernetes cluster: 
  - Watch the API server for new work assignments
  - Execute work assignments
  - Report back to the control plane (via the API server)

- Contains three components: 
  - Kubelet
  - Container Runtime
  - kube - proxy

![](https://i.imgur.com/porDOMc.png)

### Kubelet
- The kubelet is main Kubernetes agent and runs on every cluster node
- watch the API server for new work tasks, report back to control planes

### Container runtime
- The kubelet needs a container runtime to perform container-related tasks

### Kube - proxy
- runs on every node
- responsible for local cluster networking.
- kube-proxy is responsible for managing network policies on each Node and forwarding or filtering traffic to the Node based on these policies.

## Kubernetes DNS
- The cluster’s DNS service has a static IP address that is hard-coded into every Pod on the cluster. This ensures every container and Pod can locate it and use it for discovery.

## What is Pod?

![](https://i.imgur.com/Y9JUv73.png)
- Atomic unit of scheduling >> Pod
- Kubernetes runs containerized apps, container runs inside a Pod.

### Pods and Containers

- a Kubernetes Pod is a construct for running one or more containers

![](https://i.imgur.com/8kX9lCg.png)

### Pod anatomy

- Multiple containers in a Pod, they all share the same Pod environment. All containers in
the same Pod will share the same IP address (the Pod’s IP).

![](https://i.imgur.com/bQj01Gc.png)

- Containers in the same Pod can communicate with each other (container-to-container within the Pod) by the Pod’s localhost interface.

![](https://i.imgur.com/lkGBkmB.png)

- Pods is the minimum unit of scheduling in Kubernetes.
- a Pod is only ready for service when all its containers are up and running.

### Pod lifecycle

- Pods are mortal. a Pod dies -> Kubernetes start a new Pod with new ID and IP.
- Updating Pods -> delete the old one and replace it with a new one.

## Deployments

- Deployments, DaemonSets and StatefulSets are implemented as controllers that run as watch
loops constantly observing the cluster making sure observed state matches desired state.

## Service objects and stable networking

- Services provide reliable networking for a set of Pods.
- Services represent an entry point to access application.
- Services are used to discover and perform load-balancing for an object group of Pods with *Labels*.
- Services scale application without affecting the end-user.
- Services bring stable IP addresses and DNS names to the unstable Pods.

![](https://i.imgur.com/yVkVstF.png)

## Pod theory

![](https://i.imgur.com/15NHd0X.png)
 
- *Labels* : group Pods and associate them with other objects
- *Annotations* : add experimental features and integrations with 3rd-party tools and services
- *Affinity and anti-affinity*: control over where Pods run.
- *Termination control*: terminate Pods and the applications.
- *Security policies* : enforce security features.
- *Resource requests and limits*: specify minimum and maximum values for things like CPU, memory and disk IO.

### Pods enable resource sharing
-  Pods provide a shared execution environment for one or more containers
   - Shared filesystem
   - Shared network stack (IP address and ports…)
   - Shared memory
   - Shared volumes
### Static Pods vs controllers

- static Pods: deployed directly from a Pod manifest (no self-healing,scaling, or rolling updates).
- Pods deployed via controllers have all the benefits of being monitored and managed by a highly-available controller running on the control-plane.

### Pods and shared networking

- Each Pod creates its own *network namespace*.

![](https://i.imgur.com/CCUy7Aj.png)

![](https://i.imgur.com/SFlUSsf.png)

## Multi-container Pods

- Kubernetes offers several well-defined multi-container Pod patterns:
   - Sidecar pattern
   - Adapter pattern
   - Ambassador pattern
   - Init pattern
