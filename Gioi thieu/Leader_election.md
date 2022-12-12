- Khi triển khai một HA cluster K8S chúng ta sẽ cần stack nhiều Kubernetes Controller Manager, Scheduler, hoặc etcd để tạo một kiến trúc High Avaibility. Khi đó việc bầu chọn ra một *Leader* của mỗi thành phần là việc cần thiết
=> Kiểm tra file *config.yaml* chúng ta có thể thấy không có flag nào biểu thị cho hành động này. Vậy các thành phần này bầu chọn ra một leader như thế nào? 

![HA architect K8s exampler](https://i.imgur.com/YxZfSDu.png)

-  *Leader election* là một cơ chế đảm bảo rằng chỉ một instance của kube-scheduler hoặc một instance của kube-controller-manager đang active, trong khi các instance khác ở trạng thái inactive nhưng luôn sẵn sàng take leadership khi *leader* bị lỗi.

-  Trích dẫn: https://kubernetes.io/blog/2016/01/simple-leader-election-with-kubernetes/

```
## Implementing leader election in Kubernetes
The first requirement in leader election is the specification of the set of candidates for becoming 
the leader. 
Kubernetes already uses Endpoints to represent a replicated set of pods that comprise a service, so we will re-use this same object. (aside: You might have thought that we would use ReplicationControllers, but they are tied to a specific binary, and generally you want to have a 
single leader even if you are in the process of performing a rolling update)
To perform leader election, we use two properties of all Kubernetes API objects:
    - ResourceVersions - Every API object has a unique ResourceVersion, and you can use these versions to perform compare-and-swap on Kubernetes objects
    - Annotations - Every API object can be annotated with arbitrary key/value pairs to be used by clients.
```
- Có thể thấy rằng *leader election* trong K8s dựa vào *endpoints*. Trong K8S bất kỳ service nào có leader sẽ tạo ra một *Endpoint* với một *Annotation* chỉ ra leadership ở service đó. 

```
> kubectl describe ep -n kube-system kube-controller-manager
Name:         kube-controller-manager
Namespace:    kube-system
Labels:       <none>
Annotations:  control-plane.alpha.kubernetes.io/leader:
                {"holderIdentity":"controller-0","leaseDurationSeconds":15,"acquireTime":"2022-12-10T13:12:57Z","renewTime":"2022-12-10T13:30:31Z","leaderTransitions":0}
Subsets:
Events:  <none>
```
- Ở đoạn code trên có thể thấy rằng có một endpoint annotation *(Annotations: control-plane.alpha.kubernetes.io/leader)* chỉ ra rằng instance với holderIdentity là 2c4f5e3-e453-... đang là leader.
	- Leader hiện tại: “holderIdentity”: “controller-0”
	- Thời điểm leader được chọn: "acquireTime":"2022-12-10T13:12:57Z"
	- Thời gian của leader : “leaseDurationSeconds”: 15
	- Thời điểm renew leader hiện tại: "renewTime":"2022-12-10T13:30:31Z" -> nếu không thể renew, các inactive instance sẽ có quyền chiếm leadership
	- Số lần đã thay đổi leader: "leaderTransitions":0

- Việc bầu chọn ra leader cụ thể hơn ở sourcecode: [k8s leader election](https://github.com/kubernetes/client-go/blob/master/tools/leaderelection/leaderelection.go#L403)
```
// setObservedRecord will set a new observedRecord and update observedTime to the current time.
// Protect critical sections with lock.
func (le *LeaderElector) setObservedRecord(observedRecord *rl.LeaderElectionRecord) {
	le.observedRecordLock.Lock()
	defer le.observedRecordLock.Unlock()

	le.observedRecord = *observedRecord
	le.observedTime = le.clock.Now()
}

// getObservedRecord returns observersRecord.
// Protect critical sections with lock.
func (le *LeaderElector) getObservedRecord() rl.LeaderElectionRecord {
	le.observedRecordLock.Lock()
	defer le.observedRecordLock.Unlock()

	return le.observedRecord
```

=> Một instance trong một k8s service giành quyền leader bằng cách tạo một *endpoint* và thêm annotation *(control-plane.alpha.kubernetes.io/leader) *. Service enpoint được sử dụng như một **LOCK** để ngăn chặn việc tạo một *endpoint* như vậy khác trong cùng một **Namespace**