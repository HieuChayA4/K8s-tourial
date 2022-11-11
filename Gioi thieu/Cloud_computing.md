### Điện toán đám mây là gì :grey_question:

- Điện toán đám mây là mô hình cung cấp các dịch vụ điện toán qua nhà cung cấp dịch vụ (AZURE, AWS, GCP,...). Các dịch vụ có thể cấu hình được bao gồm máy chủ, lưu trữ, cơ sở dữ liệu, mạng, ... thông qua nhà cung cấp dịch vụ

- Người dùng trả tiền cho các tài nguyên điện toán được sử dụng

- Điện toán đám mây bao gồm 5 đặc điểm, ba mô hình dịch vụ và bốn mô hình triển khai

#### Đặc điểm của điện toán đám mây
- On-demand self-service: Người dùng có thể tự cung cấp quản lý các dịch vụ điện toán khi cần thiết mà không cần tương tác với các vendor dịch vụ (VD: Tạo máy chủ ảo trên AWS tùy ý người dùng mà k cần liên hệ bất cứ nhà cung cấp máy chủ nào so với dựng máy chủ ở local)
- Broad network access:  Bất cứ thiết bị nào như điện thoại, laptop, desktop… đều có thể dễ dàng truy cập.
- Resource pooling: Các tài nguyên điện toán được tạo thành các pool để chia sẻ với nhiều người dùng khác nhau một cách linh động. Người dùng không có quyền kiểm soát hoặc không biết về vị trí chính xác của tài nguyên được cung cấp (VD: Region, Availability zone)
- Rapid elasticity: Là khả năng các tài nguyên có thể được cung cấp một cách linh hoạt. Một số trường hợp có thể tự động co giãn nhanh chóng theo nhu cầu sử dụng. (VD: S3,...)
- Measured service: khả năng của dịch vụ cloud được tối ưu cho dung lượng sử dụng của khách hàng và sẽ được báo cáo thường xuyên. Các tài nguyên sử dụng sẽ được monitor, kiểm soát, và báo cáo một cách transparent cho cả người dùng và nhà cc định kỳ. (VD: Cost and Usage Report của AWS)

#### Mô hình dịch vụ
- Infrastructrure as a Service, hay IaaS: là một dịch vụ cho phép người dùng sử dụng cơ sở hạ tầng công nghệ thông tin để xây dựng hệ thống, ví dụ như hệ điều hành, máy chủ, hệ thống mạng,… trực tuyến để có thể triển khai và chạy phần mềm, hệ điều hành và ứng dụng trên đó. Người dùng không quản lý và điều khiển các phần hạ tầng phía dưới của nhà cung cấp mà chỉ cho phép người dùng tùy chỉnh đầy đủ các tài nguyên ảo hóa đó để phù hợp với nhu cầu cụ thể của họ. Về cơ bản, nó cung cấp một cơ sở hạ tầng điện toán hoàn toàn ảo hóa được cung cấp và quản lý qua internet.

- Platform as a Service - PaaS: là dịch vụ cung cấp các công cụ phần cứng và phần mềm – thường là những công cụ cần thiết để phát triển ứng dụng – cho người dùng qua internet. Một nhà cung cấp PaaS lưu trữ phần cứng và phần mềm trên cơ sở hạ tầng của riêng mình. Người dùng nhận được không chỉ là hạ tầng mà còn là các công cụ phục vụ cho việc phát triển sản phẩm của họ. Một số ví dụ Cloud Computing dưới hinh thức PaaS như AWS Elastic Beanstalk

- Software as a Service - SaaS:  Là khả năng cung cấp cho người dùng  sử dụng các ứng dụng chạy trên hạ tầng cloud của nhà cung cấp. Ứng dụng có thể được truy cập qua nhiều phương thức như PC, mobile, trình duyệt,... nhà cung cấp SaaS làm tất cả. Từ việc quản lý cơ sở hạ tầng, hệ điều hành, phần mềm trung gian và dữ liệu cần thiết để cung cấp chương trình, đảm bảo rằng phần mềm luôn sẵn sàng mọi lúc mọi nơi mà khách hàng cần. VD: Microsoft Office 365, Salesforce, Cisco WebEx, Google Apps,...

#### Các mô hình triển khai

- Private cloud: được thiết lập qua mạng nội bộ riêng biệt. Các doanh nghiệp có thể trực tiếp quản lý “Cloud” và sử dụng nội bộ . Private cloud cho phép tùy chỉnh và kiểm soát tài nguyên chuyên dùng trên cơ sở hạ tầng máy tính được lưu trữ tại chỗ. Người dùng có toàn quyền kiểm soát, vận hành và bảo trì hệ thống

- Public cloud:  một dịch vụ cung cấp tài nguyên điện toán thông qua môi trường Internet và cơ sở hạ tầng (máy chủ) hoặc ứng dụng của khách hàng được đặt tại trung tâm dữ liệu của nhà cung cấp điện toán đám mây. Khách hàng không thể thấy cũng như kiểm soát được vị trí các dịch vụ mình sử dụng. Các tài nguyên về hạ tầng được chia sẻ giữa nhiều khách hàng với nhau nhưng dữ liệu thì hoàn toàn tách biệt và chỉ có khách hàng mới có thể truy xuất được.\

- Community cloud: một dịch vụ đám mây được chia sẻ giữa nhiều tổ chức, doanh nghiệp trong cùng một cộng đồng hoặc có chung một mối quan tâm nào đó. Dịch vụ này được quản lý bởi một bên thứ ba hoặc quản lý nội bộ giữa các tổ chức này.

- Private cloud: Là sự kết hợp của private cloud và public cloud. Cho phép ta khai thác điểm mạnh của từng mô hình cũng như đưa ra phương thức sử dụng tối ưu cho người sử dụng. Những “đám mây” này thường do doanh nghiệp tạo ra và việc quản lý sẽ được phân chia giữa doanh nghiệp và nhà cung cấp điện toán đám mây công cộng. s