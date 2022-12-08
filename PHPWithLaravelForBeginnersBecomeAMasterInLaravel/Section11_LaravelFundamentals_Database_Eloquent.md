# Section 11: Laravel Fundamentals - Database - Eloquent...

### Section Intro with Edwin Diaz

### One to One Relationship
```php
use App\Models\User;
Route::get('/user/{id}/post', function($id){
    return User::find($id)->post->content;
});
```
+ User Model
```php
public function post(){
        return  $this->hasOne('App\Models\Post');
    }
```
+ modify create post migration
```php
public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->increments('id');
            $table->integer('user_id')->unsigned();
            $table->string('title');
            $table->text('content');
            $table->timestamps();
        });
    }
```

### The Inverse Relation
```php
Route::get('/post/{id}/user', function($id){
    return Post::find($id)->user->name;
});
```
+ Post Model
```php
public function user(){
        return $this->belongsTo('App\Models\User');
    }
```

### One to Many Relationship
```php
Route::get('/userposts', function(){
    $user = User::find(1);
    foreach($user->posts as $post){
        echo $post->title . "<br>";
    }
});
```
+ User Model
```php
public function posts(){
        return $this->hasMany('App\Models\Post');
    }
```

### Some Random Tips



### Many to Many Relationship Part 1
+ create Role model
```bash
$ php artisan make:model Role -m
```
+ make Role_User talble migration
```bash
$ php artisan make:migration create_users_roles_table --create=role_user
```
+ role table
```php
public function up()
    {
        Schema::create('role_user', function (Blueprint $table) {
            $table->increments('id');
            $table->integer('user_id');
            $table->integer('role_id');
            $table->timestamps();
        });
    }
```
+ role_user table
```php
public function up()
    {
        Schema::create('role_user', function (Blueprint $table) {
            $table->increments('id');
            $table->integer('user_id');
            $table->integer('role_id');
            $table->timestamps();
        });
    }
```

### Many to Many Relationship Part 2
+ user model
```php
public function roles(){
        return $this->belongsToMany('App\Models\Role');
    }
```
+ route
```php
Route::get('/user/{id}/role', function($id){
    $user = User::find($id)->roles()->orderBy('id', 'desc')->get();

    return $user;
    foreach($user->roles as $role){
        return $role->name;
    }
});
```


### Querying Intermediate Table
+ Customize table name and columns
```php
public function roles(){
        return $this->belongsToMany('App\Models\Role', 'user_roles', 'user_id', 'role_id');
    }
```
+ Role to User
```php
public function users(){
        return $this->belongsToMany('App\Models\User');
    }
```
+ pivot table
```php
Route::get('user/pivot', function(){
    $user = User::find(1);

    foreach($user->roles as $role){
        return $role->pivot->created_at;
    }
});
```

### Has Many Through Relation Part 1
+ create Country model
```bash
$ php artisan make:model Country -m
```
+ make migration to add country id to table users
```bash
$ php artisan make:migration add_country_id_column_to_users --table=users
```
+ create Country table
```php
public function up()
    {
        Schema::create('countries', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->timestamps();
        });
    }
```
+ add country id column to users table
```php
public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            //
            $table->integer('country_id');
        });
    }
```
+ add function to drop country id column in users table
```php
public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            //
            $table->dropColumn('country_id');
        });
    }
```

### Has Many Through Relation Part 2
+ add posts() to Country model
```php
public function posts(){
        return $this->hasManyThrough('App\Models\Post', 'App\Models\User');
    }
```
+ add in route
```php
use App\Models\Country;
Route::get('user/country', function(){
    $country = Country::find(4);
    foreach($country->posts as $post){
        return $post->title;
    }
});
```

### Polymorphic Relation Part 1
+ create Photo model
```bash
$ php artisan make:model Photo -m
```
+ add Photos table
```php
public function up()
    {
        Schema::create('photos', function (Blueprint $table) {
            $table->increments('id');
            $table->string('path');
            $table->integer('imageable_id');
            $table->string('imageable_type');
            $table->timestamps();
        });
    }
```

### Polymorphic Relation Part 2
+ add imageable() in photo
```php
public function imageable(){
        return $this->morphTo();
    }
```
+ add photo() in user
```php
public function photos(){
        return $this->morphMany('App\Models\Photo', 'imageable');
    }
```
+ add photo() in post
```php
public function photos(){
        return $this->morphMany('App\Models\Photo', 'imageable');
    }
```
+ user photos route
```php
Route::get('user/photos', function(){
    $user = User::find(1);
    foreach($user->photos as $photo){
        return $photo->path;
    }
});
```
+ post photos route
```php
Route::get('post/{id}/photos', function($id){
    $post = Post::find($id);
    foreach($post->photos as $photo){
        echo $photo->path. "</br>";
    }
});
```

### Polymorphic Relation the Inverse
+ photo id post
```php
use App\Models\Photo;
Route::get('photo/{id}/post', function($id){
    $photo = Photo::findOrFail($id);
    return $photo->imageable;
});
```

### Polymorphic Relation Many to Many Part 1
+ create Video model
```bash
$ php artisan make:model Video -m
```
+ create Tag model
```bash
$ php artisan make:model Tag -m
```
+ create Taggable model
```bash
$ php artisan make:model Taggable -m
```
+ create Videos table
```php
public function up()
    {
        Schema::create('videos', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->timestamps();
        });
    }
```
+ create Tags table
```php
public function up()
    {
        Schema::create('tags', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->timestamps();
        });
    }
```
+ create Taggable table
```php
public function up()
    {
        Schema::create('taggables', function (Blueprint $table) {
            $table->integer('tag_id');
            $table->integer('taggable_id');
            $table->integer('taggable_type');
        });
    }
```

### Polymorphic Relation Many to Many Part 2
+ create tag() in post
```php
public function tags(){
        return $this->morphToMany('App\Models\Tag', 'taggable');
    }
```
+ create posts() and videos() in tag
```php
public function posts(){
        return $this->morphedByMany('App\Models\Post', 'taggable');
    }

    public function videos(){
        return $this->morphedByMany('App\Models\Video', 'taggable');
    }
    }
```

### Polymorphic Relation Many to Many - Retrieving
+ retrieve post tag
```php
Route::get('/post/tag', function(){
    $post = Post::find(1);
    //return $post->tags;
    foreach($post->tags as $tag){
        echo $tag->name;
    }
});
```

### Polymorphic Relation Many to Many - Retrieving Owner
+ retrieve post title from tag
```php
Route::get('tag/post', function(){
    $tag = Tag::find(2);
    foreach($tag->posts as $post){
        echo $post->title;
    }
});
```

### Resources
[Eloquent: Relationships](https://laravel.com/docs/5.2/eloquent-relationships)
