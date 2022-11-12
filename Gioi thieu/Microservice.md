- Microservice là một dạng kiến trúc phần mềm 
- Các module trong phần mềm được chia thành các service rất nhỏ (*microservice*)
- Mỗi service được đặt trên một server riêng để có thể dễ dàng nâng cấp và scale ứng dụng

![](https://i.imgur.com/HSwddv3.png)

- Trước đây ứng dụng được đóng gói thành 1 khối tổng thể chặt chẽ - *Monolithic* -> Khó khăn upgrade, modify, maintance , nguy cơ one point failure. 
- Microservice chia 1 ứng dụng lớn thành các dịch vụ nhỏ khác nhau: 
    - Dễ nâng cấp và scale cho từng dịch vụ cụ thể, không ảnh hưởng các dịch vụ khác
    - Phục hồi hệ thống nhanh chóng khi có một service không hoạt động
    - Có thể xây dựng mỗi server trên một ngôn ngữ khác nhau 
    - Khi xây dựng ứng dụng dễ dàng trong việc chia workloads
- Tuy nhiên Microservice vẫn có những nhược điểm riêng:
    - Cần chú ý tốc độ giao tiếp giữa các service
    - Chia ứng dụng thành nhiều service quá nhỏ dẫn tới việc khó kiểm soát
    - Mỗi service có db riêng, cách hoạt động riêng -> khó đồng nhất
    - Kiến thức triển khai, vận hành hệ thống microservice

Đọc thêm về *Microservice* tại [đây](https://microservices.io)

