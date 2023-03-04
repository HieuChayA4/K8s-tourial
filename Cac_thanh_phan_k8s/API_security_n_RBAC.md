
- Kubernetes API là cách chính để tương tác và quản lý một cluster, và bảo mật truy cập tới API là quan trọng cho việc duy trì sự an toàn của cluster.
### Authentication

- Xác thực là tiến trình xác minh danh tính của một user hoặc một hệ thống truy cập vào API, Kubernetes hỗ trợ nhiều phương thức xác thực như là *client certificates, bearer tokens, và authentication proxies*. Lớp  xác thực trong Kubernetes có thể cài cắm module được, các mô đun phổ biến thường là *client certs, webhooks* và tích với các hệ thống quản lý danh tính bên ngoài như à *Active Directory (AD)* và cloud-based *Identity Access Management (IAM)*. Trên thực tế, không thể tạo tài khoản người dùng trong Kubernetes vì nó không có cơ sở dữ liệu nhận dạng tích hợp sẵn của riêng mình mà bắt buộc phải dùng hệ thống bên ngoài.

- Thông tin chi tiết và thông tin đăng nhập của cluster được lưu trữ trong tệp *kubeconfig*. Các công cụ như *kubectl* đọc tệp này để biết sẽ gửi lệnh tới cluster nào, cũng như sử dụng thông tin xác thực nào. Nó thường được lưu trữ ở : 
    - Windows: C:\Users<user>.kube\config
    - Linux/Mac: /home/<user>/.kube/config

### Authorization (RBAC)

- Quá trình ủy quyền (authorization) xảy ra ngay sau khi xác thực thành công. Mô đun ủy quyền thường thấy nhất của Kubernetes là RBAC (Role-Based Access Control)  - User nào có thể thực hiện hành động nào ở resrouce nào

![](https://i.imgur.com/gwY1cIU.png)

- RBAC là *least-privilege deny-by-default* system nghĩa là tất cả các *actions* đều mặc định bị từ chối, *action* cụ thể được cho phép bằng cách tạo *allow rules*. Kubernetes không hỗ trợ *deny rules* mà chỉ có *allow rules*


3. Transport Layer Security (TLS) is used to secure the communication between the API client and the API server, and it is important to properly configure and manage TLS certificates to prevent man-in-the-middle attacks.

4. Role-Based Access Control (RBAC) is a mechanism for controlling access to Kubernetes resources based on the roles of users and systems. RBAC uses roles and bindings to define what actions a user or system can perform on specific resources in the cluster.

5. RBAC can be used to enforce least privilege, which means giving users only the permissions they need to perform their tasks, reducing the risk of accidental or malicious actions that could harm the cluster.

6. The Kubernetes API provides a rich set of resources and verbs that can be used to define fine-grained access controls using RBAC, allowing administrators to enforce policies for different groups of users and systems.

7. It is important to properly manage and audit RBAC policies to ensure that the cluster remains secure, and to regularly review and update policies to reflect changes in the cluster and to address new security threats.