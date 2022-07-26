# Activity, Blinding và Intents
## 1.1 Activity
### Tổng quan về Activity
* Activity là một cửa sổ bao quát toàn bộ hoặc một phần màn hình, đây là nơi chúng ta sẽ đặt các UI component lên trên.
* Trong một app android, các Activity sẽ khởi động thay vì là hàm main() như các chương trình funtional.
* Một app thường có nhiều Activity và chúng có thể tồn tại song song với nhau và tương tác với nhau.
### Vòng đời của một Activity
* 1 Activity sẽ có các trạng thái: `created`, `started`, `resumed`, `paused`, `stopped`, `restarted`, `destroyed`.
* Vòng đời của 1 activity bao gồm nhiều giai đoạn. Chúng ta sử dụng một số callBack() để thực hiện dịch chuyển giữa các activities.

![](https://static.wixstatic.com/media/f1e508_78c6386c3aa94ba39e215babb6314b1e~mv2.png/v1/fill/w_540,h_613,al_c,q_90/f1e508_78c6386c3aa94ba39e215babb6314b1e~mv2.webp)

|Method | Mô tả | Trạng thái tiếp theo|
|---|---|---|
|onCreate| Được gọi khi Activity được khởi tạo lần đầu. Đây là nơi (nên) thực hiện các setup mặc định như: tạo các View, liên kết dữ liệu,... Phương thức này còn cung cấp cho ta 1 Bundle chứa trạng thái của Activity trước đó (Nếu có) Luôn gọi onStart ngay sau khi thực hiện xong.|onStart|
|onRestart|Được gọi sau khi Activity bị dừng (stopped) trước khi Activity được bắt đầu trở lại. Luôn gọi onStart ngay sau khi thực hiện xong.|onStart|
|onStart|Được gọi khi Activity hiển thị với người dùng. Gọi onResume nếu Activity xuất hiện ở nền trước của máy hoặc gọi onStop nếu nó bị ẩn.|onResume / onStop|
|onResume|Được gọi khi Activity bắt đầu tương tác với người dùng. Tại thời điểm này Activity đang ở trên cùng của Activity Stack.|onPause|
|onPause|Được gọi khi Activity mất trạng thái nền trước (bị thay thế bởi Activity khác) hoặc được gọi trước khi Activity dừng hoặc ẩn với người dùng. Gọi onResume nếu Activity được mang ra nền trước (người dùng back trở lại Activity) Gọi onStop nếu Activity bị ẩn đối với người dùng (không bao giờ được hiển thị lại nữa). Gọi onCreate nếu Activity bị hủy (do hệ thống) nhằm mục đích giải phóng tài nguyên máy.|onResume / onStop /onCreate|
|onStop|Được gọi khi Activity không còn được hiển thị lại với người dùng nữa. Gọi onRestart nếu Activity bị hủy (do hệ thống) nhằm mục đích giải phóng tài nguyên máy. Gọi onDestroy nếu Activity dừng hoạt động.|onRestart / onDestroy|
|onDestroy|Được gọi khi Activity dừng hoạt động hoặc lập trình viên gọi finish() hoặc vì hệ thống hủy Activity để giải phóng bộ nhớ.|nothing|
## 2. Binding
### 2.1 FindViewById
Khi lập trình Android, để tương tác với các View/Control trong giao diện chúng ta thường thông qua thuộc tính Id của các view/control để truy suất thay đổi dữ liệu. Vì Android chia màn hình (Activity) thành hai phần: Phần thiết giao diện, phần xử lý nghiệp vụ. Do đó để truy suất được tới các View trong phần giao diện Android cung cấp hàm findViewById.
> VD:
```kt
<TextView>
    ...
    android:id='@+id/tênView 
</TextView>
```
```kt
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    val tv = findViewById(R.id.tênView)

}
```
### 2.2 View Binding
* View Binding là 1 tiện ích cho phép việc liên kết code với View 1 cách dễ dàng hơn, khi View Binding được kích hoạt trong 1 module, nó sẽ tạo ra các binding class đối với mỗi layout.
* View Binding được sử dụng khi có rất nhiều view trong 1 layout cần tương tác với nhau thay vì sử dụng FindViewById mất nhiều thời gian.
#### Setup
Để kích hoạt View Binding trong một module, set viewBinding build option thành true trong file module-level build.gradle
```kt
buildFeatures {
    viewBinding = true
}
```
Khi được kích hoạt thì View Binding sẽ được áp dụng cho tất cả các layout, cách các view được gọi thông qua View Binding sẽ dựa trên id của chúng.
#### Cách dùng
Để tạo 1 instance của Binding Class nhằm sử dụng cho Activity, chúng ta cần thực hiện 1 số việc sau trong onCreate() của Activity:
* Gọi phương thức inflate() để tạo 1 instance của Binding class sử dụng trong Activity.
* Sử dụng phương thức getRoot() để tham chiếu đến chế độ xem gốc của file layout tương ứng.
* Truyền root view vào trong setContentView() để xác định nó là chế độ xem đang hiển thị trên màn hình.
```kt
private lateinit var binding: ActivityMainBinding
//layout file tên là activity_main nên sẽ viết là ActivityMainBinding
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    binding = ActivityMainBinding.inflate(layoutInflater)
    val view = binding.root
    setContentView(view)
}
```
Như vậy khi cần truy cập vào các View, cụ thể là Button “Click Me” ở ví dụ ở trên, ta có thể làm như sau:
```kt
private lateinit var binding: ActivityMainBinding
override fun onCreate(savedInstanceState: Bundle?) {
   super.onCreate(savedInstanceState)
   setContentView(R.layout.activity_main)

   binding = ActivityMainBinding.inflate(layoutInflater)
   setContentView(binding.root)

   binding.btnClickMe.setOnClickListener {
       Toast.makeText(this, "Đã nhấn vào Click Me!", Toast.LENGTH_SHORT).show()
   }
}
```
#### Sự khác biệt giữa FindViewById và View Binding
View Binding có lợi ích so với FindViewById như sau:
* Null safety: Vì View Binding tạo tham chiếu trực tiếp tới view, nên không có rủi ro của null pointer exception do sai view ID. Hơn nữa, khi một view chỉ xuất hiện ở vài cấu hình của một layout, trường chá tham chiếu của nó ở binding class được đánh dấu @Nullable.
* Type safety: Các trường ở từng binding class có các kiểu phù hợp với view mà chúng tham chiếu ở file XML. Điều này có nghĩa là khôn có rủi ro  xảy ra class cast exception.
> Những khác biệt trên nghĩa là những sự không tương thích giữa layout và code sẽ dẫn đến build bị lỗi ở compile time hơn là ở runtime.
## 3. Intent
Trong Android, Intent là những tin nhắn không đồng bộ cho phép các component của ứng dụng (như activities, services, broadcast receivers và content providers) yêu cầu chức năng từ một component khác.
> Intents sẽ giúp tương tác giữa các component từ cùng một ứng dụng cũng như với các component của ứng dụng khác.

Intent là instance của lớp android.content.Intent và chủ yếu hữu ích để thực hiện những việc sau:
|Component|Mô tả|
|---|---|
|Bắt đầu một Activity|Khi dùng một đối tượng Intent thông qua phương thức startActivity(), chúng ta có thể bắt đầu một Activity mới hoặc Activity hiện có.|
|Bắt đầu một Service|Khi dùng một đối tượng Intent thông qua phương thức startService(), chúng ta có thể bắt đầu một Service mới hoặc gửi các hướng dẫn đến một Service hiện có.|
|Truyền đi một Broadcast|Khi dùng một đối tượng Intent thông qua phương thức sendBroadcast() , chúng ta có thể gửi tin nhắn đến các broadcast receiver khác.|

Có 2 loại Intent là Implicit Intent (Intent không tường minh) và Explicit Intent(Intent tường minh)
![](https://developer.android.com/static/codelabs/basic-android-kotlin-training-activities-intents/img/702236c6e2276f91.png)
### 3.1 Implicit Intent
Intent này không cần chỉ định tên component. Nó gọi component của app khác để xử lí.
```kt
intent = Intent(Intent.ACTION_VIEW)
intent.setData(Uri.parse("proptit.com"))
startActivity(intent)

intent= Intent(Intent.ACTION_VIEW, Uri.parse("proptit.com"))
startActivity(intent)
```
Đoạn code trên có tác dụng dùng intent để truy cập trang web proptit từ intent
### 3.2 Explicit Intent
Intent này đáp ứng yêu cầu trong component của app. Nó lấy tên của class đầy đủ điều kiện của activity hoặc service mà ta muốn bắt đầu.
```kt
intent = Intent(applicationContext, SecondActivity::class.java)
startActivity(intent)
```
Đoạn code trên có chức năng chuyển từ activity hiện tai sang SecondActivity.
### So sánh Implicit Intent và Explicit Intent
|Implicit Intent|Explicit Intent|
|---|---|
|Không đăt tên component cụ thể như Explicit Intent, nhưng khai báo hành động chung để thư hiện để cho phép component từ app khác có thể xử lí. Ví dụ: mở mail, Bluetooth, goi điện thoai từ phím share|Là cái rõ ràng, biết chính xác activity nào có thể xử lí được yêu cầu. Ví dụ: chuyển activity này sang activity kia|
|Chỉ chỉ đinh hành động duy nhất được hiện và không trực tiếp chỉ định Android Component|Có thể thưc hiện hành động app cụ thể được có như đổi activity, tải file nền,…|
|Hành động trên 2 app là khác nhau, ko liên quan => ko có|Có thể truyền data tới activity khác bằng sử dụng method putExtra(), nhận được bằng sử dụng getIntent()|
|2 app giao tiếp với nhau|Dùng để giao tiếp trong app|
