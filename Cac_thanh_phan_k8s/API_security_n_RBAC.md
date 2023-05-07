
- Kubernetes API là cách chính để tương tác và quản lý một cluster, và bảo mật truy cập tới API là quan trọng cho việc duy trì sự an toàn của cluster.
### Authentication

- Xác thực là tiến trình xác minh danh tính của một user hoặc một hệ thống truy cập vào API, Kubernetes hỗ trợ nhiều phương thức xác thực như là *client certificates, bearer tokens, và authentication proxies*. Lớp  xác thực trong Kubernetes có thể cài cắm module được, các mô đun phổ biến thường là *client certs, webhooks* và tích với các hệ thống quản lý danh tính bên ngoài như à *Active Directory (AD)* và cloud-based *Identity Access Management (IAM)*. Trên thực tế, không thể tạo tài khoản người dùng trong Kubernetes vì nó không có cơ sở dữ liệu nhận dạng tích hợp sẵn của riêng mình mà bắt buộc phải dùng hệ thống bên ngoài.

- Thông tin chi tiết và thông tin đăng nhập của cluster được lưu trữ trong tệp *kubeconfig*. Các công cụ như *kubectl* đọc tệp này để biết sẽ gửi lệnh tới cluster nào, cũng như sử dụng thông tin xác thực nào. Nó thường được lưu trữ ở : 
    - Windows: C:\Users<user>.kube\config
    - Linux/Mac: /home/<user>/.kube/config

### Authorization (RBAC)

- Quá trình ủy quyền (authorization) xảy ra ngay sau khi xác thực thành công. Mô đun ủy quyền thường thấy nhất của Kubernetes là RBAC (Role-Based Access Control)  - User nào có thể thực hiện hành động nào ở resrouce nào

![](https://i.imgur.com/gwY1cIU.png)

- RBAC là *least-privilege deny-by-default* system nghĩa là tất cả các *actions* đều mặc định bị từ chối, *action* cụ thể được cho phép bằng cách tạo *allow rules*. Kubernetes không hỗ trợ *deny rules* mà chỉ có *allow rules*, nghĩa là chỉ cấp cho người dùng những quyền mà họ cần để thực hiện tác vụ của mình, giảm nguy cơ xảy ra các hành động vô tình hoặc ác ý có thể gây hại cho cluster.
 
### User và Permissions
-  RBAC có hai khái niệm quan trọng cần nắm rõ: 
    - Roles : Định nghĩa một tập các *Permission*
    - RoleBindings : trao quyền đến các user

```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
    namespace: test
    name: read-deployments
rules:
- apiGroups: ["apps"]
    resources: ["deployments"]
    verbs: ["get", "watch", "list"]
-> Một Role tên "deployments" có quyền get,watch,list với các đối tượng Deployment ở "test" namespace
```
```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
    name: read-deployments
    namespace: test
subjects:
- kind: User
    name: sky                           <<====  User được xác thực
    apiGroup: rbac.authorization.k8s.io
roleRef:
    kind: Role
    name: read-deployments              <<==== Role được gán cho user
    apiGroup: rbac.authorization.k8s.io

```
-  Role định nghĩa ở trên có 3 thuộc tính: 
    - apiGroups
    - resources
    - verbs
-> 3 thuộc tính này địng nghĩa hành động nào được cho phép với *objects* , **apiGroups** và **resources** định nghĩa *object*, **verbs** định nghĩa hành động

![](https://i.imgur.com/10kpy81.png)

### Cluster-level users và permissions

- *Roles* và *RoleBinding* là các object nằm trong một *namespace*, *ClusterRoles* và *ClusterRoleBinding* là các đối tượng trên toàn cluster

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
name: read-deployments
rules:
- apiGroups: ["apps"]
resources: ["deployments"]
verbs: ["get", "watch", "list"]
```
![](https://i.imgur.com/b0GcLLD.png)

- Có thể định nghĩa các Role ở cluster level (ClusterRoles) và gắn chúng vào Namespaces thông qua RoleBindings. 

### Pre-created users và permissions

- Hầu hết cluster có một tập các roles và bindings gán quyền tới một **all-powerful user**
- Docker Desktop chạy API server trong Pod ở kube-system Namespace, cờ --authorization chỉ cho Kubernetes sử dụng mô đun nào để thựn hiện ủy quyền.
![](https://i.imgur.com/wt1D3Q6.png)
- Tất cả member của **system:masters** group được cấp quyền cluster-admin role 
![](https://i.imgur.com/nPuLvF9.png)

-  User được gán Cluster-admin role có toàn bộ *verbs* trên mọi *resources* trong mọi *Namespaces*
![](https://i.imgur.com/raavACp.png)

## Admission control

- Admission control được thực thi lập tức ngay sau khi xác thực và ủy quyền thành công, Admission control sẽ xuay quanh *policies*. Có 2 loại  admission controller : 
    - Mutating : kiểm tra sự tuân thủ và có thể sửa đổi các yêu cầu
    - Validating : kiểm tra việc tuân thủ chính sách nhưng không thể sửa đổi yêu cầu

-
