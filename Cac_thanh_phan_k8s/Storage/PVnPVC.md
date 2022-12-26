- PersistentVolume subsystem cung cấp API cho user và admin các đóng gói chi tiết cách hệ thống lưu trữ hoạt động. Để làm được điều này, ta có 2 tài nguyên API: PersistentVolume và PersistentVolumeClaim.
- PersistentVolume (PV) là 1 phần của hệ thống lưu trữ trong cluster đã được chuẩn bị trước bởi admin hoặc được provision tự động (dynamic provision) trước bằng cách sử dụng *storage class*.
- PersistentVolumeClaim (PVC) là 1 yêu cầu lưu trữ của user. Nó tương tự như 1 Pod. Trong khi các pod sử dụng tài nguyên của node thì PVC sử dụng tài nguyên của PV. Các pod có thể yêu cầu mức độ cụ thể của tài nguyên (CPU, Memory). PVC có thể yêu cầu dung lượng và chế độ truy cập (access mode) cụ thể (ví dụ chúng có thể được mount 1 một lần đọc/ghi hoặc nhiều lần read only)

### Lifecycle của một volume và claim
