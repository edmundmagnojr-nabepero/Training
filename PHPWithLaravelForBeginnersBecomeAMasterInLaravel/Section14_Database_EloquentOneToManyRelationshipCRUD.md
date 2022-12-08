# Section 14: Database - Eloquent One to Many Relationship CRUD

### New - Create a new laravel project

### Database migrations
```bash
#make Address model
$ php artisan make:model Post -m

#migrate migrations
$ php artisan migrate
```
+ create Addresses table
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
+ address model
```php
protected $fillable = [
        'title',
        'body'
    ];
```

### Inserting data
+ create insert route
```php
Route::get('/insert', function(){
  $user = User::findOrFail(1);
  
  $address = new Address(['name'=>'4435 Paulina av NY NY 11218']);
  
  $user->address()->save($address);
});
```

### Reading data
+ update address
```php
Route::get('/update', function(){
  $address = Address::whereUserId(1)->first();
  
  $address->name = "4353 Update Av, alaska";
  
  $address->save();
});
```

### Updating data
+ read address
```php
Route::get('/read', function(){
    $user = User::find(1);
    echo $user->address->name;
});
```

### Deleting data
+ delete address
```php
Route::get('/delete', function(){
    $user = User::findOrFail(1);
    
    $user->address()->delete();
});
```

### Resources
[Eloquent: Relationships](https://laravel.com/docs/5.2/eloquent-relationships)
