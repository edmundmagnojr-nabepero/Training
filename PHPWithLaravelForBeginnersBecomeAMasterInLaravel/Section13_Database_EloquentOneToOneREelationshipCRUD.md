# Section 13: Database - Eloquent One to One Relationship CRUD

### Section Intro with Edwin Diaz

### New - Create a new laravel project with migrations
```bash
#make Address model
$ php artisan make:model Address -m

#migrate migrations
$ php artisan migrate
```
+ create Addresses table
```php
public function up()
    {
        Schema::create('addresses', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }
```

### Database configuration and migration
+ add user_id column to Addresses table
```php
public function up(){
  Schema::create('addresses', function (Blueprint $table) {
    $table->id();
    $table->integer('user_id')->unsigned()->nullable();
    $table->string('name');
    $table->timestamps();
  });
}
```

### Setting up Relations
+ create address()
```php
public function address(){
  return $this->hasOne('App\Models\Address');
}
```

### Creating data for user
+ create insert route
```php
Route::get('/insert', function(){
  $user = User::findOrFail(1);
  
  $address = new Address(['name'=>'4435 Paulina av NY NY 11218']);
  
  $user->address()->save($address);
});
```
+ address model
```php
protected $fillable = [
  'name'
];
```

### Updating data
+ update address
```php
Route::get('/update', function(){
  $address = Address::whereUserId(1)->first();
  
  $address->name = "4353 Update Av, alaska";
  
  $address->save();
});
```

### Reading and deleting data
+ read address
```php
Route::get('/read', function(){
    $user = User::find(1);
    echo $user->address->name;
});
```
+ delete address
```php
Route::get('/delete', function(){
    $user = User::findOrFail(1);
    
    $user->address()->delete();
});
```

### Resources
[Eloquent: Relationships](https://laravel.com/docs/5.2/eloquent-relationships)
