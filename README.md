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

- *Pods* có khả năng seld-healing, mở rộng và rollouts nhờ *Controller* nhưng chúng vẫn không đáng tin cậy khi được kết nối một các trực tiếp. Khi một Pod lỗi nó được thay thế bởi một Pod khác hoặc khi scale Pod thì các Pod với IP mới sẽ được sinh ra... Việc kết nối đến từng Pod để vận hành trong K8s là k thể.  *Services* cung cấp mạng lưới ổn định và đáng tin cậy, cân bằng tải để kết nối, giao tiếp tới một nhóm các Pods.
- *Service* là một REST object trong API được định nghĩa trong file manifest và được post lên API server.
- Mỗi *Service* có một IP riêng, một stable DNS name, và stable port.
- *Services* sử dụng các thuộc tính *labels* và *selectors* để lựa chọn các Pods được gửi traffic tới một các linh động.

![how client connect to Pods throuch Services](https://i.imgur.com/uPWt8fb.png)

-  Thông qua *Service* client có thể truy cập tới các Pod tông qua IP,DNS,Port của *Service* cho dù các Pods có thể scale up, down, hay update, rollback,..

-  Bản thân *Services* cũng có khả năng giảm bớt các liên kết ràng buộc tới các Pods thông qua *labels* và *selectors*

![select Pods with labels](https://i.imgur.com/GUZdbcV.png)

- Khi các *labels* được liệt kê trong *selector*, *service* sẽ sử dụng hàm AND để lựa chọn các Pods đúng với các labels được liệt kê kể cả những Pod có nhiều hơn các labels được liệt kê.

![](https://i.imgur.com/RGPXGaw.png)

Ví dụ: 
```
**svc.yml**
apiVersion: v1
kind: Service
metadata:
name: hello-svc
spec:
ports:
- port: 8080
selector:
app: hello-world <<== gửi tới Pods với label 
env: tkb <<== gửi tới Pods với label 

**deploy.yml** -> triển khai pod
apiVersion: apps/v1
kind: Deployment
metadata:
name: hello-deploy -> tên pod
spec:
replicas: 10 -> số lượng
<Snip>
template:
metadata:
labels:
app: hello-world <<== đánh dấu lable cho Pods
env: tkb <<== Pod labels
spec:
containers:

=> Service sẽ chọn các Pods với lable "app: hello-world" và "env: tkb" được khởi tạo bởi template được định nghĩa trong file deploy.yml
```
-  *Serivces* tự động cập nhật danh sách các Healthy pod thông qua sự kết hợp của việc lựa chọn các *label* và một cấu trúc gọi là *Enpoint opject*

- Khi một *Serive* được tạo, K8s tự động tạo một *Enpoint object* gắn liền với nó. *Enpoint object* được sử dụng để lưu trữ một danh sách các Pods khỏe mạnh khớp với các lable được định nghĩa trong selecor của *Service*.

### Truy cập *Services* từ trong cluster

- K8s hỗ trợ một số loại *Serive*, mặc định là *CLusterIP* 
- Service loại *ClusterIP* này sẽ có thể được truy cập bởi các ứng dụng khác chỉ khi các ứng dụng đó cũng nằm trong cluster. Các ứng dụng bên ngoài cluster sẽ không thể truy cập đến Service.
- *ClusterIP* được đăng ký với tên của *Service* trong dịch vụ DNS nội bộ của *Cluster*. Tất cả các Pods trong Cluster được lập trình sẵn để sử dụng dịch vụ DNS của Cluster, tất cả các Pods có thể chuyển đổi tên *Services* thành *ClusterIP*.

### Truy cập *Services* từ bên ngoài cluster

- K8s có 2 loại *Service* cho các request  đến từ bên ngoài Cluster là: NodePort và LoadBalancer.

#### NodePort
- *NodePort* nằm trên *ClusterIP* nó sẽ mở một port cụ thể trên tất cả các node trong Cluster và lưu lượng truy cập được gửi đến bất kì node nào trong Cluster thông qua port được mở sẽ được chuyển tiếp đến dịch vụ bên trong Cluster.
- Phạm vi của các port được mở cho service NodePort nằm trong khoảng 30000-32767, có thể trực tiếp chỉ định port này trong file yaml của mình hoặc để Kubernetes tự động chỉ định.
- *ClusterIP* đăng ký một DNS name, virtual IP, và port với Cluster's DNS, *NodePort Service* xây dựng trên cơ sở này và thêm một *NodePort* có thể sử dụng để truy cập *Service* từ bên ngoài Cluster.

**Ví dụ**
```
apiVersion: v1 
kind: Service 
metadata:
  name: myapp-service 
spec: 
  type: NodePort 
  selector: 
    app: myapp 
    type: front-end 
  ports: 
    - targetPort: 8080 
      port: 80 
      nodePort: 32593

-> Client kết nối từ ngoài Cluster có thể gửi traffic tới bất kỳ node nào của Cluster trên port 32593. Các Pods trên Cluster có thể truy cập *Service* này bằng tên "myapp-service" qua port 80.
```
#### LoadBalancer

- *Service LoadBalancer* giúp việc truy cập từ bên ngoài trở nên dễ dàng hơn bằng cách tích hợp với bộ cân bằng tải giao tiếp với internet trên nền tảng đám mây. Một địa chỉ Ip duy nhất sẽ chuyển tiếp tất cả các yêu cầu truy cập đến *Service* của Cluster 
- *LoadBalancer* sẽ tạo ra một service *NodePort*, và yêu cầu nhà cung cấp dịch vụ lưu trữ Cluster K8S thiết lập một Public IP hoặc DNS name mà Clients có thể truy cập *Service* từ đó. 

## Ingress
- *NodePort* và *LoadBalancer* Services  có thể expose ứng dụng với người dùng tuy nhiên vẫn còn những hạn chế
- Như đã đề cập ở trên *NodePort* chỉ làm việc với dải port 30000-32767 , và cần thông tin của Node name hoặc node IPs. *LoadBalancer* cần ánh xạ 1-1 giữa Service nội bộ và dịch vụ cân bằng tải của cloud provider. 
- *Ingress* có thể giải quyết các vấn đề trên bằng cách expose nhiều *Services* qua một cloud load-balancer, Không bị hạn chế bởi số lượng Port mà NodePort có thể cung cấp. *Ingress* tạo một *LoadBalancer Service* trên port 80 hoặc 443  và sử dụng host-based và path-based routing để gửi traffic tới chính xác *Service*.

![](https://i.imgur.com/CqYat7v.png)

- Cơ chế hoạt động của Ingress gồm 2 thành phần chính:

  - Ingress Controller: Là thành phần điều khiển chính làm nhiệm vụ điều hướng các request tới các service bên trong k8s. Thường thì Ingress Controller được cài đặt trên K8S và được expose ra ngoài dưới dạng NodePort.
  - Ingress Rule: Là một tài nguyên trên K8S. Nó chứa nội dung khai báo rule để điều hướng từ một request tới một service cụ thể trên trong K8S.
- *Ingress* hoạt động ở lớp 7 của mô hình OSI hay lớp ứng dụng , có thể kiểm tra HTTP header cũng như chuyển tiếp lưu lượng truy cập dựa trên tên host và path.

![Host-based Routing](https://i.imgur.com/kaqU7Rl.png)

![](https://i.imgur.com/xCwbPLy.png)

- *Ingress* là một tài nguyên ở mức Namespace trên K8S. Và giống như các tài nguyên khác như Pod, Deployment hay Service, ta có thể định nghĩa nó bằng cách sử dụng file manifest dạng yaml.

**Ví dụ**

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx-example
  rules:
  - http:
      paths:
      - path: /example
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 8080

-> mọi request tới mà có Path chứa Prefix là /example thì sẽ được forward tới servcie test ở port 80.
```
#### Ingress Rule 

Mỗi HTTP rule sẽ bao gồm các thông tin sau:
  - Thông tin host (không bắt buộc). Nếu có khai báo host cụ thể, rule sẽ chỉ apply cho host đó. Nếu host không được khai báo, thì rule được áp dụng cho mọi http đến.
  - Danh sách paths (ví dụ /testpath như bên trên), mỗi path sẽ có thông tin pathType và một backend (service) tương ứng với port của nó.
  - Một backend là một bộ gồm service và port. HTTP (HTTPS) request mà thỏa mãn điều kiện về host và path sẽ được chuyển tới backend đã khai báo
#### Path types
Mỗi cấu hình path trong ingress đều yêu cầu phải có path type tương ứng. Có 3 loại path type đang được k8s support gồm:
  - ImplementationSpecific
  - Exact
  - Prefix

- Tài liệu tham khảo thêm về *Ingress* : [Click here](https://kubernetes.io/docs/concepts/services-networking/ingress/)

## Service Registration
- Service Registration là tiến trình mà ứng dụng đăng chi tiết kết nối của nó tới *serivce registry* để các ứng dụng khác có thể tìm thấy nó và sử dụng nó.
- Đối với K8s : 
  - K8s sử dụng DNS nội bộ như là một *serivce registry*
  - Tất cả K8s *Services* tự động đăng ký thông tin của chúng với DNS

- Kubernetes cung cấp dịch vụ DNS nội bộ “DNS cluster”. Mọi Pod trong Cluster đều có thể tìm thấy nó. DNS cluster được triển khai trong *kube-system Namespace* dưới dạng một tập hợp các Pods được quản lý bởi *Deployment* là *coredns*.
- Cluster DNS là một *Kubernetes-native application*, nó có một controller theo dõi API server cho những *Serivces* mới. Mỗi khi *Service* mới được tạo, Cluster DNS tự động tạo bản ghi DNS ánh xạ Service name tới ClusterIP.
- Việc xác định Pods nào để gửi traffic đến khi có truy cập tới *Serivces*, K8S sử dụng Endpoints/EndpointSlice cho mọi *Service*.
- Kubelet agent trên mỗi node liên tục theo dõi API server cho Endpoints/EndpointSlice objects mới. Kubelet agent tạo các  local networking rules để redirect ClusterIP traffic tới các Pod IP. Trong các Linux-based Kubernetes clusters, công nghệ được sử dụng để tạo các rules này là [Linux IP Virtual Server (IPVS)](https://cloudcraft.info/loadbalance-gioi-thieu-lvs-ipvs/).

- Một tiến trình Service Registration đầy đủ được thể hiện ở hình duới: 

![](https://i.imgur.com/npJv7DL.png)

  - *Service* manifest được gửi tới API server để xác thực
  - *Service* được phân bổ một ClusterIP và cấu hình của nó được duy trì trong cluster store.
  - Một Endpoint/EndpointSlice opject được tạo để giữ danh sách các IP Pod khỏe mạnh phù hợp với label selector.
  - cluster DNS với vai trò là một Kubernetes-native application theo dõi API server cho Service objects mới, đăng ký các bản ghi
  - Mỗi node đang chạy một kube-proxy quan sát các objects mới và tạo các quy tắc IPVS/iptables cục bộ để định tuyến lưu lượng truy cập vào ClusterIP của *Service* đến các Pods phù hợp với label selector của *Service*.

## Service discovery
### Cluster DNS
- K8s tự động cấu hình mọi containter để chúng có thể tìm và sử dụng cluster DNS để phân giải Service name thành địa chị IP. Việc này được thực hiện bằng cách quảng bá tới tất cả container’s /etc/resolv.conf file với địa chỉ IP của cluster DNS Service. 
- Các ứng dụng sẽ phát hiện Virtual IP của một Service thông qua DNS. Kubernetes có cung cấp cho ta một internal dns server, khi một Service mới được tạo ra thì bên cạnh Virtual IP được gán cho nó thì còn có thêm một DNS đi kèm.
- Dns của Service với format FQDN (fully qualified domain name) <service-name>.<namespace>.svc.cluster.local. Với <service-name> là tên của service ở trường metadata.name, còn <namespace> là tên namespace mà Service được tạo, .svc định nghĩa cho nó là service resource, cluster.local là hậu tố cố định.
```
$ cat /etc/resolv.conf
search svc.cluster.local cluster.local default.svc.cluster.local
nameserver 192.168.0.10
options ndots:5
-> Một container được cấu hình để gửi các DNS query tới cluster DNS với địa chỉ IP là 192.168.0.10

$ kubectl get svc -n kube-system -l k8s-app=kube-dns
NAME TYPE CLUSTER-IP PORT(S) AGE
kube-dns ClusterIP 192.168.200.10 53/UDP,53/TCP,9153/TCP 3h53m
-> Servive names sẽ được gửi tới cluster DNS tên là "kube-dns" để chuyển thành các địa chỉ IP
```
- Khi ứng dụng trong pod gọi tới dns của Service thì nó sẽ được phân giải bởi k8s internal DNS, và sau đó internal DNS sẽ trả về cho ứng dụng đó IP của Service.
### Environment variables
- Một hình thức khác để nhận service discovery là thông qua environment variables. Mỗi Pod có một tập các environment variables để phân giải *Services* hiện có trong Cluster. Tuy nhiên, Pods không thể học các Services mới được thêm sau khi Pod được tạo ra, để lấy được thông tin mới thì pod của ta cần được restart lại. Do đó thông thường ta sẽ sử dụng cluster DNS.

### Service discovery and Namespaces
-  Mọi cluster đều có một *address space* và Namespaces phân vùng cho nó.
- Cluster *address space* dựa trên một DNS domain được gọi là cluster domain. Tên miền mặc định thường là *cluster.local* và các objects có một tên duy nhất ở trong đó. Namespaces cho phép phân vùng *address space* 
- Format:  <object-name>.<namespace>.svc.cluster.local
- Các Object name phải là duy nhất khi trong cùng một Namespace nhưng có thể giống nhau ở trên các Namespace khác nhau.
![](https://i.imgur.com/Rzxt9Wu.png)

- Các Objects có thể kết nối với Service trong cùng một Namespace tên rút gọn như *ent* và *cer*. Nhưng việc kết nối với các Object trong Namespace khác yêu cầu FQDN chẳng hạn như *ent.dev.svc.cluster.local* và *cer.dev.svc.cluster.local*.

### Summarising service discovery

![](https://i.imgur.com/YHVDGaU.png)
- Container được cấu hình địa chỉ của ClusterDNS trong tệp */etc/resolv.conf* gửi query Service name tới cluster DNS để phân giải thành địa chỉ IP.
- Cluster DNS trả về ClusterIP và Container gửi traffic tới nó thông qua default gateway của nó - node
- Node gửi traffic tới default gateway của chính nó và request được thực hiện bới node's kernel.
- Traffic sẽ được điều hướng đến các IP của Pod khớp với Service's label selector thông qua local IPVS rule của *kube-proxy*
## Kubernetes Storage
[Kubernetes Storage](.\Cac_thanh_phan_k8s\Storage.md)
```
[To do next]
- Continue content of the book
- Cách bầu bán, lưu trữ, bầu chọn master node etcd ?
- Controller theo dõi lẫn nhau ?
- Cách 1 pod nhận dc ip từ kupe proxy ? Công nghệ phía sau ?
- - Ngoài iptable của kupe proxy còn cn nào khác? 