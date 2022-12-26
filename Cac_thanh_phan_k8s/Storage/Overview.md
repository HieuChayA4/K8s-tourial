## Storage Providers
- Volumes là nơi lưu trữ dữ liệu của của K8s. Một container sinh ra dữ liệu trong quá trình chạy và pod đó bị khởi động lại (restart) thì toàn bộ dữ liệu sinh ra trong quá trình chạy pod sẽ bị mất. Yêu cầu đặt ra đối với không gian lưu trữ các dữ liệu của ứng dụng trong k8s là: 
    - Storage không phụ thuộc vào *pod lifecycle* - storage sẽ không bị mất cho dù xóa pod hay tạo pod mới thay thế
    - Storage có khả năng sử dụng bới mọi node
    - Storage có thể tồn tại cho dù cả Cluster bị crash.
- Vì vậy để dữ liệu của container không bị mất để ta sẽ cần sử dụng đến external storage xuất hiện dưới dạng volumes để gán cho pod. Volumes còn giải quyết được vấn đề chia sẻ dữ liệu giữa những container chạy trong cùng một pod. Nó có thể là bộ nhớ vật lý của server nơi mà pod đang chạy hoặc những external storage nằm ngoài Kubernetes cluster.
- K8s có thể sử dụng storage từ nhiều hệ thống bên ngoài, có thể là các dịch vụ cloud native như EBS của AWS hay Azure file, hoặc cũng có thể là các hệ thống lưu trữ truyền thống trong doanh nghiệp như là iSCSI, FC, NFS,... Tuy nhiên việc sử dụng storage provider khi k8s cluster chạy trên một cloud provider khác nhau là không thể (không thể sử dụng dịch vụ lưu trữ của AWS khi k8s cluster chạy trên Azure ). Mỗi provider cần một CSI plugin để có thể expose các tài nguyên lưu trữ của họ tới Kubernetes như là một volume. Plugin này là **Container Storage Interface (CSI)** và  nó chạy như tập các Pod trong *kube-system* Namespace.

## The Container Storage Interface (CSI)
- Với các Cloud Provider như AWS, Azure, GKE, VMware (cloud nội bộ) thì Kubernetes viết sẵn các đoạn code cho việc kết nối Volume từ Node/Pod của các nhà cung cấp này - *In-tree Cloud* . Tuy nhiên với xu hướng Cloud mở rộng và độ phức tạp cũng như sự khác nhau giữa các Storage của các provider, Kubernetes đã dừng phát triển code kết nối Storage của các provider khác và để provider viết code cho plugin này. Kubernetes vẫn sẽ xử lý phần từ Pod mount vào Container.
- Các plugin hiện đại dựa trên **Container Storage Interface (CSI)**, một tiêu chuẩn mở nhằm cung cấp giao diện lưu trữ cho các hệ thống điều phối containter như Kubernetes, tức là muốn dùng Storage bên ngoài thì phải có interface được cài đặt để giao tiếp với hệ thống hạ tầng máy chủ bên ngoài - *out-of-tree*.    
## Persistent volume subsystem
- Khi các externel storage được kết nối đến K8s cluster thông qua CSI,ta cần *persistent volume subsystem API resources* để tận dụng và sử dụng bộ nhớ trong ứng dụng của mình.

![](https://i.imgur.com/zeCpQQg.png)
- Ba API resource lõi của persistent volume subsystem là: 
    • Persistent Volumes (PV)
    • Persistent Volume Claims (PVC)
    • Storage Classes (SC)

- PersistentVolume (PV) là một phần không gian lưu trữ dữ liệu trong cluster. Chúng đại diện cho cách mà các tài nguyên của external storage xuất hiện trong Kubernetes. Nó là một loại tài nguyên của của Cluster giống như RAM, CPU. K8s không quan tâm đến thực tế storage thực tế là gì mà chỉ cung cấp một persistance volume như là một interface của storage thực tế đã và đang được maintance và quản trị bởi nơi nào đó khác. Việc cần làm là chỉ định loại PV nào Cluster Services hoặc các ứng dụng trong Cluster cần sử dụng để tạo và quản lý thông qua các file YAML. 

![](https://i.imgur.com/WngtkCO.png)
- Pod trong Cluster có thể sử dụng cùng lúc nhiếu loại external storage khác nhau thông qua PV. Các PV này cũng giống như các Volume khác, tuy nhiên nó tồn tại hoàn toàn độc lập với bất kỳ pod nào sử dụng PV.

![](https://i.imgur.com/v6l5UX7.png)
- PV không nằm trong bất kỳ một *Namespace* nào, chúng có thể được truy cập bởi tất cả các Pod từ mọi Namespace trong cluster.

- Một người dùng muốn sử dụng không gian lưu trữ (PV) thì cần tạo một PersistentVolumeClaim (PVC). Nó cấp quyền cho Pod truy cập vào PV. Thông thường người dùng sẽ tạo một manifest PersistentVolumeClaim, chỉ định số lượng, loại lớp lưu trữ (storage class), yêu cầu các mức tài nguyên CPU, bộ nhớ,... Ngoài ra, PVC còn có thể xác định các chế độ quyền truy cập cụ thể vào vùng lưu trữ (ví dụ như: ReadWriteOnce, ReadOnlyMany or ReadWriteMany). Kubernetes sau đó sẽ dựa vào các thông tin này để tìm và dự trữ dung lượng lưu trữ cần thiết.

- Khi một Cluster có nhiều ứng dụng khác nhau tăng thêm và phát sinh ngày càng nhiều nhu cầu lưu trữ cho các ứng dụng này. Việc tạo thêm hàng trăm PV cho các ứng dụng này sẽ gây tốn thời gian và phức tạp. *Storage class* về cơ bản tạo hoặc cung cấp PV một cách linh hoạt khi PVC yêu cầu nó. Bằng cách này, việc tạo hoặc cung cấp volumes trong Cluster có thể được tự động hóa. *Storage class* tạo PV một cách tự động ở background do đó ta cần định nghĩa external storage back-end mà ta sử dụng bằng thuộc tính *provisioner* trong file YAML. *Storage class* giống như một lớp trừu tượng khác, nó trừu tượng hóa phía underlying storage provider cũng như là các tham số cho các đặc tính của loại storage  như là loại disk, systemfile,... 

-- Continue