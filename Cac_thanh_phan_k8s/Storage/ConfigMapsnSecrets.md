### ConfigMaps
- Kubernetes cung cấp một đối tượng gọi là ConfigMap cho phép lưu trữ các cấu hình bên ngoài Pod. Đồng thời cho phép nạp cấu hình tự động vào run-time ở Pod.
- ConfigMaps thường được sử dụng để lưu trữ dữ liệu cấu hình non-sensitive,  như:
    - Biến môi trường
    - Các tệp cấu hình
    - Hostnames
    - Service ports
    - Account names

- ConfigMap là tập các ánh xạ của các cặp key/value , mỗi cặp key/value là một *entry*.
    - **Key** là một tên tùy ý có thể được tạo từ chữ và số, dấu gạch ngang, dấu chấm và dấu gạch dưới
    - **Value** là giá trị mong muốn của **Key**, có thể chứa bất cứ thứ gì.
    - Key và Value ngăn cách nhau bơi dấu : - **key:value**
- Sau khi dữ liệu được lưu trữ trong ConfigMap, dữ liệu có thể được đưa vào containers thông qua :
    - Biến môi trường
    - Các Arguments cho câu lệnh khi khởi động container
    - Các tệp trong volume

![](https://i.imgur.com/1Cpc3za.png)

- Tạo một ConfigMap trong Kubernetes rất đơn giản. Có thể tạo một tệp YAML với thông tin cấu hình và sử dụng lệnh kubectl apply để tạo ra ConfigMap, đồng thời có thể tạo Secrets từ dòng lệnh bằng cách sử dụng lệnh kubectl create secret.

- Tạo ConfigMap qua câu lệnh: 
![](https://i.imgur.com/zCOgMyP.png)
- Xem thông tin CM đã tạo: 
![](https://i.imgur.com/1ptKHhH.png)
![](https://i.imgur.com/rhx1fSA.png)

*các object ConfigMap không có khái niệm về state (desired state và actual state) 
- Tạo ConfigMap qua file YAML : 
![](https://i.imgur.com/Bk6tlFY.png)
![](https://i.imgur.com/rBJCcFo.png)

### Đưa thông tin CM vào Pods và Containers

- Có 3 cách chính để đưa dữ liệu ConfigMap vào một Container: 
    - Biến môi trường
    - Qua câu lệnh start up container
    - Các file trong volume

#### Biến môi trường
- Khi một ConfigMap được tạo và các entry của nó được ánh xạ vào các biến môi trường trong container section của Pods template, các biến môi trường xuất hiện trong container như các biến môi trường trong Linux và Window.

![](https://i.imgur.com/tPCZYX4.png)

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    chapter: configmaps
  name: envpod
spec:
  containers:
    - name: ctr1
      image: busybox
      command: ["sleep"]
      args: ["infinity"]
      env:
        - name: FIRSTNAME
          valueFrom:
            configMapKeyRef:
              name: testmap2    -> CM testmap2
              key: given        -> key tham chiếu
        - name: LASTNAME
          valueFrom:
            configMapKeyRef:
              name: testmap2
              key: team
```
- Kiểm tra biến môi trường đã được truyền vào Pod: 

![](https://i.imgur.com/3Wa7TbH.png)

### Qua câu lệnh start up container

```
apiVersion: v1
kind: Pod
metadata:
  name: startup-pod
  labels:
    chapter: configmaps
spec:
  restartPolicy: OnFailure
  containers:
    - name: args1
      image: busybox
      command: [ "/bin/sh", "-c", "echo First name $(FIRSTNAME) last name $(LASTNAME)", "wait" ] -> Startup command 
      env:
        - name: FIRSTNAME
          valueFrom:
            configMapKeyRef:
              name: multimap
              key: given
        - name: LASTNAME
          valueFrom:
            configMapKeyRef:
              name: multimap
              key: family
```
- Kiểm tra log container khi Pod startup: 

![](https://i.imgur.com/QbQuBAd.png)

- Thông tin Environment: 
![](https://i.imgur.com/rproHYb.png)

### ConfigMaps và Volumes

- Sử dụng ConfigMaps với volumes là lựa chọn linh hoạt nhất, có thể tham chiếu toàn bộ các file cấu hình cũng như tạo các cập nhật tới ConfigMap và tác dụng lên các container đang chạy. Sau khi triển khai container, toàn bộ các thay đổi cập nhật của ConfigMap có thể nhìn thấy được trong container và sẵn sàng cho các ứng dụng đang chạy.

```
apiVersion: v1
kind: Pod
metadata:
  name: cmvol
spec:
  volumes:
    - name: volmap -> tên volume
      configMap:
        name: testmap2 -> tham chiếu CM
  containers:
    - name: ctr
      image: nginx
      volumeMounts: -> mount volume vào đường dẫn trong container
        - name: volmap
          mountPath: /etc/name
```
- Show file trong thư mục đã mount volume: 

![](https://i.imgur.com/z7gFfOZ.png)

- Thư mục được cập nhật khi thay đổi ConfigMap: 

![](https://i.imgur.com/efScn1U.png)


### Secrets
- Secrets là một tính năng trong Kubernetes để quản lý dữ liệu bí mật, ví dụ như mật khẩu, token, .... Kubernetes không mã hoá Secrets, mà chỉ đổi chúng thành giá trị base64. Tuy nhiên, có thể cấu hình mã hoá encryption-at-rest bằng EncryptionConfiguration object và hầu hết các service mesh mã hoá network traffic
- Flow của một Secret thường như sau: 
	- Secrets được tạo và lưu trữ như một đối tượng không được mã hoá, sau đó chuyển qua mạng không được mã hoá đến node và gắn vào container dưới dạng plain text để có thể sử dụng bởi ứng dụng
	- Secret được mount vào container qua *tmpfs* và giải mà từ base64 về plaintext sau đó được consume bởi ứng dụng
	- Khi Pod bị xóa, Secret bị xóa khỏi node.
- Secrets cũng giới hạn kích thước là 1MiB
- Các Secrets có thể được sử dụng trong các Pod như một volume được gắn vào một thư mục hoặc được truyền vào trực tiếp cho các biến môi trường. Nó cũng có thể được truyền từ các container tới container trong cùng một Pod hoặc từ một Pod tới Pod khác.
- Tạo một Secrets trong Kubernetes rất đơn giản. Có thể tạo một tệp YAML với dữ liệu cần được bảo mật và sử dụng lệnh kubectl apply để tạo ra Secrets, đồng thời có thể tạo Secrets từ dòng lệnh bằng cách sử dụng lệnh kubectl create secret.
- Trong tất cả, Secrets là một tính năng quan trọng trong Kubernetes để giữ dữ liệu bí mật an toàn và đảm bảo rằng chỉ các Pods cần thông tin mới có thể truy cập được.

### Secrets in Pods
```
apiVersion: v1
kind: Pod
metadata:
name: secret-pod
labels:
topic: secrets
spec:
volumes:
- name: secret-vol
secret:
secretName: tkb-secret
containers:
- name: secret-ctr
image: nginx
volumeMounts:
- name: secret-vol
mountPath: "/etc/tkb"
```

- Đoạn Config trên cho thấy cách đưa Secret vào Pod trong Kubernetes bằng cách sử dụng Secret volume. Mã YAML để tạo một single-container Pod với Secret volume , với ổ đĩa *secret-vol* dựa trên một *Secret* được tạo trước đó có tên là "tkb-secret". *secret-vol* được gắn vào container tại đường dẫn  "/etc/tkb".

### Summary

- ConfigMaps và Secrets là native way của Kubernetes để tách riêng các ứng dụng và dữ liệu cấu hình. Chúng đều là đối tượng trong API của Kubernetes và có thể được tạo và thao tác bằng các lệnh kubectl apply, kubectl get và kubectl describe thông thường. 
- ConfigMaps được thiết kế cho các tham số cấu hình của ứng dụng và nếu cần thì toàn bộ các tệp cấu hình, trong khi Secrets được thiết kế cho dữ liệu cấu hình nhạy cảm. Cả hai có thể được nhúng vào các container tại run-time với việc sử dụng volumes là cách ưa dùng, vì nó cho phép cập nhật được thể hiện trong các container đang chạy. Tuy nhiên, Secrets không được mã hóa theo mặc định trong cluster store hoặc khi truyền trên mạng.
