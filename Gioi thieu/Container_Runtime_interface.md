Đọc thêm về CRI: [CRI](https://www.cloudnativeviet.net/post/2020/12/20/introduce-cri)
## Cách Kubernetes quản lý containers trong cluser
- để làm việc với container trên từng worker node, kubernetes dùng một công cụ tên là kubelet.
- kubelet chịu trách nhiệm tương tác với master node và quản lý container bên trong kubernetes cluster. Bên cạnh đó kubelet còn giám sát container ở trên worker node và gửi thông tin đó về master node.
- Nó sẽ lắng nghe thông báo từ master node về cấu hình của container mà được phân phối tới worker node, sau đó nó sẽ tạo container tương ứng với cấu hình đó lên trên worker node.

![](https://i.imgur.com/n0G2jRf.png)

## Container Runtime và Container Runtime Interface

- Container runtime là một công cụ đóng vai trò quản lý, giúp tạo và xóa container một cách dễ dàng, thay vì phải tạo container bằng hàng loạt câu CLI phức tạp.
- kubelet sẽ tương tác với container runtime ở trên worker node thông qua Container Runtime Interface (CRI).
- ![](https://i.imgur.com/LraNLxc.png)
- CRI đóng vai trò làm trung gian cho việc kubelet tương tác với các loại container runtime khác nhau (docker, cri-o, containerd, …), kubelet chỉ giao tiếp với container runtime thông qua một tập lệnh  và CRI sẽ chịu trách nhiệm tương tác với các container runtime 
![](https://i.imgur.com/Dq1TxeM.png)\
1. kubernetes master node báo cho kubelet tạo container.
2. kubelet giao tiếp với high level container runtime (cri-o, containerd, …) thông qua CRI và yêu cầu tạo container.
3. high level container runtime sẽ pull image từ container image registry xuống, extract nó ra và lưu xuống disk.
4. low level container runtime được thông báo và nó nhảy vào disk để lấy container image để tạo container.
5. Sau khi lấy được container image thì low level container runtime chạy lệnh cho linux kernel để tạo container.