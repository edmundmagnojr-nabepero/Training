# Section 10: Laravel Fundamentals - Database - Eloquent...

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


### Many to Many Relationship Part 2 


### Querying Intermediate Table


### Has Many Through Relation Part 1


### Has Many Through Relation Part 2


### Polymorphic Relation Part 1


### Polymorphic Relation Part 2


### Polymorphic Relation the Inverse


### Polymorphic Relation Many to Many Part 1


### Polymorphic Relation Many to Many Part 2


### Polymorphic Relation Many to Many - Retrieving


### Polymorphic Relation Many to Many - Retrieving Owner


### Resources
[Eloquent: Relationships](https://laravel.com/docs/5.2/eloquent-relationships)
