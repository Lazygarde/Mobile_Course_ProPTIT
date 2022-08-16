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

## 2. Broadcast Receiver
### 2.1 Khái niệm
- Broadcast Receiver là một trong 4 component lớn trong Android, với mục đích là lắng nghe các sự kiện, trạng thái của hệ thống phát ra thông qua Intent nhờ đó mà các lập trình viên có thể xử lí được các sự kiện hệ thống ở bên trong ứng dụng của mình.

- Broadcast Receiver có thể hoạt động được cả khi ứng dụng bị tắt đi, nghĩa là ở background chính vì vậy nó thường được sử dụng với service.
### 2.2 Đăng kí Broadcast Receiver
#### 2.2.1 Tạo một class Broadcast Receiver
```kt
class MyReceiver:BroadcastReceiver() {
  fun onReceive(context:Context, intent:Intent) {
    // This method is called when this BroadcastReceiver receives an Intent broadcast.
    Toast.makeText(context, "Action: " + intent.getAction(), Toast.LENGTH_SHORT).show()
  }
}
```
- Chúng ta sẽ tạo một class là MyReciever và được kế thừa từ BroadcastReceiver. Do BroadcastReceiver là một abstract class nên chúng ta bắt buộc phải override hàm `onReceiver()`. Bất cứ khi nào có một sự kiện xảy ra, Android sẽ gọi hàm `onReceiver()`.

- Hàm `onReceiver()` có 2 tham số được truyền vào là: Intent và Content. Với Intent, bạn sẽ nhận được những thông tin cần thiết từ hệ thống.
#### 2.2.2 Đăng kí Broadcast Receiver trong Manifest File
```kt
<receiver
    android:name="com.pycitup.pyc.MyReceiver"
    android:enabled="true"
    android:exported="true" >
    <intent-filter>
        <action android:name="com.pycitup.BroadcastReceiver" />
    </intent-filter>
</receiver>
```
- Chúng ta sử dụng thẻ `<receiver>` để đăng kí với một intent filter.

- Ứng dụng sử dụng intent filter để thông báo với hệ thống là ứng dụng của tôi đăng kí nhận thông báo.

- Nhưng không phải nhận tất cả mà chỉ những thông báo nào phù hợp thì mới nhận. Ở đây thì chỉ những thông báo nào có action là: `com.pycitup.BroadcastReceiver` thì mới nhận được

#### 2.2.3 Gửi Broadcast Event/Intent
```kt
val intent = Intent()
intent.addFlags(Intent.FLAG_INCLUDE_STOPPED_PACKAGES)
intent.setAction("com.pycitup.BroadcastReceiver")
intent.putExtra("Foo", "Bar")
sendBroadcast(intent)
```

- Chúng ta tạo một intent, sau đó thiết lập action cho intent này. Ở đây là `com.pycitup.BroadcastReceiver`. Chính là action mà chúng ta đã sử dụng để đăng kí ở phần phía trên bài viết. Dữ liệu sẽ được đưa vào intent thông qua hàm `putExtra()`

- Cuối cùng gửi Broadcast bằng hàm `sendBroadcast()`

- Flag `FLAG_INCLUDE_STOPPED_PACKAGES` cho phép ứng dụng vẫn nhận thông báo trong trường hợp ứng dụng được cài đặt nhưng chưa chạy lần nào hoặc bị tắt bởi công cụ quản lý hệ thống như task manager.

## 3. Permission
### 3.1 Khái niệm
- Permissions là các quyền cần để app thực hiện 1 số việc nhất định nào đó ví dụ như quyền truy cập vị trí, quyền sử dụng máy ảnh,...
- Permissions giúp hỗ trợ quyền riêng tư của người dùng bằng cách bảo vệ quyền truy cập vào những thứ sau:
    + Restricted data: dữ liệu bị hạn chế, chẳng hạn như là trạng thái hệ thống và dữ liệu liên hệ người dùng.
    + Restricted actions: hành động bị hạn chế, chẳng hạn như là kết nối với thiết bị khác hay là record âm thanh.

![](https://user-images.githubusercontent.com/84316258/184805603-8ca28afc-b0dd-4d45-9f30-7cd2bfc8403b.png)
### 3.2 Các loại Permission
#### 3.2.1 Install-time Permissions
Các quyền khi cài đặt cấp cho ứng dụng quyền truy cập có giới hạn vào dữ liệu bị hạn chế. Các quyền này cho phép ứng dụng thực hiện một số hành động bị hạn chế mà ảnh hưởng của các hành động đó đến hệ thống hoặc các ứng dụng khác là rất nhỏ. Khi khai báo quyền khi cài đặt trong ứng dụng, hệ thống sẽ tự động cấp cho ứng dụng các quyền này khi người dùng cài đặt ứng dụng. Cửa hàng ứng dụng sẽ hiện thông báo về quyền khi cài đặt cho người dùng khi họ xem trang chi tiết của ứng dụng, như minh hoạ dưới
![](https://developer.android.com/static/images/training/permissions/install-time.svg)
#### 3.2.2 Runtime Permissions
- Quyền khi bắt đầu chạy (còn gọi là quyền nguy hiểm) cấp cho ứng dụng thêm quyền truy cập vào dữ liệu bị hạn chế. Những quyền này cho phép ứng dụng thực hiện những hành động bị hạn chế có ảnh hưởng đáng kể đến hệ thống và ứng dụng khác. Do đó, bạn cần yêu cầu quyền khi bắt đầu chạy trong ứng dụng trước khi có thể truy cập dữ liệu bị hạn chế hoặc thực hiện các hoạt động bị hạn chế. Khi ứng dụng yêu cầu quyền khi bắt đầu chạy, hệ thống sẽ hiện lời nhắc cấp quyền khi bắt đầu chạy, như minh hoạ dưới.
![](https://developer.android.com/static/images/training/permissions/runtime.svg)

- Nhiều quyền khi bắt đầu chạy sẽ truy cập vào dữ liệu riêng tư của người dùng, một loại dữ liệu đặc biệt bị hạn chế, bao gồm cả thông tin có thể mang tính nhạy cảm. Có thể kể đến một số loại dữ liệu riêng tư của người dùng như thông tin vị trí và thông tin liên hệ.

- Micrô và máy ảnh cung cấp quyền truy cập vào thông tin đặc biệt nhạy cảm. Chính vì vậy mà hệ thống sẽ giúp bạn giải thích lý do vì sao ứng dụng cần truy cập vào thông tin này.

- Hệ thống sẽ chỉ định cấp độ bảo vệ nguy hiểm cho các quyền khi bắt đầu chạy.
### 3.3 Yêu cầu permission
![](https://user-images.githubusercontent.com/84316258/184806820-8884486a-745a-44ef-a5c8-2345e70cd152.png)
#### 3.3.1 Kiểm tra permission đã được cấp hay chưa
Truyền vào bên trong `ContextCompat.checkSelfPermission()` tên permission cần kiểm tra. Hàm này sẽ trả về`PERMISSION_GRANTED` hoặc `PERMISSION_DENIED` tương ứng

#### 3.3.2 Các quy tắc cần tuân thủ
- Khi nào cần quyền thì mới xin, không xin một loạt từ lúc mới vào app
- Không được chặn không cho người dùng sử dụng app nếu người dùng từ chối trao quyền
- Nếu người dùng thu hồi quyền, tắt đi tính năng không sử dụng được và vẫn phải cho người dùng sử dụng app như thường
- Giải thích tại sao app cần quyền này

#### 3.3.3 Làm thế nào để request permission
##### Dùng requestPermissions()
```kt
// MainActivity.kt
fun requestPermission() {
    when {
        ContextCompat.checkSelfPermission(
                [CONTEXT],
                Manifest.permission.[REQUESTED_PERMISSION]
                ) == PackageManager.PERMISSION_GRANTED -> {
            
            // Quyền đã được cấp. Làm gì thì làm đi.
            performAction(...)
        }
        shouldShowRequestPermissionRationale(...) -> {
        /*
            Hàm shouldShowRequestPermissionRationale() returns true nếu app đã 1 lần yêu cầu quyền và người dùng đã từ chối.
            Nếu người dùng đã từ chối, và lần này tiếp tục ấn từ chối lần thứ 2 (hoặc ấn Don't ask again ở các API Android thấp hơn), hàm sẽ trả về false.
        */
            showMessage()
            requestPermissions(...)
        }
        else -> {
            // Dùng hàm này để yêu cầu quyền
            requestPermissions(CONTEXT,
                    arrayOf(Manifest.permission.REQUESTED_PERMISSION),
                    REQUEST_CODE)
        }
    }
}
```
```kt
// MainActivity.kt
// 
override fun onRequestPermissionsResult(requestCode: Int,
        permissions: Array<String>, grantResults: IntArray) {
    when (requestCode) {
        PERMISSION_REQUEST_CODE -> {
            // Nếu Request không có phản hồi (user bấm ra ngoài thông báo, mảng grantResults sẽ empty)
            if ((grantResults.isNotEmpty() &&
                    grantResults[0] == PackageManager.PERMISSION_GRANTED)) {
                // Quyền đã được cấp
                // Làm gì thì làm đi.
            } else {
                // Giải thích tại sao feature này không khả dụng lí do rằng user đã từ chối cấp quyền
                // Tôn trọng quyết định của user. Không được chuyển hướng sang Setting của app để buộc người dùng phải cấp quyền.
            }
            return
        }
        else -> {
            // Bỏ qua các yêu cầu khác
        }
    }
}
```
##### Sử dụng contract RequestPermission()

1. Tạo một biến loại `ActivityResultCallback` gọi tới `registerForActivityResult()`. Callback này sẽ xử lý khi có kết quả yêu cầu quyền từ người dùng 
```kt
val requestPermissionLauncher =
    registerForActivityResult(RequestPermission()
    ) { isGranted: Boolean ->
        if (isGranted) {
            // Permission is granted. Continue the action or workflow in your
            // app.
        } else {
            // Explain to the user why they can't use this function
        }
    }
```
2. Gọi hàm `launch()` ở cái launcher vừa tạo:
```kt
fun requestPermission() {
    when {
        ContextCompat.checkSelfPermission(
                CONTEXT,
                Manifest.permission.REQUESTED_PERMISSION
                ) == PackageManager.PERMISSION_GRANTED -> {
            // You can use the API that requires the permission.
        }
        shouldShowRequestPermissionRationale(...) -> {
            showInContextUI(...)
        }
        else -> {
            // Callback đã nghe được
            requestPermissionLauncher.launch(
                    Manifest.permission.REQUESTED_PERMISSION)
        }
    }
}
```
#### 3.3.4 Changelog permission theo API
1. Kể từ [Android 6.0](https://developer.android.com/about/versions/marshmallow/android-6.0-changes#behavior-runtime-permissions)
    + Mới: Runtime Permissions
    + ID của phần cứng:
        + Để có được thông tin MAC/IP/Bluetooth Address của phụ kiện (tai nghe, bàn phím), cần khai báo `ACCESS_FINE_LOCATION` hoặc `ACCESS_COARSE_LOCATION`
2. Kể từ [Android 7.0](https://developer.android.com/about/versions/nougat/android-7.0-changes#perm)
    + Tệp tin riêng tư sẽ được bảo vệ chặt chẽ tránh việc lộ metadata (kích thước file, ngày tạo, ngày chỉnh sửa)
3. Kể từ [Android 8.0](https://developer.android.com/about/versions/oreo/android-8.0-changes#rmp)
    + Trước 8.0, Nếu có một loạt các quyền được khai báo bên trong file Manifest, khi người dùng cho phép 1 quyền, thì toàn bộ các quyền khác mà nằm chung nhóm permission đó sẽ được cấp theo.
    + 8.0 sửa lỗi này
    + Ví dụ, App yêu cầu 2 quyền `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE`. Khi ứng dụng yêu cầu `READ_EXTERNAL_STORAGE` và người dùng cho phép quyền này. Ở các API level 25 và thấp hơn, hệ thống cũng sẽ tự động trao luôn quyền `WRITE_EXTERNAL_STORAGE`, bởi vì cả hai quyền trên đều nằm trong nhóm permission STORAGE và được liệt kê bên trong file Manifest

4. Kể từ [Android 9.0](https://developer.android.com/about/versions/pie/android-9.0-changes-all#privacy-changes-all)
    - Giới hạn thông tin truy cập được từ các cảm biến ở trạng thái background
        + Camera: Không được phép
        + Continuous Report Sensor (accelerometers, gyroscope): Không nhận được events
        + On-change Report Sensor: Không nhận được events
        + Để nhận event, cần chuyển sang service foreground
    - Mới: Nhóm quyền `CALL_LOG` tách ra từ nhóm quyền `PHONE`.
    - Truy cập `số điện thoại` hoặc `trạng thái điện thoại (đang gọi, đang quay số)` cần thêm nhiều bước
    - Quét wifi, lấy thông tin mạng đang sử dụng sẽ cần thêm nhiều bước

5. Kể từ [Android 10](https://developer.android.com/about/versions/10/privacy/changes#top-changes)
    - Ứng dụng từ giờ sẽ chỉ được truy cập vào scoped storage (thư mục riêng của app, được hệ thống Android cấp riêng) và không cần phải khai báo quyền truy cập bộ nhớ trong file Manifest
    - Để đọc các file bên ngoài thư mục riêng của app, sử dụng quyền [MANAGE_EXTERNAL_STORAGE](https://developer.android.com/training/data-storage/manage-all-files).
    - Khi cần theo dõi vị trí người dùng ở trạng thái background, cần cung cấp quyền `ACCESS_BACKGROUND_LOCATION`
6. Kể từ [Android 11](https://developer.android.com/about/versions/11/privacy)
    - Tăng cường tính năng scoped storage:
        + Ứng dụng sẽ không còn có thể tự tạo thư mục riêng trong bộ nhớ. Để truy cập vào cái của riêng nó mà được hệ thống cấp cho, sử dụng getExternalFilesDirs()
    - Hỗ trợ Chỉ cho phép cung cấp quyền 1 lần
    - Tự động xoá quyền nếu không sử dụng app 1 thời gian
    - [Foreground services](https://developer.android.com/about/versions/11/privacy/foreground-services):
        Nếu app target Android11+ và sử dụng camera hoặc microphone ở foreground, cần phải khai báo các thuộc tính camera và microphone ở `<service>`
