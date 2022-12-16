# Section 30: New Application - Laravel 7

### Setting p views part 1
```bash
composer require laravel/ui
php artisan ui bootstrap --auth
npm install resolve-url-loader@^5.0.0 --save-dev --legacy-peer-deps
npm install && npm run dev
```
copy necessary files to project

### Setting p views part 2
```php
#home-master.blade.php
<!-- Blog Entries Column -->
<div class="col-md-8">
  @yield('content')
</div>
#then transfer to home.blade.php the blog content
```

### Admin View creation setup
+copy blank admin html to admin.blade.php
+ create AdminsController
```bash
$ php artisan make:controller AdminsController
```
+ write inside AdminsController
```php
public function index(){
  return view('admin.index');
}
```
+ add in route
```php
Route::get('/admin', [App\Http\Controllers\AdminsController::class, 'index'])->name('admin.index');
```
+ inside index.blade.php
```php
<x-admin>
    @section('content')
        <h1 class="h3 mb-4 text-gray-800">Dashboard</h1>
    @endsection
</x-admin>
```
+ change line 355 in admin.blade.php
```php
@yield('content')
```

### Menu links and migration
+ RouteServiceProvider.php
```php
#change from
public const HOME = '/home';
#change to
public const HOME = '/admin';
```
+ add in home-master.blade.php
```php
@if(Auth::check())
  <li class="nav-item">
    <a class="nav-link" href="{{route('admin.index')}}">Admin</a>
  </li>
  @else
  <li class="nav-item">
    <a class="nav-link" href="/login">Login</a>
  </li>
@endif
```

```php
#change from
<a class="sidebar-brand d-flex align-items-center justify-content-center" href="index.html">
#change to
<a class="sidebar-brand d-flex align-items-center justify-content-center" href="{{route('home')}}">
```
+ connect to database
```bash
php artisan migrate
```

### Blog Post view
+ make Post model and controller
```bash
$ php artisan make:model Post -mc
```
+ add in route
```php
Route::get('/post', [App\Http\Controllers\PostController::class, 'show'])->name('post');
```
+ add in route
```php
<x-home-master>
  @section('content')
      #copy blog content from blog post index file
  @endsection
</x-home-master>
```
+ write show function in post controller
```php
public function show(){
  return view('blog-post');
}
```

### Posts migration
+ create post table
```php
public function up() {
  Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->string('title');
    $table->text('post_image')->nullable();
    $table->text('body');
    $table->timestamps();
  });
}
```

### Creating dummy data with relationships - Users with Posts
+ Post model
```php
protected $guarded = [];

public function user(){
  return $this->belongsto(User::class);
}
```
+ User model
```php
public function posts(){
  return $this->hasMany(Post::class);
}
```
+ create PostFactory
```bash
$ php artisan make:factory PostFactory --model=Post
```
+ PostFactory
```php
use App\Models\User;

public function definition() {
  return [
    'user_id' => User::factory(),
    'title' => $this->faker->sentence,
    'post_image' => $this->faker->imageUrl('900', '300'),
    'body' => $this->faker->paragraph
  ];
}
```
+ DatabaseSeeder
```php
public function run() {
  \App\Models\User::factory(10)->create()->each(function($user){
    $user->posts()->save(\App\Models\Post::factory()->make());
  });
}
```
+ seed the database
```bash
$ php artisan db:seed
```

### Displaying posts on home page
+ modify homecontroller
```php
public function index() {
  $posts = Post::all();
  return view('home', ['posts' => $posts]);
}
```
+ modify blog post to come from database
```php
@foreach($posts as $post)
  <!-- Blog Post -->
  <div class="card mb-4">
    <img class="card-img-top" src="{{$post->post_image}}" alt="Card image cap">
    <div class="card-body">
      <h2 class="card-title">{{$post->title}}</h2>
      <p class="card-text">{{Str::limit($post->body, '50', '...')}}</p>
      <a href="#" class="btn btn-primary">Read More &rarr;</a>
    </div>
    <div class="card-footer text-muted">
      Posted on {{$post->created_at}} by
      <a href="#">Start Bootstrap</a>
    </div>
  </div>
@endforeach
```

### Displaying individual posts
+ change route
```php
Route::get('/post/{post}', [App\Http\Controllers\PostController::class, 'show'])->name('post');
```
+ change Read More link
```php
<a href="{{route('post', $post->id)}}" class="btn btn-primary">Read More &rarr;</a>
```
+ change show function
```php
public function show(Post $post){
  return view('blog-post', ['post'=>$post]);
}
```
+ get data from database
```php
<h1 class="mt-4">{{$post->title}}</h1>
<a href="#">{{$post->user->name}}</a>
<p>Posted {{$post->created_at->diffForHumans()}}</p>
<img class="img-fluid rounded" src="{{$post->post_image}}" alt="">
<p>{{$post->body}}</p>
```

### Creating a post from admin - Part 1
+ add route
```php
Route::post('/admin/posts', [App\Http\Controllers\PostController::class, 'store'])->name('post.store');
```
+ add create post form in create.blade.php
```php
<form method="post" action="{{route('post.store')}}" enctype="multipart/form-data">
  @csrf
  <div class="form-group">
    <label for="title">Title</label>
    <input  type="text"
            name="title" 
            class="form-control" 
            id="title" 
            aria-describedby="" 
            placeholder="Enter Title">
  </div>
  <div class="form-group">
    <label for="file">File</label>
    <input  type="file" 
            name="post_image" 
            class="form-control" 
            id="post_image">
  </div>
  <div class="form-group">
    <textarea   name="body" 
                id="body" 
                cols="30" 
                ows="10" 
                class="form-control"></textarea>
  </div>
  <button type="submit" class="btn btn-primary">Submit</button>
</form>
```
+ add store function in PostController
```php
public function store(){
  dd(request()->all());
}
```


### Creating a post from admin - Part 2
+chang store function content for validation
```php
public function store(){
  $inputs = request()->validate([
    'title'=>'required|min:8|max:255',
    'post_image'=>'file',
    'body'=>'required'
  ]);
  if(request('post_image')){
    $inputs['post_image'] = request('post_image')->store('images');
  }
}
```

### Creating a post from admin - Part 3
+ change from local in env
```php
FILESYSTEM_DRIVER=public
```
+ link public to storage
```bash
$ php artisan storage:links
```
+ add in store function
```php
auth()->user()->posts()->create($inputs);
return back();
```

### Displaying a post from admin - Part 1
+ change to View All Post
```php
<a class="collapse-item" href="{{route('post.index')}}">View All Posts</a>
```
+ add route
```php
Route::get('/admin/posts', [App\Http\Controllers\PostController::class, 'index'])->name('post.index');
```
+ create index.blade.php
```php
<x-admin-master>
    @section('content')
    <h1>All Posts</h1>
    @endsection
</x-admin-master>
```
+ add index functio in postcontroller
```php
public function index(){
  return view('admin.posts.index');
}
```

### isplaying a post from admin - Part 2
+ copy data table to index
+ add script section
```php
  @section('scripts')
  <!-- Page level plugins -->
  <script src="{{asset('vendor/datatables/jquery.dataTables.min.js')}}"></script>
  <script src="{{asset('vendor/datatables/dataTables.bootstrap4.min.js')}}"></script>

  <!-- Page level custom scripts -->
  <script src="{{asset('js/demo/datatables-demo.js')}}"></script>
  @endsection
```

### Displaying a post from admin - Part 3
+ modify index function
```php
public function index(){
        $posts = Post::all();
        return view('admin.posts.index', ['posts'=>$posts]);
    }
```
+ change table content
```php
<table class="table table-bordered" id="dataTable" width="100%" cellspacing="0">
  <thead>
    <tr>
      <th>Id</th>
      <th>Title</th>
      <th>Image</th>
      <th>Created At</th>
      <th>Updated At</th>
    </tr>
  </thead>
  <tfoot>
    <tr>
      <th>Id</th>
      <th>Title</th>
      <th>Image</th>
      <th>Created At</th>
      <th>Updated At</th>
    </tr>
  </tfoot>
  <tbody>
    @foreach($posts as $post)
    <tr>
      <td>{{$post->id}}</td>
      <td>{{$post->title}}</td>
      <td>
        <img src="{{$post->post_image}}" height="40px" alt="">
      </td>
      <td>{{$post->created_at->diffForHumans()}}</td>
      <td>{{$post->updated_at->diffForHumans()}}</td>
    </tr>
    @endforeach
  </tbody>
</table>
```

### Displaying the owner of the post
```php
<th>Owner</th>
<td>{{$post->user->name}}</td>
```

### Code - Important update for image accessor
```php
getPostImageAttribute($value) {
if (strpos($value, 'https://') !== FALSE || strpos($value, 'http://') !== FALSE) {
    return $value;
  }
  return asset('storage/' . $value);
}
```

### Example of Mutators and Accessors with an image path
+ mutators
```php
public function setPostImageAttribute($value){
  $this->attributes['post_image']->asset($value);
}
```
+ accessors
```php
public function getPostImageAttribute($value){
  return asset($value);
}
```

### Flash messaging and deleting
+ delete button
```php
<td>
  <form method="post" action="{{route('post.destroy', $post->id)}}" enctype="multiparat/form-data">
    @csrf
    @method('DELETE')
    <button class="btn btn-danger">Delete</button>
  </form>
</td>
```
+ flash message
```php
@if(Session::has('message'))
  <div class="alert alert-danger"> {{Session::get('message')}} </div>
@else
  <div class="alert alert-success"> {{Session::get('post-created-message')}} </div>
@endif
```
+ add route
```php
Route::delete('/admin/posts/{post}/destroy', [App\Http\Controllers\PostController::class, 'destroy'])->name('post.destroy');
```
+ add destroy function
```php
public function destroy(Post $post){
  $post->delete();
  Session::flash('message', 'Post was deleted');
  return back();
}
```
+ add message in store function and redirect to post index
```php
session()->flash('post-created-message', 'Post was created'.$inputs['title']);
return redirect()->route('post.index');
```

### Updating a post part 1
+ add edit route
```php
Route::get('/admin/posts/{post}/edit', [App\Http\Controllers\PostController::class, 'edit'])->name('post.edit');
```
+ create edit.blade.php and copy everything from create.blade.php
+ create edit function
```php
public function edit(Post $post){
  return view('admin.posts.edit', ['post'=>$post]);
}
```

### Updating a post part 2
+ change route in edit.blade.php
```php
action="{{route('post.update', $post->id)}}"
```
+ insert under @csrf
```php
@method('PATCH')
 ```
 + create update function
```php
public function update(Post $post){
  $inputs = request()->validate([
    'title'=>'required|min:8|max:255',
    'post_image'=>'file',
    'body'=>'required'
  ]);
  if(request('post_image')){
    $inputs['post_image'] = request('post_image')->store('images');
    $post->post_image = $inputs['post_image'];
  }
  $post->title = $inputs['title'];
  $post->body = $inputs['body'];
  $post->save();
  session()->flash('post-updated-message', 'Post was updated'.$inputs['title']);
  return redirect()->route('post.index');
}
 ```
 + update flash message
 ```php
 @if(Session::has('message'))
  <div class="alert alert-danger"> {{Session::get('message')}} </div>
 @elseif(Session::has('post-created-message'))
  <div class="alert alert-success"> {{Session::get('post-created-message')}} </div>
 @elseif(Session::has('post-updated-message'))
  <div class="alert alert-success"> {{Session::get('post-updated-message')}} </div>
 @endif
 ```
 + add update route
 ```php
 Route::patch('/admin/posts/{post}/update', [App\Http\Controllers\PostController::class, 'update'])->name('post.update');
 ```

### Creating Authorization Policies Part 1
+ restrict user to only display posts owned by him
```php
$posts = auth()->user()->posts;
```

### Creating Authorization Policies Part 2
+ create Post model policy
```bash
$ php artisan make:policy PostPolicy --model=Post
```
+ restrict user to only edit posts owned by him
```php
$this->authorize('view', $post);
```
+ restrict user to only delete posts owned by him
```php
@can('view', $post)
                        <form method="post" action="{{route('post.destroy', $post->id)}}" enctype="multiparat/form-data">
                          @csrf
                          @method('DELETE')
                          <button class="btn btn-danger">Delete</button>
                        </form>
                        @endcan
```
+ restrict user to only edit posts owned by him in route
```php
Route::get('/admin/posts/{post}/edit', [App\Http\Controllers\PostController::class, 'edit'])->middleware('can:view,post')->name('post.edit');
```
+ check if logged in user can do rud
```php
return $user->id === $post->user_id;
```

### Creating Authorization Policies Part 3 and Refactoring
+ store method
```php
$this->authorize('create', Post::class);
```

### Laravel pagination
+ pagination
```php
<div class="d-flex">
          <div class="mx-auto">
            {{$posts->links()}}
          </div>
         </div>
```
+ view pagination folder
```bash
$ php artisan vendor:publish
```
