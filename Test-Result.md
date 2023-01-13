Võ Văn Đức- Bài Làm:

**Câu 1**. Cho 3 URI tương ứng với 3 controller và method sau
    - `/admin/product` - ProductController@index
    - `/admin/user` - UserController@index
    - `/admin/category` - CategoryController@index
​
Hãy định nghĩa các route cho các URI trên với các yêu cầu sau
- Sử dụng prefix chung là `admin`
- Cả 3 route đều là `GET`
- User phải login mới đc access vào 3 route trên
- Sau khi tạo xong, chỉ duy nhất 3 route đc tạo ra, ko đc phép hơn

​**Trả lời**:

    Route::prefix('admin')->middleware('auth')->group(function () {
        Route::get('/product', [App\Http\Controllers\Backend\ProductController::class, 'index'])->name('product');
        Route::get('/user', [App\Http\Controllers\Backend\UserController::class, 'index'])->name('user');
        Route::get('/product', [App\Http\Controllers\Backend\CategoryController::class, 'index'])->name('category');
    })->name('admin');
    ​
**Câu 2**. Hãy cho biết sự khác nhau giữa post và get (nêu rõ sự giống nhau và khác nhau). Trong thực tế POST hay get được dùng nhiều hơn.

​**Trả lời**:

Giống nhau:
- Đều là các HTTP method dùng để trao đổi dữ liệu giữa client và server

Khác nhau :
- Phương thức POST sẽ gửi dữ liệu ngầm nối vào phần body, không xuất hiện trên URL nên sẽ bảo mật hơn phương thức GET.
- Phương thức GET chỉ chấp nhận các ký tự ASCII còn Phương thức POST không có rằng buộc nào.
- Phương thức GET thực thi nhanh hơn POST vì những dữ liệu gủi đi luôn được webbrowser cached lại.
- Khi dùng phương thức POST thì server luôn thực thi và trả về kết quả cho client, còn với phương thức GET thì webbrowser sẽ xem trong cached có kết quả tương ứng với yêu cầu đó không và trả về ngay mà không cần phải thực thi các yêu cầu đó ở phía server.

Theo em trên thực tế phương thức GET được sử dụng nhiều hơn.

**Câu 3** Kể tên các loại Route bạn thường sử dụng trong Laravel

​Các loại Route mà em thường sử dụng là:

- Route::get nhận resquest với phương thức GET.
- Route::post nhận resquest với phương thức POST.
- Route::put nhận resquest với phương thức PUT.
- Route::delete nhận resquest với phương thức DELETE.
- Route::group tạo ra các nhóm route.
- Route::resource sử dụng với resource controller.
- Route::prefix dùng để thêm tiền tố cho một nhóm các routes 

**Câu 4**. Nếu trong thư mục `database/migrations` của bạn đang có 3 file migration lần lượt là 1,2,3. Bạn đã chạy `php artisan migrate` cho 3 file đó rồi.
​
Sau đó bạn tạo thêm 2 file migration lần lượt là 4,5. Lúc này bạn tiến hành chạy lệnh `php artisan migrate`. Những file nào sẽ được thực thi, vì sao lại là những file đó?
​

Sau khi bạn thêm 2 file migration 4 và 5 và chạy câu lệnh `php artisan migrate` thì chỉ có hai file này được thực thi bởi vì lệnh này sẽ chạy tất cả các migrations chưa được thực thi trong hệ thống. Hàm này sẽ chạy đến function up() trong hai files này và thêm vào hai table mới trong database. 
​

**Câu 5**. Nếu trong thư mục `database/migrations` của bạn đang có 3 file migration lần lượt là 1,2,3. Bạn đã chạy `php artisan migrate` cho 3 file đó rồi.
​
Sau đó bạn tạo thêm 2 file migration lần lượt là 4,5. Lúc này bạn tiến hành chạy lệnh `php artisan migrate:rollback`. Những file nào sẽ được thực thi, vì sao lại là những file đó?

Sau khi bạn thêm 2 file migration 4 và 5 và chạy câu lệnh `php artisan migrate:rollback` thì chỉ có 3 file đầu tiên lần lượt được thực thi theo thứ tự ngược lại bởi vì lệnh này dùng để rollback các migrations gần nhất trong hệ thống. Nó sẽ chạy function down() trong các file đồng nghĩa với việc drop các table trong database
​

**Câu 6**. Hãy cho biết sự khác nhau giữa migrate:refresh và migrate:reset trong Laravel

**Trả lời**:

- Lệnh `migrate:reset` dùng để rollback tất cả các migration trong chương trình, có thể hiểu là nó sẽ chạy drop tất cả các table. 
- Lệnh `migrate:refresh` dùng để thực hiện lần lượt rollback toàn bộ các migrations của bạn và thực hiện migrate lại. Câu lệnh này dùng để rollback và migrate trong một command duy nhất. 
​

**Câu 7**. Hãy lấy một ví dụ về 1 trường hợp Query N+1 và giải thích nó bằng code demo

Ví dụ: chúng ta cần truy vấn đến bảng author để lấy tên tác giả của từng quyển sách thì chứng ta có model Book và câu truy vấn như sau:
    
    ​class Book extends Model {
    public function author()
    {
    return $this->belongsTo(‘App\Author’);
    }
    }

Sử dụng vòng lặp foreach để lấy từng tên tác giả trong author như cách dưới đây:

    foreach (Book::all() as $book)
    {
    echo $book->author->name;
    }

Với đoạn code trên đầu tiên sẽ lấy ra tất cả các cuốn sách, sau đó ở mỗi vòng lặp nó lại truy vấn tới bảng author để lấy ra tên các tác giả. Vậy nếu trong database có 25 cuốn sách thì nó sẽ truy vấn tới database tổng cộng là 26 lần (tính cả 1 lần nó lấy book:all). Chúng ta có thể gọi đây là trường hợp Query N+1 và cần phải khắc phục nó.

    foreach (Book::with(‘author’)->get() as $book)
    {
    echo $book->author->name;
    }
Bằng cách sử dụng kỹ thuật `Eager Loading` với `with('author')` thì chúng ta sẽ làm giảm được số lượng câu truy vấn được tạo ra: 

    select * from books
    select * from authors where id in (1, 2, 3, 4, 5, …)

**Câu 8**. Hãy kể tên một số ORM Relationship trong Laravel mà bạn đã sử dụng (tối thiểu 5) kèm giải thích phía sau
​Một số ORM Relationship em đã sử dụng như 
- One to One: là mối quan hệ 1-1 dùng để chỉ ra mối quan hệ giữa một Model với một đối tượng Model khác mà cái này chỉ phụ thuộc vào cái kia và ngược lại.

Ví dụ: Một người chỉ có một Avatar.

    class User extends Authenticatable
    {
        public function avatar()
        {
            return $this->hasOne('App\Avatar');
        }
    }

- One to Many: là mối quan hệ 1- nhiều dùng để chỉ ra mối quan hệ khi một đối tượng Model được link tới nhiều Model khác

Ví dụ : Một bài viết có nhiều comment. 

    class Post extends Model
    {
        
        public function comments()
        {
            return $this->hasMany('App\Comment');
        }
    }

    class Comment extends Model
    {
    public function post()
    {
        return $this->belongsTo('App\Post');
    }
    }

Khi thêm belongsTo() thì eloquent sẽ tự tìm được đến khóa ngoại của mối quan hệ ví dụ ở đây là 'id' ở Post và 'post_id' ở Comment

- Many to Many: là mối quan hện nhiều-nhiều và cần phải có một bảng trung gian để chứa id của hai model có mối quan hệ. 

Ví dụ : Một product sẽ thuộc nhiều orders mà một order lại có nhiều products. Để biểu diễn được quan hệ này chúng ta cần sử dụng đến một bảng thế 3, mình sẽ đặt tên là product_order. và đồng thời sẽ chứa 2 cột là order_id và product_id.

Ở model Product:

    class Product extends Model
    {
        public function orders()
        {
            return $this->belongsToMany('App\Order', 'product_order', 'product_id', 'order_id');
        }
    }

Ở model Order:

    class Order extends Model
    {
        public function product()
        {
            return $this->belongsToMany('App\Product');
        }
    }

Bảng trung gian được tạo ra ở đây sẽ là bảng `product_order `

-One to One Polymorphic: Mối quan hệ này tương tự quan hệ One to One, tuy nhiên mục đích của mối quan hệ này là 1 model có thể belongsTo 1 hay nhiều model khác. 

Ví dụ: nếu một post có một image và một user cũng có một image thì nếu như bình thường chúng ta cần có thêm 2 bảng post_image, user_image thì ở đây sẽ không cần nữa.

    posts
        id - integer
        name - string

    products
        id - integer
        name - string

    images
        id - integer
        url - string
        imageable_id - integer
        imageable_type - string


    class Image extends Model
    {
        public function imageable()
        {
            return $this->morphTo();
        }
    }


    class Post extends Model
    {
        public function image()
        {
            return $this->morphOne('App\Image', 'imageable');
        }
    }

    class Product extends Model
    {
        public function image()
        {
            return $this->morphOne('App\Image', 'imageable');
        }
    }

- One to Many Polymorphic: Mối quan hệ này cũng gần giống với quan hệ One to Many tuy nhiên nó cho phép một model belongsTo nhiều Model khác mà chỉ cần dùng 1 associate

Ví dụ: Một User có thể comment ở cả Post lẫn Video thì chỉ cần 1 bảng comments 

    posts
        id - integer
        title - string
        body - text

    videos
        id - integer
        title - string
        url - string

    comments
        id - integer
        body - text
        commentable_id - integer
        commentable_type - string

    class Comment extends Model
    {
        public function commentable()
        {
            return $this->morphTo();
        }
    }

    class Post extends Model
    {
        public function comments()
        {
            return $this->morphMany('App\Comment', 'commentable');
        }
    }

    class Video extends Model
    {
        public function comments()
        {
            return $this->morphMany('App\Comment', 'commentable');
        }
    }

**Câu 9**. Hãy cho biết sự khác nhau giữa Session và Cookie trong PHP

​Sự khác nhau giữa `Session` và `Cookie`:

- Session có thể lưu trữ bất kì kiểu dữ liệu nào bởi vì giá trị lưu trữ của nó là kiểu object còn Cookie chỉ có thể lưu trữ kiểu dữ liệu String.
- Session được lưu trữ ở phía server còn Cookie được lưu ở phía client. Dữ liệu session không dễ dàng được sửa đổi vì chúng được lưu trữ ở phía máy chủ. Trong khi đó, dữ liệu Cookie sẽ dễ dàng hơn vì được lưu trữ ở phía client.
- Session được bảo mật vì nó được lưu trữ dạng nhị phân/dạng mã hóa và được giải mã tại server. Cookie không an toàn do được lưu trữ dưới dạng văn bản ở phía client.
- Session độc lập cho mọi client. Cookie có thể hoặc không độc lập cho mọi client.
- Session không thể bị vô hiệu hóa còn Cookie có thể bị vô hiệu hóa.
- Session không giới hạn về quy mô hoặc số lượng Session được sử dụng trong một ứng dụng. Cookie bị giới hạn kích thước ở 4096 bytes và số lượng giới hạn ở 20.

**Câu 10**. Để kiểm tra một `column name` là email có phải là column của table `users` hay không, bạn kiểm tra như thế nào trong Laravel. Cho ví dụ

​Ví dụ để kiểm tra column name là `email` có phải là một column của table `users` hay không chúng ta có thể sử dụng Schema với hàm hasColumn() như ví dụ dưới đây:

    Schema::hasColumn('users', 'email');

Đoạn code này sẽ trả về giá trị `true` nếu bảng `users` có column `email` và trả về `false` nếu không tồn tại.

**Câu 11**. Để tạo một Rule trong Laravel bạn sử dụng câu lệnh nào?
    Để truyền một giá trị từ Request vào trong Rule bạn truyền thông qua method nào
    Cho ví dụ về cách truyền

​- Để tạo một Rule trong Laravel chúng ta sử dụng câu lệnh: 
    
    php artisan make:rule `Tên rule cần tạo` 

Nếu chúng ta cần truyền giá trị của input đang validate thì chúng ta sử dụng method  `__invoke($attribute, $value, $fail)` được sinh ra khi ta thêm hậu tố `--invokable` trong câu lệnh với :

+ `$attribute` là tên của input đang được validate. 
+ `$value` là giá trị của nó.
+ `$fail` là một callback sẽ được gọi đến khi giá trị không vượt qua việc validate với một message lỗi.

Nếu chúng ta cần truyền thêm giá trị khác nữa từ Request thì có thể truyền thêm giá trị vào phần parameter của hàm khi gọi đến ví dụ như: 

 Ở Request:

    new CheckMatchUser($this->id)

 Ở Rule:

    public $id;  
    public function __construct($id)
        {
            $this->id = $id;
        } 

**Câu 12**. Hãy cho biết cách tạo một đối tượng (object) từ một class trong PHP. cho ví dụ 

​Để có được một đối tượng(`object`) chúng ta có thể tạo một class với nhiệm vụ đóng gói các thuộc tính (`attribute`) và phương thức (`method`) chung của các đối tượng. 

Ví dụ như một class Fruit có các thuộc tính name và color, bên trong nó còn có các method để get và set các thuộc tính như `set_name`,` get_name`.

**Code**
    
    <?php
    class Fruit {
    // Attributes
    public $name;
    public $color;

    // Methods
    function set_name($name) {
        $this->name = $name;
    }
    function get_name() {
        return $this->name;
    }
    function set_color($color){
        $this->color = $color;
    }
    function get_color() {
        return $this->color;
    }
    }

    $apple = new Fruit();
    $banana = new Fruit();
    $apple->set_color('Red');
    $banana->set_color('Yellow');
    $apple->set_name('Apple');
    $banana->set_name('Banana');

    echo $apple->get_name();
    echo $apple->get_color();
    echo "<br>";
    echo $banana->get_name();
    echo $banana->get_color();

    ?>

Output:

    AppleRed
    BananaYellow

Chúng ta có thể tạo ra nhiều Đối tượng(object) từ một class bằng cách tạo mới đối tượng = new Class(), mỗi đối tượng sẽ có đầy đủ tất cả các thuộc tính và phương thức được định nghĩa trong lớp nhưng các giá trị thuộc tính của chúng cũng có thể khác nhau.
Như ở ví dụ trên cả hai đối tượng (`object`) đề được tạo ra từ class Fruit nên đều có các method `set_color()`, `set_name()` để thêm các giá trị thuộc tính cho object.

Tuy nhiên giá trị thuộc tính color của đối tượng apple sẽ là 'red' còn của banana sẽ là 'yellow' bởi vì chúng ta đã thêm các giá trị khác nhau cho mỗi đối tượng.

**Câu 13**. Hãy cho biết ý nghĩa của abstract class, khi nào nên sử dụng abstract class

​`Abstract class` là một class chứa các phương thức trừu tượng. Phương thức abstract của abstract class không được phép khai báo nội dung phương thức. Nó chỉ có thể định nghĩa tên cũng như các tham số đầu vào. 

Có 2 loại method là `abstract method` và `method thường`:
  + abstract method là method trống không có thực thi.
  + method thường là method có thực thi.

Các lớp khác khi kế thừa abstract class sẽ phải định nghĩa các phương thức của nó, một lớp chỉ có thể kế thừa một abstract class.

Chúng ta sử dụng abstract class khi một nhóm đối tượng có cùng bản chất kế thừa từ một class.

**Câu 14**. Hãy cho biết ý nghĩa của interface, khi nào nên sử dụng interface

`​Interface` là một Template và không phải là một lớp đối tượng. Tất cả các hàm trong `interface` đều ở dạng khai báo, không được định nghĩa nội dung, là phương thức trừu tượng và chỉ có thể được định nghĩa với khả năng là public.

Một đối tượng có thể implement nhiều `interface` cùng lúc, khi implement một interface thì nó phải khai báo và định nghĩa tất cả các hàm trong Interface đó. 

Chúng ta sử dụng interface khi một nhóm đối tượng không có cùng bản chất nhưng chúng có hành động giống nhau.

**Câu 15**. Hãy cho biết sự giống và khác nhau giữa where và whereIn trong Laravel

​Sự giống nhau giữa `where` và `whereIn` là đều dùng để thêm mệnh đề where vào truy vấn. 

Sự khác nhau: 
`where` cơ bản có 3 tham số truyền vào đó là tên của cột, toán tử so sánh và giá trị để so sánh với cột.

`where` sử dụng để trả về các row thỏa mãn điều kiện mà chúng ta thêm vào ví dụ như:

    $users = DB::table('users')->where('age', '<', 18)->get(); 
Sẽ trả về các bản ghi của users có giá trị cột age nhỏ hơn 18. 

`whereIn` có 2 tham số truyền vào là tên của cột và mảng các giá trị so sánh.

`whereIn` dùng để kiểm tra giá trị của cột nằm trong một mảng ví dụ:

    $users = DB::table('users')
                    ->whereIn('age', [18, 19, 20])
                    ->get();    
Giá trị trả về sẽ là các bản ghi của users có giá trị giống với các giá trị có trong mảng tham số truyền vào là 18,19,20;

**Câu 16**. Hãy cho biết ý nghĩa của việc đặt tên route trong laravel. Cho ví dụ về cách đặt tên route trong Laravel

​Chúng ta có thể đặt tên cho `route` bằng phương thức name để dễ dàng tương tác thay vì phải nhớ các `URI` của từng route.

Ví dụ:

    Route::get('user/profile', function () {
    })->name('profile');

Thay vì phải nhớ URI 'user/profile' chúng ta đã đặt tên cho bằng 'profile', khi cần chuyển hướng đến chúng ta chỉ cần dùng name này của nó 

    return redirect()->route('profile');

**Câu 17**. Một object user có attribute là first_name và last_name. Giá trị của first_name là `Mr.` giá trị của `last_name` là Laravel. Định nghĩa trong model để có thể tạo ra một attribute mới `fullname`. Fullname là giá trị của first_name + last_name

​Ở trong Model user chúng ta sẽ sử dụng Accessors trong Eloquent để tạo ra attribute `fullname` này từ hai thuộc tính có sẵn `first_name` và `last_name`.
 
    protected $appends = ['full_name'];
    public function getFullNameAttribute()
        {
            return $this->first_name . ' ' . $this->last_name;
        }

Bằng cách thêm hàm getFullNameAtribute bạn đã có quyền truy cập vào thuộc tính `full_name` được tạo ra bởi việc nối chuỗi `first_name` và `last_name`, tuy nhiên bạn cần sử dụng thêm $appends trong Eloquent để có thể thêm thuộc tính `full_name` vào đối tượng user. full_name trả về của object đưa ra trong bài sẽ là `Mr. Laravel`

**Câu 18**. Một product có thể thuộc nhiều category và một category có thể có nhiều product
Hãy viết Relationship thể hiện điều này (viết nó trong model Product, User)
Model Product:
    ​<?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Product extends Model
    {
        public function categories()
        {
             return $this->belongsToMany(Category::class, 'CategoryProduct', 'product_id', 'category_id');
        }
    }

Model Category:
    ​<?php

    namespace App;

    use Illuminate\Database\Eloquent\Model;

    class Category extends Model
    {
        public function products()
        {
            return $this->belongsToMany(Product::class);
        }
    }

**Câu 19**. Điều gì sảy ra khi bạn submit một form không có input name='_token'

CSRF viết tắt của cross-site request forgery là một loại tấn công giả mạo khi các lệnh trái phép được thực hiện thay cho một người dùng đã được xác thực. 

​Nếu không có input ẩn `name='_token'` được sinh ra bằng cách sử dụng `@csrf` thì một trang web độc hại có thể tấn công chúng ta bằng cách gửi HTML form đến URL nhận request POST của form đó và thêm được bản ghi không phải của người dùng đã xác thực vào trong hệ thống. 

Khi chúng ta không thêm input name='_token' vào form thì laravel sẽ trả về cho bạn lỗi `419 PAGE EXPIRED` ví dụ như khi bạn POST một form.
Đây là một mã lỗi không chính thức được dùng riêng trong laravel framework để biểu thị CSRF token đang bị thiếu hoặc đã hết hạn.

**Câu 20**. Làm thế nào để kiểm tra một table đã tồn tại trong database (trong Laravel)

​Chúng ta có thể kiểm tra một table có tồn tại trong database hay không trong Laravel bằng cách sử dụng

    Schema::hasTable('mytable'); 

Với mytable là tên table mà chúng ta đang muốn kiểm tra.
Nếu tồn tại table thì giá trị trả về sẽ là `true` còn nếu chưa tồn tại thì giá trị trả về sẽ là `false`.

`Schema Builder` Là một class trong bộ Facades của Laravel nó giúp chúng ta làm việc với tất cả các cơ sở dữ liệu mà Laravel hỗ trợ với các hàm được định nghĩa sẵn
​
