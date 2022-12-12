# Section 16: Database - Eloquent Polymorphic Relationship CRUD

### New - Create a new laravel installation with Database setup
```bash
$ laravel new polymorphic
$ cd polymorphic
$ git init
$ git add .
$ git commit -m "initial commit"
$ git remote add origin [https://github.com/edmundmagnojr-nabepero/manytomany.git](https://github.com/edmundmagnojr-nabepero/polymorphic.git)
$ git push -u origin master


#Database migrations

#make Staff model
$ php artisan make:model Staff -m

#make Product model
$ php artisan make:model Product -m

#make Photo model
$ php artisan make:model Photo -m

#migrate migrations
$ php artisan migrate
```
+ create tables
```php
#Staff table
public function up()
    {
        Schema::create('staff', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }
   
#Product table
public function up()
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }
    
#Photo table
public function up()
    {
        Schema::create('photos', function (Blueprint $table) {
            $table->id();
            $table->string('path');
            $table->integer('imageable_id');
            $table->string('imageable_type');
            $table->timestamps();
        });
    }
```

### Relationships and mass assignment
+ photo model
```php
protected $fillable = ['path'];

    public function imageable(){
        return $this->morphTo();
    }
```
+ product model
```php
protected $fillable = ['name'];

    public function photos(){
        return $this->morphMany('App\Models\Photo', 'imageable');
    }
```
+ staff model
```php
protected $fillable = ['name'];

    protected $fillable = ['name'];

    public function photos(){
        return $this->morphMany('App\Models\Photo', 'imageable');
    }
```

### Inserting data
+ create insert role
```php
use App\Models\Photo;
use App\Models\Product;
use App\Models\Staff;

Route::get('/create', function(){
    $staff = Staff::find(1);
    $staff->photos()->create(['path'=>'example.jpg']);
});
```

### Reading data
+ read staff photo path
```php
Route::get('/read', function(){
    $staff = Staff::findOrFail(1);
    foreach($staff->photos as $photo){
        echo $photo->path;
    }
});
```

### Updating data
+ update staff photo
```php
Route::get('/update', function(){
    $staff = Staff::findOrFail(1);
    $photo = $staff->photos()->whereId(1)->first();
    $photo->path = "Update example.jpg";
    $photo->save();
});
```

### Deleting data
+ delete user role
```php
Route::get('/delete', function(){
    $staff = Staff::findOrFail(1);
    $staff->photos()->whereId(1)->delete();
});
```

### Extra Techniques
```php
#Assign
Route::get('/assign', function(){
    $staff = Staff::findOrFail(1);
    $photo = Photo::findOrFail(2);
    $staff->photos()->save($photo);
});

#Unassign
Route::get('/un-assign', function(){
    $staff = Staff::findOrFail(1);
    $staff->photos()->whereId(2)->update(['imageable_id'=>'0', 'imageable_type'=>'']);
});
```

### Resources
[Eloquent: Relationships](https://laravel.com/docs/5.2/eloquent-relationships)
