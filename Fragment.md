# Fragment
## Tổng quan về Fragment
* Fragment đại diện cho 1 phần UI có thể tái sử dụng.
* Một fragment có riêng cho nó layout, có riêng 1 lifecycle cũng như có riêng phần xử lý event từ người dùng.
* Fragment không thể tồn tại độc lập, nó phải thiết lập dựa trên 1 Activity hoặc là 1 fragment cha.
* Fragment cho phép chúng ta áp dụng tính mô đun và tính tái sử dụng vào trong UI app bằng cách: Kết hợp của một Global component (component có mặt ở nhiều Activity, ví dụ như: thanh điều hướng, thanh search...) và một diện tích đã cố định có khả năng thay đổi layout dựa trên mục đích sử dụng (ví dụ: có 4 tab: Tab Overview, tab List, tab Manager, tab Setting).
![](https://images.viblo.asia/full/fd7d3ee0-8433-46f1-9f36-d55afd8885ad.png)
## Fragment Lifecycle
![](https://i.imgur.com/0EVReuq.png)
|Method|Mô tả|
|---|---|
|onAttach()|Được gọi khi fragment đã được liên kết với một activity.|
|onCreate()|Được sử dụng để khởi tạo fragment.|
|onCreateView()|Được sử dụng để khởi tạo fragment view.|
|onActivityCreated()|Được gọi khi fragment và view của fragment được khởi tạo, dùng để hoàn thành nốt công đoạn khởi tạo fragment và activity.|
|onStart()|Được gọi để hiển thị fragment.|
|onResume()|Người dùng hoàn toàn nhìn thấy và tương tác được với fragment.|
|onPause()|Được gọi khi fragment không còn hiển thị và người dùng đang rời khỏi fragment.|
|onStop()|Được gọi để dừng fragment.|
|onDestroyView()|Giao diện view fragment bị xóa sau khi thực hiện.|
|onDestroy()|Được gọi khi hủy fragment.|
|onDetach()|Được gọi ngay sau khi fragment bị tách ra khỏi activity.|
## Chuyển dữ liệu giữa fragment - fragment
- Kể từ fragment v1.3 trở lên, `FragmentManager` sẽ implement `FragmentResultOwner`, giúp thuận tiện cho việc pass data từ frag này sang frag kia
- Giả sử A -> B, B là người đưa ra result, rồi quay lại A sẽ sử dụng result đó: 
    + A sẽ cần implement
        ```kt
        setFragmentResultListener(requestKey: String,
                                 (requestKey: String, bundle: Bundle) -> Unit
        )
        ```
    + B sẽ cần implement 
        ```kt
        setFragmentResult(requestKey: String, bundle: Bundle)
        ```
- Ngược lại ta cũng có thể chuyển dữ liệu từ A sang B bằng cách tương tự
![](https://images.viblo.asia/fd7d3ee0-8433-46f1-9f36-d55afd8885ad.png)

### Chuyển dữ liệu giữa fragment - activity
- Lần này chúng ta sẽ áp dụng `setFragmentResultListener` lên trên `FragmentManager` nằm ở Activity.
```kt
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        supportFragmentManager
                .setFragmentResultListener("requestKey", this) { requestKey, bundle ->
            // We use a String here, but any type that can be put in a Bundle is supported
            val result = bundle.getString("bundleKey")
            // Do something with the result
        }
    }
}
```
- Chiều ngược lại: Activity ra Fragment: ta sẽ sử dụng `setFragmentResult` lên `FragmentManager`.
```kt
 supportFragmentManager
        .setFragmentResult("request", Bundle().apply { "Bruh" to 9 })
```