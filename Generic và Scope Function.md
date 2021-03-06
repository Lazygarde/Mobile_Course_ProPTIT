# Generic and Scope Function
## 1. Generic
`Generics` có nghĩa là chúng ta sử dụng một class hoặc một cách `implement` theo cách rất chung chung
> VD1: `interface List` cho phép chúng ta tái sử dụng code. Chúng ta có thể tạo một danh sách String, danh sách integer và chúng ta sẽ có các hoạt động giống nhau ngay cả khi các kiểu là khác nhau. Vì vậy, danh sách như một chức năng chung cho mỗi lần `implement`.

> VD2: Khởi tạo 1 class Data kiểu `Generics` có menthod showdata
```kt
class CountAppear<T>(val key : T, var value: Int) {
    fun add(k : Int) {
        value += k
    }
    fun show() {
        println("$key: $value")
    }
}
fun main() {
    val a = CountAppear("Huong", 0)
    a.add(4)
    a.add(11)
    a.show() // Huong: 15
    val b = CountAppear(26, 4)
    b.add(6)
    b.show() // 26: 10
}
```
> Như ta thấy khi khởi tạo 1 Instance của class CountAppear ta truyền vào hàm khởi tạo 2 kiểu dữ liệu khác nhau đó là Int và String nhưng khi các hàm chạy thì đều vẫn sẽ thực hiện 1 hành động giống nhau.
### Một số quy ước
Có rất nhiều cách để đặt tên cho kiểu tham số trong Generic nhưng chúng ta nên tuân theo nhưng kiểu đặt tên tiêu chuẩn như sau:
- T - `Type` (Kiểu dữ liệu bất kỳ thuộc `Wrapper class`: String, Integer, Long, Float, …)
- E – `Element` (phần tử – được sử dụng phổ biến trong `Collection Framework`)
- K – `Key` (khóa)
- V – `Value` (giá trị)
- N – `Number` (kiểu số: Integer, Double, Float, …)
### Từ khoá in và out
Khi chúng ta muốn gán generic type cho bất kỳ super type của nó, thì chúng ta cần sử dụng từ khóa out và khi chúng ta muốn gán generic type cho bất kỳ sub-type của nó, thì chúng ta cần sử dụng từ khóa in.
## 2. Scope funtion
Scope function (hàm phạm vi) là những function có chức năng chính là cung cấp một scope nhỏ hơn từ function gọi. Khi gọi một hàm của một object với biểu thức lambda, nó tạo lên một scope tạm thời. Ở scope này, bạn có thể truy cập object mà không cần tên. Có 5 loại scope function: `let`, `run`, `with`, `apply`, và `also`.

Cơ bản thì các hàm này nó giống nhau đều thực thi trong 1 block. Điều khác biệt lớn của nó ở đây là đối tượng tham chiếu và giá trị trả về.
> Đây là ví dụ về scope funtion:
```kt
data class Student(val name: String, val age: Int, var GPA: Double) {
    fun show(){
        print("Name: $name\nAge: $age\nGPA: $GPA")
    }
    fun changeGPA(tmp: Double){
        GPA = tmp
    }
}
fun main() {
    Student("Huong", 20, 0.0).let {
        it.changeGPA(3.2)
    	it.show()
    }

    // Name: Huong
    // Age: 20
    // GPA: 3.2
}
```
> Nếu không dùng scope ở đây thì khi bạn muốn sử dụng nó bạn sẽ phải gọi lại tên của nó. Như ví dụ dưới:
```kt
data class Student(val name: String, val age: Int, var GPA: Double) {
    fun show(){
        print("Name: $name\nAge: $age\nGPA: $GPA")
    }
    fun changeGPA(tmp: Double){
        GPA = tmp
    }
}
fun main() {
    val huong = Student("Huong", 20, 0.0)
    huong.changeGPA(3.2)
    huong.show()
    
    // Name: Huong
    // Age: 20
    // GPA: 3.2
}
```
Các scope function này nó không giới thiệu về kỹ thuật mới, nó chỉ giúp cho code của bạn gọn gàng, đẹp đẽ và dễ đọc hơn. Dưới đây là bảng mô tả chi tiết về sự khác biệt giữa các hàm:
| | it | this | return | extension fun |
|---|---|---|---|---|
| `let` | object | class | anything | yes |
| `apply` |  | object | object | yes |
| `run` |  | object | anything | yes |
| `also` | object | class | object | yes |
| `with` |  | object | anything | no |
### 2.1 `let funtion`
 `let function` thường dùng để cung cấp null safety calls. Sử dụng toán tử safe call (?.) với let để có null safety. Nó thực thi khối chỉ với giá trị không null.
```kt
fun main() {
    var a: Int ?= null
    a?.let{ // a là null nên không thực hiện
        print(it)
    }
    a = 2
    a?.let{ // a khác null nên được thực hiện
        print(it)
    }
}
```
### 2.2 `with funtion`
Nên sử dụng `with funtion` để gọi hàm vào một context object mà không cung cấp lambda result.
```kt
data class Student(val name: String, val age: Int, val GPA: Double) {}
fun main() {
    val huong = Student("Huong", 20, 3.2)
    with(huong) {
        print("Name: $name\nAge: $age\nGPA: $GPA")
    }
}
```
### 2.3 `run funtion`
`run funtion` là sự kết hợp của `let funtion` và `with funtion`. `run funtion` được sử dụng khi object lambda chứa cả khởi tạo và sự tính toán của giá trị trả về. Sử dụng run ta có thể thực hiện null safety calls cũng như những các phép tính khác.
```kt
data class Student(val name: String, val age: Int, val GPA: Double) {}
fun main() {
    var huong : Student ?= null
    huong?.run { // huong đang là null nên không thực hiện
        print("Name: $name\nAge: $age\nGPA: $GPA")
    }
    huong = Student("Huong", 20, 3.2)
    huong?.run { // huong khác null nên thực hiện được
        print("Name: $name\nAge: $age\nGPA: $GPA")
    }
    
    // Name: Huong
    // Age: 20
    // GPA: 3.2
}
```
### 2.4 `apply funtion`
`apply funtion` dùng cho khối code không trả về một giá trị và chủ yếu hoạt động trên các thành viên của receiver object. Trường hợp phổ biến cho apply là cấu hình object.
```kt
class Student() {
    lateinit var name: String
    lateinit var age: String
    lateinit var GPA: String
    fun show(){
        print("Name: $name\nAge: $age\nGPA: $GPA")
    }
}
fun main() {
    var huong : Student? = null
    huong = Student().apply {
        name = "Huong"
        age = "20"
        GPA = "3.2"
    }
    huong.show()
    
    // Name: Huong
    // Age: 20
    // GPA: 3.2
}
```
### 2.5 `also funtion`
`also funtion` được dùng nơi mà ta phải thực hiện bổ sung khi ta khởi tạo object members
```kt
fun main() {
    val a = mutableListOf(1, 2, 3)
    a.also{
        println(a)
    }.add(4).also{
        println(a)
    }
    // [1, 2, 3]
    // [1, 2, 3, 4]
}
```
