# K8s-tourial

## Basic Term
- Kubernetes is an **application orchestrator**, orchestrates containerized cloud-native microservices apps
- An orchestrator is a system that *deploys* and *manages* applications. It can deploy your applications and dynamically respond to changes
- A *containerized application* is an app that runs in a container

<p align="center">
  <img src="https://i.imgur.com/PJvLZvs.png">
</p>

- A cloud-native application is one that’s designed to meet cloud-like demands of auto-scaling, self-healing, rolling updates, rollbacks and more. Run anywhere that have Kubernetes

<p align="center">
  <img src="https://i.imgur.com/El4dHWU.png">
</p>

- A microservices app is built from lots of independent small specialised parts that work together to form a meaningful application
- Each microservice runs as a container

<p align="center">
  <img src="https://i.imgur.com/XC6RIxT.png">
</p>

:point_right: Kubernetes deploys and manages (*orchestrates*) applications that are packaged and run as containers (*containerized*) and that are built in ways (*cloud-native microservices*) that allow them to scale, self-heal, and be updated in-line with modern cloud-like requirements.

## Kubernetes and Docker
- Docker has tools that build and package applications as container images.
- Kubernetes operates at a higher level providing orchestration services such as self-healing, scaling and updates.

<p align="center">
  <img src="https://i.imgur.com/Jrckkgb.jpg">
</p>

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

<p align="center">
  <img src="https://i.imgur.com/MfYhI3o.png">
</p>

## Worker nodes

- Nodes are servers that are the workers of a Kubernetes cluster: 
  - Watch the API server for new work assignments
  - Execute work assignments
  - Report back to the control plane (via the API server)

- Contains three components: 
  - Kubelet
  - Container Runtime
  - kube - proxy

<p align="center">
  <img src="https://i.imgur.com/porDOMc.png">
</p>

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

<p align="center">
  <img src="https://i.imgur.com/Y9JUv73.png">
</p>

- Atomic unit of scheduling >> Pod
- Kubernetes runs containerized apps, container runs inside a Pod.

### Pods and Containers

- a Kubernetes Pod is a construct for running one or more containers

<p align="center">
  <img src="https://i.imgur.com/8kX9lCg.png">
</p>

### Pod anatomy

- Multiple containers in a Pod, they all share the same Pod environment. All containers in
the same Pod will share the same IP address (the Pod’s IP).

<p align="center">
  <img src="https://i.imgur.com/bQj01Gc.png">
</p>

- Containers in the same Pod can communicate with each other (container-to-container within the Pod) by the Pod’s localhost interface.

<p align="center">
  <img src="https://i.imgur.com/lkGBkmB.png">
</p>

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

<p align="center">
  <img src="https://i.imgur.com/yVkVstF.png">
</p>

## Pod theory

<p align="center">
  <img src="https://i.imgur.com/15NHd0X.png">
</p>
 
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

<p align="center">
  <img src="https://i.imgur.com/CCUy7Aj.png">
</p>

<p align="center">
  <img src="https://i.imgur.com/SFlUSsf.png">
</p>

## Multi-container Pods

- Kubernetes offers several well-defined multi-container Pod patterns:
   - Sidecar pattern
   - Adapter pattern
   - Ambassador pattern
   - Init pattern
   


## Virtual clusters và Namepaces
- Namespaces là cách để chia một Kubernetes cluster thành nhiều cluster ảo.
- Kubernetes Namepaces # [Linux Kernel namespace](./Gioi%20thieu/Namespace_linux.md)
- Namespaces là một phần của Kubernetes cluster được thiết kế để có thể áp *quotas* và *policy* tới các nhóm objects. Các đối tượng có thể được *namespaced*  bao gồm các đối tượng như Pods, Services và Deployments.
![Phạm vi của Namespaces trong Kubernetes](https://i.imgur.com/NR2u1mi.png)
  - Một Kubernetes Cluster sẽ bao gồm rất nhiều node (master, worker).
  - Một namespace trong Kubernetes Cluster sẽ nằm trên tất cả các node.
  - Mọi ứng dụng khi triển khai trong Kubernetes phải thuộc vào một namespace nào đó.

- Resource: Được hiểu là một loại tài nguyên được kubernetes quản lý như namespace, pods, volume, service, serviceaccount, configMap, secret,…
 ![](https://i.imgur.com/nQm6q2N.png)

- Có 2 cách để triển khai objects tới một Namespace cụ thể: 
  - Imperatively: yêu cầu thêm flag -n hoặc --namespace vào commands 
  - Declaratively: chỉ định Namespace trong file YAML.

## Kubernetes Deployments
- Deployment là đối tượng chính đảm nhận việc deploy và quản lý ứng dụng của K8s. Nó cho phép deploy các Pod, update Pod, rollback Pod và các ReplicaSet.
- Để tạo mới đối tượng Deployment, cần định nghĩa thông tin cho nó sử dụng tập tin YAML.
Ví dụ: 

```
Version: apps/v1
kind: Deployment
metadata:
  name: <name>
spec:
  replicas: <replicas-number>
  selector:
    matchLabels:
      <label>
  template:
    metadata:
      labels:
        <label>
    spec:
      containers:
      - name: <container_name>
        image: <container_image>
      - name: <container_name>
        image: <container_image>
```
  - <name> là tên của Deployment
  - <replicas-number> là số lượng Pod mà ta muốn Deployment đảm bảo.
  - <label> định nghĩa label cho Pod.
  - <container_name> tên của container trong Pod sau khi khởi tạo
  - <container_image> tên của Image được sử dụng để tạo container.

- Một Deployment object chỉ có thể quản lý một Pod template. Ví dụ một ứng dụng có dịch vụ front-end và back-end sẽ cần các Pod khác nhau cho mỗi dịch vụ và sẽ đi kèm theo 2 Deployment Objects. Và mỗi Deployment Objects có thể quản lý nhiều bản replica của Pod giống nhau. 
### Deployments và ReplicaSets
- Deployment sẽ quản lý các ReplicaSet và các ReplicaSet sẽ quản lý các Pods và mang tới khả năng self-healing và sacling để chạy các ứng dụng. Deployment sẽ tạo ra các ReplicaSet để đảm bảo số lượng Pod đồng thời thêm *rollout* và *rollback*
- Để định nghĩa số lượng replicas, số lượng Pod mà Deployment này quản lý, sử dụng field *spec.replicas*. Trường *spec.selector* dùng để định nghĩa cách mà Deployment tìm các Pod cần quản lý ở Thuộc tính *matchLabels* . Bất kỳ Pod nào có label định nghĩa là “<label>” sẽ được Deployment select và quản lý.
- trường *spec.strategy* có thể dùng để định nghĩa cách rollout và rollback các update của Pod.
- Trường *spec.template* dùng để định nghĩa thông tin về Pod cần chạy. Thuộc tính *metadata.labels* trong trường *spec.template* có thể dùng để gán nhãn cho Pod.

Ví dụ: 

```
 Version: apps/v1
kind: Deployment
metadata:
  name: node-hello
spec:
  strategy:
    type: Rollingupdate
    rollingupdate:
      maxUnavailable: 1
      maxSurge: 1
  replicas: 1
  selector:
    matchLabels:
      app: node-hello
  template:
    metadata:
      labels:
        app: node-hello
    spec:
      containers:
      - name: node-hello
        image: gcr.io/google-samples/node-hello:1.0

```
- Việc update sử dụng trường *rollingupdate*
- *maxUnavailable: 1* đảm bảo không có quá 1 Pod không hoạt động trong quá trình update
- *maxSurge: 1* đảm bảo không có quá 1 Pod được tạo nhiều hơn trong quá trình update

### Declarative vs imperative
Có hai cách cơ bản để triển khai trong Kubernetes: **imperative**, sử dụng các lệnh *kubectl* hoặc **declarative**, bằng cách viết file khai báo và sử dụng kubectl apply.

[Declarative vs imperative tourial](https://medium.com/payscale-tech/imperative-vs-declarative-a-kubernetes-tutorial-4be66c5d8914)

### Rolling update một Deployment

- Rolling update cho phép ta thay đổi các thiết lập của Pods một cách từ từ.
Ví dụ: 
- Để thực hiện update image của container, ta cần update file YAML Deployment với bản image mới hơn và post nó lên API server. Thao tác này cập nhật Deployment object hiện có với *desired state* mới yêu cầu cùng số lượng *Pods* nhưng tất cả đều chạy với *image* phiên bản mới hơn.
- Kubernetes tạo một *ReplicaSet* thứ hai để tạo và quản lý các *Pods* với *image* mới. Tại thời điểm này, K8s tồn tại hai *ReplicaSet* – *ReplicaSet* đầu dành cho các *Pods* có *Image* cũ và *ReplicaSet* mới dành cho các *Pods* có *Image* mới. Khi Kubernetes tăng số lượng *Pods* trong *ReplicaSet* mới, nó sẽ giảm số lượng *Pods* trong *ReplicaSet* cũ. Quá trình update diễn ra một cách zero downtime.

- Quá trình *Rollback* diễn ra một cách tương tự

## Kubernetes Services

```
[To do next]
- Continue content of the book
- Cách bầu bán, lưu trữ, bầu chọn master node etcd ?
- Controller theo dõi lẫn nhau ?
- Cách 1 pod nhận dc ip từ kupe proxy ? Công nghệ phía sau ?
- - Ngoài iptable của kupe proxy còn cn nào khác? 