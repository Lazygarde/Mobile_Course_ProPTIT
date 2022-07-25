# Activity, Blinding và Intents
## 1.1 Activity
### Tổng quan về Activity
* Activity là một cửa sổ bao quát toàn bộ hoặc một phần màn hình, đây là nơi chúng ta sẽ đặt các UI component lên trên.
* Trong một app android, các Activity sẽ khởi động thay vì là hàm main() như các chương trình funtional
* Một app thường có nhiều Activity và chúng có thể tồn tại song song với nhau và tương tác với nhau.
### Vòng đời của một Activity
* 1 Activity sẽ có các trạng thái: `created`, `started`, `resumed`, `paused`, `stopped`, `restarted`, `destroyed`.
* Một vòng đời của Activity được mô tả dưới sơ đồ như sau:

![](https://developer.android.com/images/activity_lifecycle.png)

|Method | Mô tả | Trạng thái tiếp theo|
|---|---|---|
|onCreate| Được gọi khi Activity được khởi tạo lần đầu. Đây là nơi (nên) thực hiện các setup mặc định như: tạo các View, liên kết dữ liệu,... Phương thức này còn cung cấp cho ta 1 Bundle chứa trạng thái của Activity trước đó (Nếu có) Luôn gọi onStart ngay sau khi thực hiện xong.|onStart|
|onRestart|Được gọi sau khi Activity bị dừng (stopped) trước khi Activity được bắt đầu trở lại. Luôn gọi onStart ngay sau khi thực hiện xong.|onStart|
|onStart|Được gọi khi Activity hiển thị với người dùng. Gọi onResume nếu Activity xuất hiện ở nền trước của máy hoặc gọi onStop nếu nó bị ẩn.|onResume / onStop|
|onResume|Được gọi khi Activity bắt đầu tương tác với người dùng. Tại thời điểm này Activity đang ở trên cùng của Activity Stack.|onPause|
|onPause|Được gọi khi Activity mất trạng thái nền trước (bị thay thế bởi Activity khác) hoặc được gọi trước khi Activity dừng hoặc ẩn với người dùng. Gọi onResume nếu Activity được mang ra nền trước (người dùng back trở lại Activity) Gọi onStop nếu Activity bị ẩn đối với người dùng (không bao giờ được hiển thị lại nữa). Gọi onCreate nếu Activity bị hủy (do hệ thống) nhằm mục đích giải phóng tài nguyên máy.|onResume / onStop /onCreate|
|onStop|Được gọi khi Activity không còn được hiển thị lại với người dùng nữa. Gọi onRestart nếu Activity bị hủy (do hệ thống) nhằm mục đích giải phóng tài nguyên máy. Gọi onDestroy nếu Activity dừng hoạt động.|onRestart / onDestroy|
|onDestroy|Được gọi khi Activity dừng hoạt động hoặc lập trình viên gọi finish() hoặc vì hệ thống hủy Activity để giải phóng bộ nhớ.|nothing|
