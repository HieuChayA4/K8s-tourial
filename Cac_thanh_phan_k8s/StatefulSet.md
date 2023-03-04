### StatefulSets

- StatefulSets trong Kubernetes là một kiểu định tuyến của Pod. Nó đảm bảo rằng mỗi Pod của StatefulSet đều có một tên duy nhất và lịch sử chạy liên tục, điều này giúp cho các ứng dụng cần sự giữ nguyên trạng thái như database hay hệ thống tập tin để hoạt động đúng cách. Nó cũng hỗ trợ việc quản lý từng Pod trong StatefulSet như một thực thể riêng biệt với tên riêng, IP riêng và các thiết lập khác.

- StatefulSets đảm bảo các điều sau:

	- Có thể predict được tên Pod và có tính persistent
	- Có thể predict được DNS hostname và có tính persistent
	- Có thể predict được các volume binding và có tính persistent
->Điều này giúp cho các ứng dụng có thể dựa trên tính persistent tên Pod, tên máy chủ DNS và các liên kết volume để hoạt động đúng cách.

- Đoạn YAML snippet dưới đây chỉ ra một số thuộc tính của một StatefulSet điển hình: 

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
    name: tkb-sts       -> tên của StatefulSet
spec:
    selector:
        matchLabels:
            app: mongo
serviceName: "tkb-sts"
replicas: 3             -> số lượng pod replica
template:
    metadata:
        labels:
            app: mongo
spec:
    containers:
    - name: ctr-mongo
        image: mongo:latest -> image 
```
-> Khi đoạn snippet này được post lên API server nó sẽ tồn tại trong cluster store, số lượng replicas được gắn với cluster nodes, và StatefulSet controller sẽ theo dõi trạng thái của cluster đảm bảo rằng *observed state* đúng với *desired state*

#### StatefulSet Pod naming

- Tất các Pod được quản lý bởi StatefulSet đều có  *predictable* và *persistent names*. Những tên này rất quan trọng và là cốt lõi của cách Pods được bắt đầu, tự phục hồi, scaled, xóa, attached to volumes, v.v.
- Định dạng của StatefulSet Pod name là <StatefulSetName>-<Integer>. Pod đầu tiên được tạo bởi StatefulSet luôn luôn có index là "0", và các Pod tiếp theo được đánh số kế tiếp. 

#### Thứ tự tạo và xóa Pod

- StatefulSets tạo một Pod tại một thời điểm, và luôn luôn đợi khi Pod trước đó ở trạng thái *running* trước khi sẵn sàng tạo Pod tiếp theo. 

![](https://i.imgur.com/77Kqkgh.png)

- Việc scaling cũng tuân theo quy luật tương tự. Pod có stt kế tiếp sẽ được tạo trước và Pod có stt cao nhất sẽ bị xóa trước nếu scale down. 

#### Xóa StatefulSet

-  Xóa một StatefulSet sẽ không terminate các Pod theo thứ tự -> Giảm số lượng replica về 0 trước khi xóa StatefulSet

-  Có thể dùng thuộc tính **terminationGracePeriodSeconds** để kiểm soát cách Pod bị terminated. Thường tối thiểu là 10 giấy để các ứng dụng chạy trong Pod có thể xóa toàn bộ bộ nhớ đệm và đảm bảo các tiến trình ghi dữ liệu được hoàn thành.

#### StatefulSets và Volumes

- Khi một StatefulSet Pod được tạo, volume Pod đó cần sẽ được tạo cùng thời điểm và được đặt tên một cách đặc biệt kết nối chúng với đúng Pod. 
- Các Volume được tách rời khỏi Pod thông qua PVC. Volume sẽ có lifecycle tách riêng với Pod cho phép chúng tồn tại khi một Pod fail hoặc terminated. Điều này cho phép Pod thay thế được gắn vào cùng một storage của Pod bị thay thế. 

#### Network ID và headless Services

- Khi một ứng dụng cần kết nối trực tiếp tới một hoặc một số Pod cụ thể, StatefulSet sử dụng **Headless Service** để tạo các **predictable DNS hostnames** cho mỗi Pod replica. Các ứng dụng có thể try vấn DNS cho danh sách các Pod replica và sử dụng nó để kết nối trực tiếp đến Pod. 

```
apiVersion: v1
kind: Service
metadata:
    name: mongo-prod
spec:
    clusterIP: None
    selector:
        app: mongo
        env: prod
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
    name: sts-mongo
spec:
    serviceName: mongo-prod  -> Governing Service
```
-  **Headless Service** là một Kubernetes Service object không có địa chỉ IP (spec.clusterIP đặt là None). Nó sẽ trở thành một StatefulSet's Governing Service khi được chỉ định trong StatefulSet manifest ở thuộc tính spec.serviceName.
- Service sẽ tạo các bản ghi DNS SRV cho mỗi Pod replica khớp với label selector của headless Service. Các Pod và ứng dụng khác có thể tìm thành viên của StatefulSet bằng cách thực hiện DNS lookup tên của headless Service.  

![](https://i.imgur.com/ekIEjSM.png)

#### Rolling updates


- StatefulSet hỗ trợ rolling update. Vd khi update version image trong YAML và đẩy lên API server, sau khi được xác thực, controller thay thế các Pod cũ bằng Pod mới. Tiến trìn sẽ bắt đầu với Pod có stt lớn nhất, lần lượt từng Pod một đến khi tất cả các Pod chạy ở phiên bản mới. 



