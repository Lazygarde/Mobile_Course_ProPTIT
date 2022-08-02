# Navigation, TabLayout và ViewPager2
## 1. Navigation
### 1.1 Navigation component
* Android Navigation component là thư viện các component cho phép chúng ta đơn giản hoá quá trình điều hướng giữa các fragment.
> VD:![](https://developer.android.com/static/images/topic/libraries/architecture/navigation-graph_2x-callouts.png)
- Navigation component có các thành phần chính:
    + Navigation Graph: Là 1 file `xml` bao gồm tất cả các vùng nội dung riêng lẻ trong ứng dụng (navigation destination) cũng như các đường dẫn khả thi mà người dùng có thể di chuyển đến thông qua ứng dụng (navigation action).
    + Navigation Host: Là một container rỗng hiển thị đích đến từ các navigation graph. Navigation chứa cách triển khai navigation host mặc định, navigation host fragment hiển thị đích đến của fragment.
    + Navigation Controller: Là đối tượng quản lý việc điều hướng bên trong 1 navigation host.
- Khi người dùng tương tác với UI, Navigation Controller sẽ nhận lệnh (và một số thông tin kèm theo) và thực hiện điều hướng sang một Nav Destination khác.
### 1.2 Getting Started
#### 1.2.1 Thêm dependency: 
Scope: `build.gradle (App)`
```groovy
dependencies {
  def nav_version = "2.5.1"
  // Java language implementation
  implementation "androidx.navigation:navigation-fragment:$nav_version"
  implementation "androidx.navigation:navigation-ui:$nav_version"
  // Kotlin
  implementation "androidx.navigation:navigation-fragment-ktx:$nav_version"
  implementation "androidx.navigation:navigation-ui-ktx:$nav_version"
```
#### 1.2.2 Thêm vào NavGraph
- Tại thư mục `res`, Chọn `New` / `Android Resource File`
- Điền tên: nav_graph
- Trong `Resource Type`, chọn `Navigation`
- Giao diện của NavGraph
![](https://developer.android.com/static/images/guide/navigation/nav-editor-2x.png)
    (1): Các Navigation Destination, trong đó có 1 các được đánh dấu là START
    (2): GraphEditor: Minh hoạ các destination và quan hệ giữa chúng (Action)
    (3): Attributes: Hiển thị các thuộc tính của Destination/Action
#### 1.2.3 Thêm NavHost vào Layout của Activity
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.appcompat.widget.Toolbar
        .../>

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/nav_host_fragment"
            android:name="androidx.navigation.fragment.NavHostFragment"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"

            app:defaultNavHost="true"
            app:navGraph="@navigation/nav_graph" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        .../>

</androidx.constraintlayout.widget.ConstraintLayout>
```
- Chúng ta vẫn sử dụng một FragmentContainerView như mọi khi làm việc với Fragment, tuy nhiên cần chú ý về 3 thuộc tính mới:
    + `android:name`: Tên class sử dụng cho NavHost này, trong trường hợp này, ta sử dụng class `androidx.navigation.fragment.NavHostFragment`
    + `app:navGraph`: Liên kết NavHost này cho Nav Graph nào, ở đây là: `navigation/nav_graph`
    + `app:defaultNavHost="true"`: thuộc tính này can thiệp vào nút Back trên điện thoại, không cho nó hoạt động như bình thường nữa. Tức là bình thường ấn back thì nó sẽ không quay về fragment trước, mà nó quay về Activity trước. 

#### 1.2.4 Thêm fragment vào NavGraph:
```xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android"
    app:startDestination="@id/blankFragment">
    <fragment
        android:id="@+id/blankFragment"
        android:name="com.example.cashdog.cashdog.BlankFragment"
        android:label="@string/label_blank"
        tools:layout="@layout/fragment_blank" />
</navigation>
```
- Chú ý một số thuộc tính:
    + `name`: tên class của Fragment
    + `label`: label cho fragment này, khi kết hợp với 1 bottomNav chẳng hạn thì nó sẽ dùng luôn cái label này

#### 1.2.5 Thêm action giữa hai fragment
- Cách 1 là dùng kéo thả giữa 2 fragment: Click chọn 1 Destination có trên màn hình, nó sẽ hiện ra một cái chấm tròn ở phía mạn phải, cầm kéo nó về cái destination bạn mong muốn:
![](https://developer.android.com/static/images/topic/libraries/architecture/navigation-actioncircle_2x.png)
- Cách 2 là sửa trực tiếp trên XML:
```xml
<fragment
        android:id="@+id/blankFragment"
        android:name="com.example.cashdog.cashdog.BlankFragment"
        android:label="@string/label_blank"
        tools:layout="@layout/fragment_blank" >
        <action
            android:id="@+id/action_blankFragment_to_blankFragment2"
            app:destination="@id/blankFragment2" />
    </fragment>
```
- Thuộc tính id: Tên của action này
- Thuộc tính destination: Đích đến của action này, đương nhiên điểm khởi hành sẽ là fragment chứa action đó.

### 1.3 [Điều hướng bằng Navigation Component](https://developer.android.com/guide/navigation/navigation-navigate)
- Trước hết thì chúng ta phải get instance NavController bằng 1 trong 3 cách sau:
```kt
Fragment.findNavController()
View.findNavController()
Activity.findNavController(viewId: Int)
```
- SafeArgs: tạm thời bỏ qua, sẽ nhắc đến sau.
- DeepLinkRequest sử dụng Navigation: cũng tạm thời bỏ qua
### 1.4 Navigate bằng ID
```kt
viewTransactionsButton.setOnClickListener { view ->
   view.findNavController().navigate(R.id.viewTransactionsAction)
}
```
[Bonus](https://developer.android.com/guide/navigation/navigation-navigate#navoptions): Khi khai báo một `<action>` trong NavGraph, một `NavAction` sẽ được sinh ra và nó bao gồm các thuộc tính:
- Destination: Đích đến của action
- Default Args: là 1 bundle chứa các các argument default (nếu được cung cấp)
- `NavOptions`: là một thành phần chứa các cái cài đặt mà chúng ta muốn áp dụng cho `NavAction` này, ví dụ như: 
`enterAnim`, `exitAnim`: Animation khi chúng ta chúng ta bắt đầu Action và khi kết thúc Action (tương ứng với khi bắt đầu chuyển frag và tới gần khi kết thúc chuyển frag), `popUpToId`...
### 1.5 Navigation và Backstack
- Việc chuyển qua chuyển lại giữa các destination, đương nhiên sẽ được quản lý bởi Android thông qua 1 backstack. 
- Destination đầu tiên được khởi tạo trong `NavHost` sẽ được tự động đặt vào trong stack khi mở app. Các destination sau, cứ mỗi cái được thêm thì sẽ được cho vào đỉnh stack. 
- Ấn `Back` (nút vật lý/ảo của Android) hay `Up` (nút back của riêng app, nằm ở top-left) sẽ gọi `NavController.navigateUp()` và `NavController.popBackStack()` sẽ pop cái đỉnh của stack ra.
- `NavController.popBackStack()` trả về Boolean cho biết việc pop đỉnh có đc thực hiện thành công hay không. Đôi lúc nó sẽ là false, ví dụ như khi chúng ta ấn Back tại Start Destination. Ta sẽ cần phải sử dụng finish khi này:
```kt
if (!navController.popBackStack()) {
    // Call finish() on your Activity
    finish()
}
```
- popUpTo và popUpToInclusive: 
    + Khi cần pop nhiều hơn là 1 destination thì sao ? Ví dụ sau khi người dùng login thành công, chúng ta cần phải pop toàn bộ các destination liên quan tới việc login, không cho phép người dùng back về nữa.
    + Sử dụng: `app:popUpTo` để set destination mà ta cần giữ lại, và xoá đi hết tất cả các destination từ nó trở đi. 
    + Nếu muốn xoá triệt để cả cái destination được sd ở `popUpTo`, gán thuộc tính: `app:popUpToInclusive="true"`
    + Ví dụ:
    ![navigation-getting-started-pop](https://user-images.githubusercontent.com/84316258/182306183-b6bdf61f-e78c-4a38-b8a9-2f141fdf2bb5.png)
    + Flow: A -> B -> C -> A
    + Tại C chúng ta implement Action như sau: 
    ```xml
    <action
        android:id="@+id/action_c_to_a"
        app:destination="@id/a"
        app:popUpTo="@+id/a"
        app:popUpToInclusive="true"/>
    ```
    + Với popUpTo chúng ta đã tiến hành xoá B và C, đồng thời còn xoá luôn cả A
    + Nếu ở đây chúng ta không `popUpToInclusive="true"`, sẽ tồn tại tới 2 instance A trong BackStack.
### 1.6 Bottom Navigation
- Là cái thanh điều hướng giữa các tab khác nhau: ví dụ như của Zalo, của Messenger
![bot_nav_animation](https://user-images.githubusercontent.com/84316258/182369402-0abcaba6-7190-4cd5-b3f2-44344d43e11e.gif)
- Bước 1: Thêm dependency (do đang sử dụng MaterialComponent)
```groovy
// build.gradle (app)
dependencies {
    // ...
    implementation 'com.google.android.material:material:<version>'
    // ...
}
```
- Bước 2: Thêm BotNavView vào Activity layout
```xml
<!-- res/layout/activity_main.xml -->
<com.google.android.material.bottomnavigation.BottomNavigationView
      android:id="@+id/bottom_navigation"
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      app:menu="@menu/bottom_navigation_menu" />
```
- Bước 3: Vẽ menu cho BotNavView:
```xml
<!-- res/menu/bottom_navigation_menu.xml -->
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
      android:id="@+id/page_1"
      android:enabled="true"
      android:icon="@drawable/icon_1"
      android:title="@string/text_label_1"/>
  <item
      android:id="@+id/page_2"
      android:enabled="true"
      android:icon="@drawable/icon_2"
      android:title="@string/text_label_2"/>
</menu>
```
- Bước 4: Viết logic cho BotNavView:
```kt
BottomNavigationView.OnNavigationItemSelectedListener { item ->
    when(item.itemId) {
        R.id.item1 -> {
            // Respond to navigation item 1 click
            true
        }
        R.id.item2 -> {
            // Respond to navigation item 2 click
            true
        }
        else -> false
    }
}
```
Ngoài ra khi reselect: có `setOnNavigationItemReselectedListener`
- Bước 4+: Chỉnh màu cho BotNav
```xml
<style name="Widget.App.BottomNavigationView" parent="Widget.MaterialComponents.BottomNavigationView.Colored">
    <item name="materialThemeOverlay">@style/ThemeOverlay.App.BottomNavigationView</item>
</style>

<style name="ThemeOverlay.App.BottomNavigationView" parent="">
    <item name="colorPrimary">@color/shrine_pink_100</item>
    <item name="colorOnPrimary">@color/shrine_pink_900</item>
</style>
```
```xml
<com.google.android.material.bottomnavigation.BottomNavigationView
    ...
    style="@style/Widget.App.BottomNavigationView"
/>
```
## 2. TabLayout
### 2.1 Định nghĩa
- Là một UI Component giúp chia một Layout ra làm nhiều Tab. Ví dụ như là Facebook có một số Tab như News Feed, Friends, Noti...

### 2.2 Tại sao sử dụng TabLayout
- Cho phép ta thêm một Tab programmatically, tức là không cần phải fixed cứng là cái TabLayout này có bao nhiêu Tab Con bên trong
- Kết hợp với một `ViewPager2`: Một cái container cung cấp animation chuyển Tab rất smooth.
![TabLayout-in-Android](https://user-images.githubusercontent.com/84316258/182369474-f9cfbd9c-7380-4edd-85a0-49bb52dafa4a.gif)
### 2.3 Cách sử dụng, kết hợp với ViewPager2
#### 2.3.1 Cố định số TabItem (Fixed)
- Bên trong TabLayout có thể bao gồm nhiều TabItem:
```xml
<!-- MainActivity.xml -->
 <com.google.android.material.tabs.TabLayout
         android:layout_height="wrap_content"
         android:layout_width="match_parent">

     <com.google.android.material.tabs.TabItem
             android:text="@string/tab_text"/>

     <com.google.android.material.tabs.TabItem
             android:icon="@drawable/ic_android"/>

 </com.google.android.material.tabs.TabLayout>
```
#### 2.3.2 Không cố định (Non-fixed)
#### 2.3.3 B1: Thêm TabLayout và ViewPager2 vào file layout
```xml
<!-- fragment_play.xml -->
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.google.android.material.tabs.TabLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>

    <androidx.viewpager2.widget.ViewPager2
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />

</LinearLayout>
```
#### 2.3.4 B2: Viết Adapter quản lý TabLayout này
```kt
// PlayFragment.kt
class PlayFragmentAdapter(fragment: Fragment) : FragmentStateAdapter(fragment) {
        override fun getItemCount(): Int = 2
        override fun createFragment(position: Int): Fragment {
            return when (position) {
                0 -> FavoriteFragment()
                1 -> ...
                2 -> ...
                else -> MyMusicFragment()
            }
        }
    }
```
#### 2.3.5 B3: Liên kết Adapter với ViewPager, liên kết TabLayout với ViewPager
```kt
// PlayFragment.kt
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        linkViewPagerWithTabLayout()
    }
    private fun linkViewPagerWithTabLayout() {
        binding.viewPager.adapter = PlayFragmentAdapter(this)
        // Sử dụng TabLayoutMediator để kết nối 2 thằng với nhau
        TabLayoutMediator(binding.tabLayout, binding.viewPager) { tab, position ->
            when (position) {
                0 -> tab.text = "Favourite"
                else -> tab.text = "My Music"
            }
        }.attach()
    }
}
```
### Chú ý:
- Số lượng TabItem chỉ nên từ 3 - 5 tab
## 3. ViewPager2
### 3.1 Định nghĩa:
- Là một ViewGroup giúp chúng ta kéo qua kéo lại giữa các page. Nó sử dụng một RecycleView.Adapter để xử lý tab hiện lên màn hình.
![030d43f6-ed6c-4f49-a2f4-abd86f015da1](https://user-images.githubusercontent.com/84316258/182369511-5ce834b2-443b-402c-9876-681974742d6f.gif)
### 3.2 Tại sao sử dụng ViewPager2
Điểm mạnh của nó so với phiên bản 1:
- Hỗ trợ bố cục right to left (RTL)
- Hỗ trợ bố trí theo chiều dọc (cuộn theo chiều dọc)
- Sự kiện PageChangeListener tốt hơn
- Đổi từ `PageAdapter` sang `RecyclerView.Adapter`
- Đổi từ `FragmentStateFragmentAdapter` sang `FragmentStateAdapter`
- Đổi từ `registerOnPageChangeCallback` sang `addPageChangeListener`
### 3.3 Cách sử dụng cùng với TabLayout:
Xem [Cách sử dụng TabLayout kết hợp với ViewPager2](#cách-sử-dụng-kết-hợp-với-viewpager2)
