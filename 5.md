# Object và Callback
## 1. Object
Object là instance của một class. Thường thì ta phải định nghĩa class, sau đó mới tạo các instance của class đó.

Trong Kotlin, từ khoá `object` được dùng khi muốn tạo một đối tượng với những sự thay đổi nhỏ của lớp mà không phải khai báo tường minh lớp con của lớp đó.
### 1.1 `Object expression`
`Object expression` được sử dụng thay cho `anonymous inner class` trong java.

Có nhiều cách để khởi tạo một object mới:
#### Object không kế thừa
```kt
fun main() {
    val a = object {
        val arr = mutableListOf<Int>()
        fun addNumber(x: Int) {
            arr.add(x)
        }
        fun printf() {
            print(arr)
        }
    }
    a.addNumber(26)
    a.addNumber(10)
    a.printf() // [26, 10]
}
```
Cách viết này tương đương với
```kt
class List {
    val arr = mutableListOf<Int>()
    fun addNumber(x: Int) {
        arr.add(x)
    }
    fun printf() {
        print(arr)
    }
}
fun main() {
    val a = List()
    a.addNumber(26)
    a.addNumber(10)
    a.printf() // [26, 10]
}
```
#### Object được kế thừa
```kt
open class PhanSo(var tuSo : Int, var mauSo : Int){
    fun congPhanSo(tuSoX : Int, mauSoX : Int) {
        tuSo = tuSo * mauSoX + tuSoX * mauSo
        mauSo *= mauSoX
    }
    fun printf() {
        print("$tuSo/$mauSo")
    }
}
fun main() {
    val a = object : PhanSo(1,2) {
        fun congHonSo(honSoX : Int, tuSoX : Int, mauSoX : Int) {
            tuSo = tuSo * mauSoX + (tuSoX + honSoX * mauSoX) * mauSo
            mauSo *= mauSoX
        }
    }
    a.congPhanSo(2,3)
    a.congHonSo(1,1,2)
    a.printf()
}
```
### 1.2 `Object declarion`
`Object declarion` là một cách để định nghĩa singleton trong Kotlin.

Chúng ta có thể tạo ra một instance object ở cấp độ cao nhất của chương trình  mà không cần khai báo fun, khai báo class.
```kt
object PhepToan {
    fun tong(a: Int, b: Int) {
        println(a + b)
    }
    fun hieu(a: Int, b: Int) {
        println(a - b)
    }
    fun tich(a: Int, b: Int) {
        println(a * b)
    }
}
fun main() {
    PhepToan.tong(4, 11) // 15
    PhepToan.hieu(26, 10) // 16
    PhepToan.tich(6, 7) // 42
}
```
Cũng như Class, từ khóa `object` có thể được sử dụng cho property, method, initalizer block, nhưng không thể dùng cho contructor.
### 1.3 `Companion objects`
`Companion objects` chứa factory method hoặc các phương thức khác để khi chúng ta muốn sử dụng không cần khởi tạo class mà chỉ cần gọi tên class.

Class trong Kotlin không thể có các thành phần `static` như `static method`, `static property`. Vì vậy để thay thế Kotlin có:

- `Package-level function` (thay cho static method)
- `Object declaration` (thay cho static method và static field). Nhưng `package-level function` không thể truy cập vào các thành phần private của Class nên `object declaration` được sử dụng thay thế. Sau đây là ví dụ đơn giản cho `companion object`:
```kt
class A {
    companion object {
        fun bar() {
            println("Companion object called")
        } 
    } 
} 
fun main() {
    A.bar() // Companion object called
}
```
## 2. Call back
### 2.1 Lambda expression
Cấu trúc đầy đủ của một lambda expression
```kt
fun main() {
    val sum : (Int, Int) -> Int = {a : Int, b : Int -> a + b}
    // val <lambdaName> : <type> -> <returnType> = { <argumentList> -> <codeBody>}
    print(sum(1, 1)) // 2
}
```
- Biểu thức của lambda luôn được bao bởi {}
- Nếu lambda function có bất kì param nào nó phải ở trước toán tử -> (kiểu dữ liệu của param có thể được bỏ qua)
- Body của lambda function phải ở sau toán tử ->
### 2.2 Anonymous function
Anonymous function giống như funtion thường, nhưng không có tên.
```kt
fun main() {
    val sum = fun(a : Int, b : Int) : Int {
        val c : Int = a + b
        return c
    }
    print(sum(1, 1)) // 2
}
```
### 2.3 Funtion Literal
```kt
fun main() {
    val sum: Int.(Int) -> Int = { // this: Int, it: Int
        plus(it) // this + it
    }
    println(1.sum(2)) // 3
}
```
- Int1 là receiver, tức là thứ sẽ sử dụng funtion này.
- Int2 là argument, tức là tham số như bình thường
- this là bản thế, cái thứ đang thực thi hàm này, ở đây ta nghĩ ngay this chính là Int1 (receiver)
- it là tham số truyền vào (do ở đây có mỗi một tham số nên được phép dùng it)

Nếu thay `it` bằng `this` thì kết quả trả về là 2 chứ không phải 3
```kt
fun main() {
    val sum: Int.(Int) -> Int = { // this: Int, it: Int
        plus(this) // this + it
    }
    println(1.sum(2)) // 2
}
```
### 2.4 Higher order function
`Higher order function` là function có input là một funtion, hoặc chính nó trả về một function.
```kt
fun sum(a : Int, b : Int, block : (Int, Int) -> Int) : Int {
    return block(a, b)
}
fun main() {
    val c : (Int, Int) -> Int = { a, b ->
        a + b
    }
    print(sum(1, 1, c))
}
```
### 2.5 Callback
Callback là:
- Sử dụng một funtion type là một tham số. Cũng có nghĩa đứa main function là higher order function.
- Ở trong body của main function, nó sẽ gọi tới hàm worker (ở đây hàm worker chưa được định nghĩa, chỉ biết tên nó, các tham số truyền vào của nó và giá trị trả về)
- Main function đang thực thi thì sẽ tạm dừng rồi nhảy tới hàm worker, đợi nó làm việc xong, trả về một kết quả rồi hàm chủ lại tiếp tục làm việc tiếp.
```kt
fun calc(a : Int, b : Int, f : (Int, Int) -> Int) : Int {
    return f(a,b)
}
fun main() {
    val sum : (Int, Int) -> Int = { a, b -> 
        a + b
    }
    val mul : (Int, Int) -> Int = { a, b -> 
        a * b
    }
    println(calc(1, 1, sum)) // 2
    print(calc(2, 2, mul)) // 4
}
```
- Lợi ích của callback:
    + Tái sử dụng code, hạn chế lỗi: Có thể tái sử dụng lại code chỉ cần thay đổi lại hàm worker.
    + Tăng cường tính bao đóng của OOP cho hàm worker.
- Điểm trừ của callback:
    + Chương trình chính phải dừng lại đợi chương trình con. Nếu hàm callback mất thời gian sẽ ảnh hưởng tới hiệu suất chương trình.
    + Trở nên rất loạn khi sử dụng đa luồng bất đồng bộ.
    + Khó debug, khó kiểm soát lỗi.
