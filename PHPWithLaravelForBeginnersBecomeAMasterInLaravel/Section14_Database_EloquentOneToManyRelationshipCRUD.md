# Section 14: Database - Eloquent One to Many Relationship CRUD

### New - Create a new laravel project

### Database migrations
```bash
#make Post model
$ php artisan make:model Post -m

#migrate migrations
$ php artisan migrate
```
+ create Post table
```php
public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->integer('user_id')->unsigned()->nullable()->index();
            $table->string('title');
            $table->text('body');
            $table->timestamps();
        });
    }
```

### Relationships and mass assignment
+ create posts()
```php
public function posts(){
        return $this->hasMany('App\Models\Post');
    }
```
+ post model
```php
protected $fillable = [
        'title',
        'body'
    ];
```

### Inserting data
+ insert new user using tinker
```bash
$ php artisan tinker
> App\Models\User::create(['name'=>'Edwin Diaz','email'=>'edwin@codingfaculty.com','password'=>bcrypt("123")]);
> exit
```
+ create insert route
```php
use App\Models\User;
use App\Models\Post;

Route::get('/create', function(){
    $user = User::findOrFail(1);
    $post = new Post(['title'=>'My first post with Edwin Diaz', 'body'=>'I love larvel with Edwin Diaz']);
    $user->posts()->save($post);
});
```

### Reading data
+ read user post
```php
Route::get('/read', function(){
    $user = User::findOrFail(1);
    foreach($user->posts as $post){
        echo $post->title."<br>";
    }
    // return $user->posts;
});
```

### Updating data
+ update user post
```php
Route::get('/update', function(){
    $user = User::find(1);
    $user->posts()->whereId(1)->update(['title'=>'I love laravel', 'body'=>'This is awasome, thank you Edwin Diaz']);
    $user->posts()->where('id','=','2')->update(['title'=>'I love laravel 2', 'body'=>'This is awasome, thank you Edwin Diaz']);
});
```

### Deleting data
+ delete user post
```php
Route::get('/delete', function(){
    $user = User::find(1);
    $user->posts()->whereId(1)->delete();
});
```

### Resources
[Eloquent: Relationships](https://laravel.com/docs/5.2/eloquent-relationships)
