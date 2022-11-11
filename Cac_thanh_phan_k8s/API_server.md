
- Trước tiên ta cần hiểu khái niệm về Kubernetes API : API Kubernetes là một interface lập trình resource-based (RESTful) được cung cấp qua giao thức HTTPs. Hỗ trợ truy xuất, tạo, cập nhật và xóa các tài nguyên thông qua các hành động với HTTP (POST, PUT, PATCH, DELETE, GET).
    - K8S API cho phép người dùng lấy thông tin về trạng thái của các đối tượng trong hệ thống như Pods, Namespaces, Services… Hầu hết các tác vụ sử dụng kube-api thông qua lệnh kubectl nhưng cũng có thể gọi trực tiếp REST API.
- Kubernetes thường sử dụng thuật ngữ RESTful phổ biến để mô tả các API concept:
    - Một *resource type* là tên mà được sử dụng trong một URL (pods, namespaces, services)
    - Tất cả các resource types đều có một object schema là *kind*
    - Danh sách các instances của một resource được gọi là *collection*
    - Một single instance  của một resoure type là một *resource*, thường đại diện cho một *object*
    - Đối với một số *resource type*, API bao gồm một hoặc nhiều *sub-resources*, được biểu diễn dưới dạng đường dẫn URI bên dưới resource
- Các *object* được tạo thông qua API có một tên duy nhất cho phép tạo và truy xuất. Trong một namespace chỉ một *object* của một *kind* có thể có một tên duy nhất tại một thời điểm, có thê xóa *object* và tạo một *object* mới cùng tên.

## API server

- Kubernetes API server hay là: *kube-apiserver* là một thành phần cấu thành kubernetes control plane có nhiệm vụ expose các Kubernetes API và giúp các thành phần khác trong K8s cluster giao tiếp với nhau. API server sẽ chịu trách nhiệm Authentication, Authorization client, validation config của resource, chuyển đổi resource thành dạng có thể lưu vào trong etcd.

![API_request_flow](https://i.imgur.com/Oap1l7x.png)

- Client gửi request tới Kubernetes để tạo, đọc, cập nhật và xóa các *object* như Pod và Services. Client có thể sử dụng *kubectl* hoặc các công cụ phất triển API khác để tạo request. Request sẽ được gửi đến API server để xác thực và ủy quyền.

- Nếu đó là Create request, *object* sẽ được triển khai tới cluster và trạng thái của nó sẽ được lưu giữ trong cụm lưu trữ.

### Ví dụ: Tạo một Pod trên K8s
- Câu lệnh tạo một Pod chạy Nginx 

> kubectl run --generator=run-pod/v1 nginx --image=nginx --restart="Always"

- *kubectl* kết nối với *kube-apiserver* và yêu cầu tạo một Pod có tên Nginx với image là nginx
- *kubectl* sẽ tạo một POST request kèm theo body là config của resource tới API server. Sau đó API server sẽ kiểm tra xem client này có được phép gọi tới nó hay không qua Authentication plugin, sau đó API sẽ kiểm tra quyền của client này có được thực hiện hành động này hay không qua Authorization plugi.
- API server sẽ thực hiện chuyển đổi config resource ban đầu thành dạng format nó có thể đọc thông quá các Admission control plugin
- API server sẽ validation config của resource này có đúng hay không, nếu mọi thứ đều ok hết thì resource này sẽ được lưu vào ectd.
- Khi ghi dữ liệu thành công vào etcd, etcd sẽ trả lời api-server và client.
- *Scheduler* thấy có 1 Pod được tạo trên Api server nhưng chưa được bound vào node. Scheduler lúc này sẽ thực hiện kiểm tra tất cả các node đang sẵn sàng và chọn ra một node tối ưu nhất (ví dụ node1) có thể chạy Pod trên và trả kết quả về cho api-server.
- Khi nhận được kết quả của Scheduler, api-server sẽ ghi thông tin vào etcd, hiểu rằng Pod nginx sẽ cần được tạo trên node1.
- kubelet định kỳ kết nối với api-server để cập nhật trạng thái node mà nó đang quản lý, trạng thái các Pod đang chạy trên node đó cũng như nhận các yêu cầu mới từ api-server
- sau khi kubelet đã nhận thông tin từ api-server, nó làm việc với CRI.
- Pod đã được tạo thành công, Controller định kỳ sẽ gọi api-server để biết trạng thái hiện tại (current state) và trạng thái mong muốn (desired state). Trạng thái mong muốn là có 1 Pod nginx chạy trên node1, còn hiện tại thì không có Pod nginx nào đang chạy --> Controller Manager sẽ yêu cầu tạo lại Pod nginx để đảm bảo trạng thái hiện tại phải giống với trạng thái mong muốn.
