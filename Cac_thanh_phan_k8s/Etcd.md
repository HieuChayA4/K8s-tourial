- "/etc" lưu trữ cấu hình của một hệ thống -> d"istributed “/etc” tạo thành “etcd” để lưu thông tin cấu hình của các hệ thống phân tán quy mô lớn.
-* Etcd* là key-value store được K8s sử dụng để lưu trữ dữ liệu cluster. Nó cung cấp một cách để lưu trữ dữ liệu cần được truy cập bởi một hệt thống phân tấn hoặc cluster một cách nhất quán và có khảng năng chịu lỗi.
- *Etcd* được viết bằng Go, hỗ trợ đa nền tảng, các file nhị phân nhỏ và một cộng đồng lớn. Giao tiếp giữa các máy etcd được xử lý thông qua thuật toán đồng thuận Raft - đại khái là cơ chế cho phép một cụm nhiều máy tính (server) hoạt động mạch lạc, suôn sẻ, dù cho trong đó có một vài server đang bị ngắt kết nối với cluster.


- Khi một client hoặc một process  gọi một API, API server tiến hành: 
    - Xác định xem client này có quyền gọi API hay không  
    - Xác định xem request gọi API có được authorized hay không
    - Kiểm tra config resouce có đúng hay không, nếu có thì API server lưu reousce này vào etcd
    - các thành phần khác của control plane xác định pods nào sẽ chạy dựa trên các đối tượng *Deploymments*, lên lịch cho *Pods* đến các *node* có tài nguyên phù hợp, thiết lập mạng, v.v. 

-> tại sao k8s k dùng databse khác như SQl, PostgreSql,.. ? 
- API server có nhiệm vụ giúp các thành phần khác trong K8S giao tiếp với nhau và expose API trên toàn bộ cluster. Đồng thời K8s cần một nơi để lưu trữ các trạng thái mong muốn của *object* -> cần tính nhất quán cao.
- Khi API ngừng hoạt động kéo theo toàn bộ kubernetes control-plane ngừng hoạt động theo -> Tính khả dụng cao
- Khi API server nhận lượng lớn traffic, việc ghi thông tin vào hệ thống lưu trữ bị chậm cũng sẽ ảnh hưởng hoạt động của toàn hệ thống.
- API server cần thông báo mọi sự thay đổi của các resources theo thời gian thực.
- Tuy nhiên API server chỉ lưu trữ metadata của Pods và các *object* khác -> không cần thiết một cơ sở dự liệu với tập dự liệu lớn
- Hầu hết các *object* được 3truy cập bởi các pattern (type, namespace, name,..) -> truy xuất dạng key-value
-> các cơ sở dữ liệu như SQL không phù hợp (query, tốn công sức triển khai replica, HA,..)

- Etcd là một "strongly consistent, distributed key-value store": 
    - Etcd có tính nối tiếp chắc chắn, có nghĩa là các hoạt động xảy ra theo một số thứ tự, phù hợp với thứ tự thời gian thực của các hoạt động đó (nếu hành động tạo A hoàn thành trước khi hoạt động B bắt đầu, thì A sẽ xuất hiện trước B theo thứ tự tuần tự hóa và ngược lại khi hành động chính sửa A thành thì không ai có thể thấy được dữ liệu cũ của A.)
    - Không giống như cơ sở dữ liệu SQL truyền thống, etcd được thiết kế từ đầu để có thể chạy với nhiều nodes. Thiết kế đặc biệt của etcd cho phép tính khả dụng cao (mặc dù không phải 100%) mà không làm mất đi tính nhất quán (thuật toán đồng thuật RAFT).
    - Mô hình dữ liệu của Etcd tường minh và đơn giản, lưu trữ dữ liệu dạng key-value và tổ chức dữ liệu dưới mô hình thư mục phân cấp.
    - Etcd cho phép client theo dõi sự thay đổi cho các specific key 

    -> Etcd phù hợp với K8s do etcd là loại DB vừa Strong Consistency + hiệu xuất cao, đáp ứng yêu cầu hoạt động của K8s.
