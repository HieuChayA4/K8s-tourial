## Volumes

- Kubernetes hỗ trợ nhiều loại Volumes:awsElasticBlockStore, azureDisk, azureFile,cephfs,cinder,configMap,csi,downwardAPI,emptyDir,fc (fibre channel),flexVolume,flocker,gcePersistentDisk,gitRepo (deprecated),glusterfs,hostPath,iscsi,local,nfs,persistentVolumeClaim,projected,portworxVolume,quobyte,rbd,scaleIO,secret,storageos,vsphereVolume

Ví dụ về một số loại Volumes
#### configMap
- configMap cung cấp 1 cách để đưa dữ liệu cấu hình vào trong Pod. Dữ liệu lưu trữ trong ConfigMap object có thể được tham chiếu trong volume có type là configMap và sau đó được sử dụng bởi các ứng dụng container đang chạy bên trong 1 pod.

- Khi tham chiếu 1 configMap object, đơn giản ta chỉ cần cung cấp tên của nó trong volume để tham chiếu đến nó. Ta cũng có thể tùy chỉnh path để sử dụng 1 mục cụ thể trong configMap. Ví dụ để mount ConfigMap có tên log-config vào trong 1 pod có tên configmap-pod, ta có thể sử dụng file yaml sau:

```
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
    - name: test
      image: busybox
      volumeMounts:
        - name: config-vol
          mountPath: /etc/config
  volumes:
    - name: config-vol
      configMap:
        name: log-config
        items:
          - key: log_level
            path: log_level
```
- ConfigMap có tên log-config được mount như là 1 volume và tất cả nội dung được lưu trữ trong mục log_level của nó được mount vào trong pod tại đường dẫn /etc/config/log_level. Lưu ý rằng đường dẫn này được trích xuất từ mountPath của volume và path có key là log_level.

#### emptyDir
- Một *emptyDir* volume đầu tiên được tạo ra khi 1 pod được phân bổ vào 1 Node và *emptyDir* sẽ tồn tại chừng nào pod vẫn còn chạy trên node đó. Như tên của nó, nó sẽ được khởi tạo là trống rỗng. Tất cả các container trong pod có thể đọc và ghi vào cùng 1 file trong emptyDir volume cho dù volume đó có thể được mount vào cùng path hoặc khác path trong mỗi container. *Khi 1 pod bị xóa khỏi node vì bất kỳ lý do gì, dữ liệu trong emptyDir sẽ bị xóa vĩnh viễn*. Một Container đang bị lỗi sẽ không dẫn đến việc xóa Pod khỏi node, do đó dữ liệu trong emptyDir volume được xem là an toàn khi Container có sự cố.
- Một số trường hợp sử dụng cho emptyDir:

    - Không gian lưu trữ nháp (scratch space) ví dụ như để thực hiện việc sắp xếp trộn (merge sort) dựa trên disk.
    - Lưu trữ bản sao tạm thời (checkpointing) cho 1 phép tính tốn nhiều thời gian để có thể phục hồi khi có sự cố
    - Lưu giữ các file mà 1 container phục vụ nội dung nạp vào (lấy về) trong khi 1 webserver container khác phục vụ data.

- Mặc định, *emptyDir* volume được lưu trữ trên bất kỳ phương tiện (medium) nào đang được sử dụng bởi Node. Đó có thể là HDD, SSD hoặc network storage. Tuy nhiên ta có thể thiết lập trường emptyDir.medium thành Memory để nói Kubernetes mount với tmpfs (RAM-backed filesystem). Dù tmpfs có tốc độ nhanh nhưng tmpfs sẽ bị xóa khi node khởi động lại và bất kỳ file nào được lưu vào sẽ được tính vào giới hạn bộ nhớ của Container.

```
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {}
```
#### hostPath

- Một hostPath volume sẽ mount 1 file hoặc 1 thư mục từ filesystem trên node host vào trong pod. Ngoài thuộc tính path bắt buộc, user có thể chỉ định tùy chọn type cho hostPath volume.

```
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      # vị trí thư mục trên host
      path: /data
      # trường này là tùy chọn
      type: Directory
```
#### local 

- Một *local* volume đại diện cho 1 thiết bị lưu trữ cục bộ đã được mount vào hệ thống, chẳng hạn như disk, partition hoặc directory.

- *local* volume chỉ có thể được sử dụng như là *PersistentVolume* được tạo tĩnh. Tính năng Dynamic provisioning (tạo tự động) vẫn chưa được hỗ trợ.

- So với *hostPath* volume thì *local* volume có thể được sử dụng theo cách lâu dài (durable) và có thể di chuyển được (portable) mà không cần phải lập lịch thủ công để đưa pod vào node vì hệ thống biết được các ràng buộc của volume trên node bằng cách nhìn vào node affinity trên PersistentVolume.

- Tuy nhiên, *local* volume vẫn còn phụ thuộc vào sự sẵn sàng của node bên dưới và không phù hợp với tất cả ứng dụng. Nếu 1 node không **healthy** thì *local* volume cũng không thể truy cập được và pod sử dụng nó cũng sẽ không thể chạy được. Các ứng dụng sử dụng *local* volume phải có khả năng chịu được việc giảm/mất tính durable cũng như khả năng bị mất dữ liệu vì phải phụ thuộc vào thiết bị lưu trữ cục bộ.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 100Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: local-storage
  local:
    path: /mnt/disks/ssd1
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - example-node
```
- *Node affinity* là một thuộc tính của Pod để thu hút chúng vào một tập hợp các node (có thể là tùy chọn hoặc yêu cầu cứng).
- *PersistentVolume nodeAffinity* là  trường bắt buộc khi sử dụng *local* volume. Nó cho phép Kubernetes Scheduler lập lịch các pod một cách chính xác đến đúng node sử dụng *local* volume.
- Khi sử dụng local volume, ta nên tạo StorageClass với trường *volumeBindingMode* thiết lập thành *WaitForFirstConsumer*. Việc trì hoãn *volume binding* đảm bảo rằng việc quyết định *binding PersistentVolumeClaim* cũng sẽ được đánh giá cùng với bất kỳ ràng buộc node nào khác của Pod (pod có thể có các điều kiện để lựa chọn node cho riêng mình) ví dụ như yêu cầu tài nguyên của node, node selectors, Pod affinity, và Pod anti-affinity.

#### nfs

- Một *nfs* volume cho phép 1 NFS (Network File System) hiện có được mount vào trong pod. Không giống *emptyDir*, sẽ bị xóa khi pod bị xóa, nội dung của nfs volume sẽ được giữ lại và volume chỉ bị unmounted. Điều này có nghĩa là 1 NFS volume có thể được chuẩn bị sẵn data và data đó có thể chuyển giao (handed off) giữa các pod. NFS có thể được mount bởi nhiều writer cùng một lúc.

Tìm hiểu thêm các loại volume khác tại: [K8s Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)

