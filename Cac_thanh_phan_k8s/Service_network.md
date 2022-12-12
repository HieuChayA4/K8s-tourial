### Service Network
- ClusterIPs nằm trên một *"speacial"* network là *service network*, mặc định không có route đến nó, các containers gửi tất cả ClusterIP traffic đến chúng thông qua *default gateway*.
- Container's default gateway gửi traffic tới node chúng chạy trên đó. Các node cũng không có route tới *service network*, các node cũng gửi các traffic thông qua chính default gateway của nó. 
-  Mõi K8s node chạy một system service gọi là *kube-proxy*. *Kube-proxy* chịu trách nhiệm cho việc bắt các traffic tới ClusterIPs và điều hướng chúng tới các địa chỉ IP của Pod khớp với Service's label selector.
-  Ta đã biết *kube-proxy* là một Pod-based K8s-native app triển khai một controller theo dõi API server về các Services và Endpoints objects mới. Nó sẽ tạo các local IPVS rule chỉ cho node bắt các traffic tới Service's ClusterIP và chuyển tiếp chúng tới các IP của Pod.
- Kubernetes ban đầu sử dụng iptables để thực hiện bẫy traffic và cân bằng tải. Tuy nhiên, nó đã được thay thế bằng IPVS trong Kubernetes 1.11. Lý do là bởi vì IPVS là bộ cân bằng tải L4 dựa trên kernel hiệu suất cao, có khả năng mở rộng tốt hơn iptables và thực hiện cân bằng tải tốt hơn.
- Đọc thêm về [Linux IP Virtual Server (IPVS)](https://cloudcraft.info/loadbalance-gioi-thieu-lvs-ipvs/).
