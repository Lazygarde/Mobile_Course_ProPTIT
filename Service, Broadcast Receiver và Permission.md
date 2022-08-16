# Service, Broadcast Receiver và Permission
## 1. Service

### 1.1 Khái niệm
![](https://vncoder.vn/upload/img/lesson/1582860020.jpg)
- Service là một thành phần ứng dụng (application component) có thể thực hiện các hoạt động lâu dài trong background.
- Nó không cung cấp giao diện cho người dùng.
- Sau khi bắt đầu chạy, một Service có thể tiếp tục chạy trong một thời gian, ngay cả khi người dùng chuyển sang ứng dụng khác.
- Ngoài ra một thành phần có thể liên kết (bind) với một Service để tương tác với Service đó, thậm chí là thực hiện giao tiếp giữa các quá trình IPC (interprocess communication).
- Ví dụ: một Service có thể thực hiện các giao dịch mạng, chơi nhạc, ra vào file I/O hoặc tương tác với một content provider, tất cả đều từ background.

### 1.2 Phân loại Service
#### 1.2.1 Foreground Service (Unbound Service)
- Một Foreground Service thực hiện một số hoạt động mà người dùng dễ nhận thấy. Ví dụ một ứng dụng nghe nhạc có thể chơi một bản nhạc và điều khiển (control) nó bằng Foreground Service.

- Một điều bắt buộc là Foreground Service phải hiện thị một Notification. Foreground Service sẽ tiếp tục chạy ngay cả khi người dùng không tương tác với ứng dụng.

- Khi bạn sử dụng foreground service, bạn phải hiển thị thông báo (notification) để người dùng chủ động biết rằng service đang chạy. Không thể loại bỏ thông báo này trừ khi service bị dừng (stopped) hoặc bị xóa khỏi foreground

#### 1.2.2 Background Service (Unbound Service)
- Một Background Service sẽ thực hiện các hoạt động mà người dùng không trực tiếp nhìn thấy. Ví dụ một ứng dụng sử dụng một service để thu gom bộ nhớ chẳng hạn, thì đó là một Background Service.

#### 1.2.3 Bound Service
- Một Service được gọi là Bound Service khi một thành phần của ứng dụng ràng buộc với nó bởi lời gọi bindService().

- Một Bound Service cung cấp một giao diện Client - Server cho phép các thành phần tương tác với nó: gửi yêu cầu, nhận kết quả và thậm chí là IPC.

- Một Bound Service chỉ chạy miễn là có một thành phần ràng buộc với nó. Có thể có nhiều thành phần ràng buộc với Bound Service cùng lúc, nhưng khi tất cả tháo bỏ ràng buộc (unbound) thì Service sẽ bị hủy (destroyed)

### 1.3 Độ ưu tiên các loại Service
- Hệ thống Android bắt buộc phải dừng một Service khi bộ nhớ ít và phải khôi phục tài nguyên hệ thống cho Activity đang được sử dụng. - Nếu Service được ràng buộc với một Activity đang sử dụng, nó ít khả năng bị kill; nếu Service được khai báo và chạy ở chế độ Foreground nó cũng khó biết kill.
- Nếu Service là Started và chạy lâu dài, hệ thống sẽ làm giảm vị trí ưu tiên của nó. Như vậy các loại service sẽ được xếp theo độ ưu tiên sau: Bound Service khó bị kill nhất, tiếp theo là Foreground Service và Background Service.

### 1.4 Service's lifecycle
![](https://vntalking.com/wp-content/uploads/2018/10/vong-doi-service-android.jpg)

#### 1.4.1 onStartCommand()
Hàm này được gọi khi service bắt đầu bằng cách gọi hàm startService(). Khi hàm này thực hiện, service được khởi động và có thể chạy nền background vô thời hạn. Để có thể hủy service thì chỉ có 2 cách:

- Bạn tự gọi hàm stopSelf() hoặc stopService().
- May rủi chờ đến khi hệ thống Android cần tài nguyên cho ứng dụng khác thì sẽ kill service này .
#### 1.4.2 onBind()

Hàm này chỉ được thực hiện nếu service là bound service. Bạn phải cung cấp một giao diện(interface) mà client sử dụng để giao tiếp với Service bằng cách trả về một IBinder. Hàm này được gọi bất kể khi nào có một thành phần nào đó muốn bind vào service

#### 1.4.3 onCreate()

Đây là hàm được khi service được khởi tạo và chỉ được gọi duy nhất một lần
#### 1.4.4 onDestroy()

Tương tự như Activity thì hàm này được khi service bị hủy(có thể do bạn gọi hàm stopService()hay do hệ thống tự hủy) để giải phóng tài nguyên.
### 1.5 Các giá trị trả về trong onStartCommand()
= Tất cả chúng ta đều biết rằng: khi các Service tiếp tục chạy trong  background thì chắc chắn chúng sẽ tiêu tốn một số bộ nhớ để thực thi.

- Vì vậy, khi có nhiều ứng dụng chạy trên thiết bị Android, bộ nhớ thiết bị sẽ dần dần bị chiếm hết , và theo thời gian khi bộ nhớ thiết bị thấp đến mức nghiêm trọng, thì hệ thống Android bắt đầu kết thúc các quá trình (processes), để giải phóng bộ nhớ bị chiếm bởi các quá trình đó.

- Nhưng bạn có thể đang thực hiện một số nhiệm vụ quan trọng với các Service, điều đó cũng có thể bị chấm dứt khi Service ngừng hoạt động. vì vậy những khái niệm này là để cho hệ thống Android biết bạn muốn thực hiện hành động nào khi bộ nhớ thiết bị ổn định trở lại và khi nó sẵn sàng khởi chạy lại các dịch vụ.

#### 1.5.1 START_NOT_STICKY.
Yêu cầu hệ thống không cần khởi động lại dịch vụ, ngay cả khi có đủ bộ nhớ.

#### 1.5.2 START_STICKY.
Yêu cầu hệ thống tạo một bản sao mới của Service, khi có đủ bộ nhớ. Ở đây bạn sẽ mất kết quả mà có thể đã tính toán trước đó

#### 1.5.3 START_REDELIVER_INTENT
Yêu cầu hệ thống khởi động lại dịch vụ sau sự cố và cũng phân phối lại các Intents có tại thời điểm xảy ra sự cố.

#### 1.5.4 START_STICKY_COMPATIBILITY
Giá trị này cũng giống như START_STICKY nhưng nó không chắc chắn, đảm bảo khởi động lại service.

## 3. Permission
### 3.1 Khái niệm
- Permissions là các quyền cần để app thực hiện 1 số việc nhất định nào đó ví dụ như quyền truy cập vị trí, quyền sử dụng máy ảnh,...
- Permissions giúp hỗ trợ quyền riêng tư của người dùng bằng cách bảo vệ quyền truy cập vào những thứ sau:
    + Restricted data: dữ liệu bị hạn chế, chẳng hạn như là trạng thái hệ thống và dữ liệu liên hệ người dùng.
    + Restricted actions: hành động bị hạn chế, chẳng hạn như là kết nối với thiết bị khác hay là record âm thanh.
![image](https://user-images.githubusercontent.com/84316258/184805603-8ca28afc-b0dd-4d45-9f30-7cd2bfc8403b.png)
