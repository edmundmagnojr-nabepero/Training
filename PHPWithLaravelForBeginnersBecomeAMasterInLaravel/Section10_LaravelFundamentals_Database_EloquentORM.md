# Section 10: Laravel Fundamentals - Database - Eloquent / ORM

### Section Intro with Edwin Diaz

### Reading Data
+ make a Post Model
```bash
php artisan make:model Post
```
+ if the table and id name is different from the model name
```php
protected $table = 'posts';
protected $primaryKey = 'id';
```
+ include the model in the route
```php
use App\Models\Post;
```
+ route code
```php
Route::get('/find', function(){
    $posts = Post::all();

    foreach($posts as $post){
        return $post->title;
    }
});
```
### Reading / Finding with Constraints
```php
Route::get('/findwhere', function(){
    $posts = Post::where('id', 2)->orderBy('id', 'desc')->take(1)->get();
    return $posts;
});
```

### More ways to retrieve Data
```php
Route::get('/findmore',function(){
    $posts = Post::findOrFail(4);
    $postswhere = Post::where('users_count', '<', 50)->firstOrFail();
    
    return $posts;
});
```

### Inserting / Saving Data
+ insert
```php
Route::get('/basicinsert', function(){
    $post = new Post;
    $post->title = "New Eloquent title insert";
    $post->content = "Wow Eloquent is really cool, look as this content.";
    $post->save();
});
```
+ update
```php
Route::get('/basicinsert2', function(){
    $post = Post::find(5);
    $post->is_admin = 0;
    $post->save();
});
```

### Creating data and configuring mass assignment
```php
Route::get('/create', function(){
    Post::create(['title'=>'The create method', 'content'=>'WOw I am learning a lot with Laravel']);
});
```

### Updating with Eloquent
```php
Route::get('/update2', function(){
    Post::where('id', 2)->where('is_admin', 1)->update(['title'=>"New PHP Title", 'content'=>'I love my instructor']);
});
```

### Deleting Data
+ delete()
```php
Route::get('/delete2', function(){
    $post = Post::find(2);
    $post->delete();
});
```
+ destroy(id)
```php
Route::get('/delete3', function(){
    Post::destroy(3);
});
```
+ destroy([id1, id2])
```php
Route::get('/delete4', function(){
    Post::destroy([4, 5]);
});
```
+ where(condition)
```php
Route::get('/delete5', function(){
    Post::where('is_admin', 0)->delete();
});
```
    

### Soft Deleting / Trashing
+ making a migration to add a deleted_at column
```bash
php artisan make:migration add_deleted_at_column_to_posts_table --table=posts
```
+ insert to Post model
```php
use Illuminate\Database\Eloquent\SoftDeletes;
```
+ insert to the body of Post model
```php
use SoftDeletes;

protected $dates = ['deleted_at'];
```
+ migration content
```php
public function up()
    {
        Schema::table('posts', function (Blueprint $table) {
            //
            $table->softDeletes();
        });
    }
```
```php
public function down()
    {
        Schema::table('posts', function (Blueprint $table) {
            //
            $table->dropColumn('deleted_at');
        });
    }
```
+ Soft delete implementation
```php
Route::get('/softdelete', function(){
    Post::find(1)->delete();
});
```

### Retrieving Deleted / Trashed Records
```php
Route::get('/readsoftdelete', function(){
    //find function
    $post = Post::find(1);
    return $post;

    //withTrashed function
    $post = Post::withTrashed()->where('id', 1)->get();
    return $post;

    //onlyTrashed function
    $post = Post::onlyTrashed()->where('id', 1)->get();
    return $post;
});
```

### Restoring Deleted / Trashed Records
```php
Route::get('/restore', function(){
    Post::withTrashed()->where('is_admin', 0)->restore();
});
```

### Deleting a Record Permanently
```php
Route::get('/forcedelete', function(){
    Post::onlyTrashed()->where('is_admin', 0)->forceDelete();
});
```

### Resources
[Eloquent: Getting Started](https://laravel.com/docs/5.2/eloquent)
