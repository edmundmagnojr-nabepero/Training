# Section 17: Database - Eloquent Polymorphic Many to Many Relationship CRUD

### New - Create a new project with some migrations - classwork
```bash
$ laravel new polymorphicmanytomany
$ cd polymorphicmanytomany
$ git init
$ git add .
$ git commit -m "initial commit"
$ git remote add origin [https://github.com/edmundmagnojr-nabepero/polymorphicmanytomany.git](https://github.com/edmundmagnojr-nabepero/polymorphic.git)
$ git push -u origin master


#Database migrations

#make Post model
$ php artisan make:model Post -m

#make Video model
$ php artisan make:model Video -m

#make Tag model
$ php artisan make:model Tag -m

#make Taggable model
$ php artisan make:model Taggable -m

#migrate migrations
$ php artisan migrate
```
+ create tables
```php
#Posts table
public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }
   
#Videos table
public function up()
    {
        Schema::create('videos', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }
    
#Tags table
public function up()
    {
        Schema::create('tags', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }
    
#Taggables table
public function up()
    {
        Schema::create('taggables', function (Blueprint $table) {
            $table->id();
            $table->integer('tag_id');
            $table->integer('taggable_id');
            $table->string('taggable_type');
            $table->timestamps();
        });
    }
```

### Relationships and mass assignment
+ tag model
```php
protected $fillable = ['name'];
```
+ post and video model
```php
protected $fillable = ['name'];

    public function tags(){
        return $this->morphToMany('App\Models\Tag', 'taggable');
    }
```

### Inserting data
+ create insert post, video and tags
```php
use App\Models\Post;
use App\Models\Video;
use App\Models\Tag;

Route::get('/create', function(){
    $post = Post::create(['name'=>'My first post']);
    $tag1 = Tag::find(1);
    $post->tags()->save($tag1);
    $video = Video::create(['name'=>'video.mov']);
    $tag2 = Tag::find(2);
    $video->tags()->save($tag2);
});
```

### Reading and Updating data
+ read post tag
```php
Route::get('/read', function(){
    $post = Post::findOrFail(1);
    foreach($post->tags as $tag){
        echo $tag;
    }
});
```
+ update post tag
```php
Route::get('/update', function(){
    // $post = Post::findOrFail(1);
    // foreach($post->tags as $tag){
    //     return $tag->whereName('PHP')->update(['name'=>'Updated PHP']);
    // }

    $post = Post::findOrFail(1);
    $tag = Tag::find(3);
    //$post->tags()->save($tag);
    $post->tags()->attach($tag);
    $post->tags()->sync([1,2]);
    
});
```

### Deleting data
+ delete post tags
```php
Route::get('/delete', function(){
    $post = Post::find(1);
    foreach($post->tags as $tag){
        $tag->whereId(1)->delete();
    }
});
```

### Resources
[Eloquent: Relationships](https://laravel.com/docs/5.2/eloquent-relationships)
