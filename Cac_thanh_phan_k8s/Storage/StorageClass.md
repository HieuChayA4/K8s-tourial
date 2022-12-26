## Storage CLass

- **StorageClass** cung cấp 1 cách để người quản trị mô tả “classes” của hệ thống lưu trữ được cung cấp. Các class khác nhau có thể ánh xạ (map) với các mức chất lượng dịch vụ (QoS) khác nhau hoặc ánh xạ đến chính sách dự phòng (backup policy) hoặc các chính sách tùy ý được xác định bởi người quản trị cluster

### Resource của Storage class

- *StorageClass* cũng là một tài nguyên của CLuster giống như node và PV. Mỗi *StorageClass* chứa các trường *provisioner, parameters, và reclaimPolicy* được sử dụng khi một PV thuộc về class cần được dynamic provisioned
- Tên của **StorageClass** là cực kỳ quan trọng, đó là thông tin để người dùng yêu cầu một *Class* lưu trữ trên hệ thống. Người quản trị có thể cấu hình tên và các tham số của **StorageClass** khi tạo, và không thể thay đổi sau khi tạo xong.

- Ex: Nếu trên hệ thống của bạn cài cả storage class dùng nfs và longhorn thì bạn có thể tạo ra các storage class tương ứng với loại storage nêu như:

	- nfs-*StorageClass*: Sẽ sử dụng nfs làm storage system và tạo các volume trên đó
	- longhorn-*StorageClass*: Sẽ sử dụng longhorn làm storage system và tạo các volume trên đó. 
	- longhorn-single-*StorageClass*: Chỉ định storage class dùng longhorn storage với cấu hình volume replicas=1
	- longhorn-ha-*StorageClass*: Chỉ định storage class dùng longhorn storage với cấu hình volume replicas=3 để đảo bảm high availibility
```
apiVersion: storage.k8s.io/v1
kind: *StorageClass*
metadata:
  name: example-nfs
provisioner: example.com/external-nfs
parameters:
  server: nfs-server.example.com
  path: /share
  readOnly: "false"
reclaimPolicy: Retain
``` 
### Provisioner 
- Mỗi *StorageClass* có 1 Trình chuẩn bị (Provisioner) quyết định volume plugin nào được sử dụng để chuẩn bị PV. Trường này phải được chỉ định : AWSElasticBlockStore, AzureFile, FC, NFS,... 
  
### Reclaim Policy
- Các PV được tự động tạo bởi **StorageClass** sẽ có reclaim policy sẽ có *Reclaim policy* được chỉ định trong trường *reclaimPolicy* của class. Giá trị có thể là *Delete* hoặc *Retain*. Nếu không chỉ định trường *reclaimPolicy* khi 1 *StorageClass* object được tạo ra thì mặc định sẽ là *Delete*.

### Cho phép mở rộng Volume
- Các PV có thể được cấu hình để có thể mở rộng được. Tính năng này khi được thiết lập là true sẽ cho phép user thay đổi kích thước của volume bằng cách chỉnh sửa PVC object tương ứng.
- Các loại volume sau hỗ trợ việc mở rộng volume khi **StorageClass** bên dưới có trường **allowVolumeExpansion** được thiết lập là true. 
  
| Loại Volume | Phiên bản Kubernetes |
| ----------- | ----------- |
|gcePersistentDisk |	1.11 |
|awsElasticBlockStore |	1.11 |
|Cinder |	1.11 |
|rbd |	1.11 |
|Azure File	| 1.11 |
|Azure Disk	| 1.11 |
|Portworx |	1.11 |
|FlexVolume |	1.13 |
|CSI	| 1.14 (alpha), 1.16 (beta) aaa |

### Mount Option

- Các PV được tự động tạo ra bởi 1 *StorageClass* sẽ có các mount option được chỉ định trong trường *mountOptions* của class.

- Nếu volume plugin không hỗ trợ các mount option nhưng *mountOptions* vẫn được chỉ định thì quá trình chuẩn bị *provision* sẽ bị thất bại. Các mount option không được kiểm tra trên cả class hay PV vì thế việc mount PV chỉ đơn giản là bị thất bại nếu có tùy chọn mount không hợp lệ.

### Chế độ liên kết Volume

- Trường *volumeBindingMode* kiểm soát khi nào thì việc liên kết volume và dynamic provisioning diễn ra.

- Mặc định, chế độ *Immediate* cho biết rằng việc liên kết volume và dynamic provisioning diễn ra ngay khi PVC được tạo. 

- Chế độ *WaitForFirstConsumer*, việc liên kết hay provisioning của 1 PV chỉ xảy ra khi 1 Pod sử dụng PVC được tạo ra. PV sẽ được lựa chọn hoặc được provisioned phù hợp. Nó có thể bao gồm yêu cầu về *tài nguyên, node selectors, pod affinity và anti-affinity, và taints và tolerations*.

- Các plugin sau hỗ trợ *WaitForFirstConsumer* với dynamic provisioning:AWSElasticBlockStore, GCEPersistentDisk, AzureDisk

- Các plugin sau hỗ trợ *WaitForFirstConsumer* với liên kết PV đã được tạo trước: AWSElasticBlockStore, GCEPersistentDisk,AzureDisk, Local

-> Các plugin khác thì sao ?

[Đọc thêm về các tham số của StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/#parameters)