- linux namespaces cho phép cách ly tài nguyên hệ thống giữa các tiến trình một cách độc lập
- Linux namespaces cách li một tiến trình, làm cho tiến trình đó xuất hiện như là một tiến trình duy nhất chạy trên máy chủ
- Namespaces là một chức năng mang tính trừu tượng của Linux Kernel , nó chia các kernel resource và phân bổ cho các tiến trình cho các resource đó, khi nói tới namespace thì có nghĩa là các tài nguyên đi kèm là riêng biệt mặc dù tài nguyên đó cũng có thể được sử dụng ở các namespace khác như: ID Process, Host Name, User’s ID, … và một số khác liên quan đến quyền truy cập mạng và giao tiếp giữ các tiến trình với nhau.
- Linux namespaces sẽ bao gồm các thành phần nhỏ hơn như:
	- PID namespace cho phép ta tạo các process tách biệt.
	- Networking namespace cho phép ta chạy chương trình trên bất kì port nào mà không bị xung độ với các process khác chạy trên server.
	- Mount namespace cho phép ta mount và unmount filesystem mà không ảnh hưởng gì tới host filesystem.
	- User namespaces cho phép  một tiến trình sử dụng user và group IDs  trong và ngoài một namespace 
	- Cgroup namespace ảo hóa các nội dung của file /proc/self/cgroup. Tiến trình  trong một cgroup namespace chỉ có thể xem các đường dẫn liên quan đến namespace root của chúng. Cgroups là tính năng của Linux Kernel để giới hạn resource của một process. Cgroups sẽ định ra giới hạn của CPU và Memory mà một process có thể dùng

Container được xây dựng từ một vài tính năng của Linux kernel, trong đó hai tính năng chính là "namespaces" and "cgroups". Đây là hai tính năng của Linux giúp ta tách biệt một process hoàn toàn độc lập với các process còn lại.