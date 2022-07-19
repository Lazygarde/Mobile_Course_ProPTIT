# Class, Interface và Abstract class

## 1. Class

### 1.1 Khai báo Class trong Kotlin
```kt
class PhanSo {
    
}
```
Trong Kotlin nếu ta không xác định `visibility modifier` cho class như class PhanSo trên thì mặc định sẽ là `public`.
### 1.2 Constructor
Kotlin có một `primary constructor` và có thể có một hoặc nhiều `secondary constructors`.
- `primary contructor`: thường là `contructor` chính, để khởi tạo class và được khai báo tách biệt với thân class.
- `secondary contructor`: được khai báo trong thân class. Class trong Kotlin tồn tại 1 khối gọi là `initializer block`: cho phép bạn thêm các logic để khởi tạo.
#### 1.2.1 Primary Constructor
Để xác định `primary constructor` ta khai báo như sau:
```kt
class PhanSo(tuSo: Int, mauSo: Int) {
    
}
```
Nếu muốn thực hiện các logic code ngay sau `primary constructor` có thể thực hiện bằng cách khởi tạo ra một `block {}` với từ khóa tiền tố `init` ở trước:
```kt
class PhanSo(tuSo: Int, mauSo: Int) {
    init {
       println("$tuSo/$mauSo") 
    }
}
```
Để coi các `param` của `primary constructor` như một `property` trong class, ta thêm `var` hoặc `val` cho các `param` của `primary constructor`:
```kt
class PhanSo(val tuSo: Int, val mauSo: Int) {
    
}
```
#### 1.2.2 Secondary Constructors
Class trong Kotlin có thể có nhiều `secondary constructors`.
```kt
class PhanSo {
    val tuSo: Int
    val mauSo: Int
    constructor(TuSo: Int, MauSo: Int) {
        this.tuSo = TuSo
        this.mauSo = MauSo
        println("$tuSo/$mauSo")
    }
    constructor(TuSo: Int, MauSo: Int, HonSo: Int) {
        this.tuSo = TuSo + HonSo * MauSo
        this.mauSo = MauSo
        println("$tuSo/$mauSo")
    }
}
fun main() {
    val a = PhanSo(1, 2) // 1/2
    val b = PhanSo(1, 2, 1) // 3/2
}
```
Khối khởi tạo `initializer block` (nếu có) sẽ thực thi trước khi `constructor` hoạt động.
```kt
class PhanSo {
    val tuSo: Int
    val mauSo: Int
    constructor(TuSo: Int, MauSo: Int) {
        this.tuSo = TuSo
        this.mauSo = MauSo
        println("$tuSo/$mauSo")
    }
    constructor(TuSo: Int, MauSo: Int, HonSo: Int) {
        this.tuSo = TuSo + HonSo * MauSo
        this.mauSo = MauSo
        println("$tuSo/$mauSo")
    }
    init {
        print("Đây là phân số: ")
    }
}
fun main() {
    val a = PhanSo(1, 2) // Đây là phân số: 1/2
    val b = PhanSo(1, 2, 1) // Đây là phân số: 3/2
}
```
### 1.3 Properties
Ta có thể khai báo `properties` ngay trong `primary constructor` hoặc bên trong class
```kt
class SV(val ID: Int) { // cách 1
    val HoTen: String //cách 2
}
```
Với cách 1, `primary constructor` vừa đóng vai trò là định nghĩa thuộc tính, vừa đóng vai trò là setter thuộc tính.
### 1.4 Getter & Setter
Trong các ngôn ngữ khác (C++, Java): Getter, setter là một method của class. Còn trong Kotlin, thuộc tính/biến có thể là return của một hàm khác -> Code ngắn gọn hơn.
```kt
class TuGiac(val ChieuDai: Int, val ChieuRong: Int) {
    val DienTich: Int
    	get() = this.ChieuDai * this.ChieuRong
}
```
### 1.5 Visibility Modifiers
Kotlin có 4 `visibility modifiers`: `private`, `protected`, `internal` và `public`. Nếu không xác định rõ ràng `visibility modifier` thì mặc định sẽ là `public`.
- `private` chỉ được sử dụng trong class đó (không thể truy cập qua `instance` của class đó)
- `protected` giống với `private` + có thể sử dụng ở trong các `subclass` (các class kế thừa nó).
- `internal` khả năng truy cập rộng hơn 2 loại trên và được truy cập bởi các `instance` của `class` khai báo nó (các `instance` và nơi khai báo `class` thuộc cùng `module`)
- `public` rộng nhất và được sử dụng ở bất kì đâu thông qua `instance` khai báo nó.

## 2. Abstract Class
- `Abstract Class` (lớp trừu tượng) là class sinh ra là để được kế thừa.
- Bên trong body của class này vẫn chứa các `properties` hoặc `methods` như class bình thường, nhưng nó có thêm một số members khác, hơi bất thường.
```kt
abstract class DaGiac {
    abstract fun In()
}
class TamGiac : DaGiac() {
    override fun In() {
        print("Tam Giac")
    }
}
class TuGiac : DaGiac() {
    override fun In() {
        print("Tu Giac")
    }
}
```
- Các member bất thường, được đánh dấu với `abstract` thì các class kế thừa abstract class này bắt buộc phải `override` lại member này.
- `Abstract class` sinh ra là để bị kế thừa. `Member abstract` sinh ra là để `override`.
- Ngoài ra thì ta cũng có thể ghi đè một member khi class chứa nó không phải abstract, bằng từ khoá open:
```kt
open class DaGiac {
    open fun In() {
        
    }
}
abstract class TamGiac : DaGiac() {
    abstract override fun In()
}
```
## 3. Interfaces
- Có thể hiểu rằng: 
    + Class là một bản vẽ, và kiến trúc sư có thể tạo ra nhiều ngôi nhà từ 1 bản vẽ, mỗi ngôi nhà có một số điểm khác nhau nho nhỏ. 
    + Interface là những options khách A, khách B muốn lựa chọn. Option này có thể là: có vườn thượng, có bể bơi, có thang máy...
- Định nghĩa: _Bất thường_ giống abstract class, nhưng các members đều phải là abstract.

```kt
interface PTIT {
    val ID: String //abstract property
    fun Name(): String //abstract method
}
class PTITMienBac:PTIT {
    override val ID: String ="B"
    override fun Name(): String = "Nguyen Van A"
} 
```
## So sánh Interface và Abstract Class
|  | Abstract Class | Interface |
|---|---|---|
| Non-abstract Members | Có cho phép | Không cho phép |
| Đa kế thừa | Không cho phép Đa kế thừa | Cho phép đa kế thừa nhiều interfaces |
| Kế thừa từ đứa còn lại | Abstract extends được interface | Interface không extends được abstract |
| Ý nghĩa | Là một template class cho một loạt các class có chức năng tương tự nhau. 'Is-A' | Là một tính năng có thể xuất hiện ở một số các class. 'Can-do' |
