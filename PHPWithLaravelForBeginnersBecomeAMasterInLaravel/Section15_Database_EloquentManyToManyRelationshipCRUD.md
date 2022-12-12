# Section 15: Database - Eloquent Many to Many Relationship CRUD

### New - Create a new laravel installation
```bash
$ laravel new manytomany
$ cd manytomany
$ git init
$ git add .
$ git commit -m "initial commit"
$ git remote add origin https://github.com/edmundmagnojr-nabepero/manytomany.git
$ git push --set-upstream origin master
```

### Database migrations
```bash
#make Role model
$ php artisan make:model Role -m
$ php artisan make:migration create_role_user_table --create=role_user

#migrate migrations
$ php artisan migrate
```
+ create Role table
```php
public function up()
    {
        Schema::create('roles', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }
```
+ create Role_User table
```php
public function up()
    {
        Schema::create('role_user', function (Blueprint $table) {
            $table->id();
            $table->integer('user_id')->unsigned()->nullable()->index();
            $table->integer('role_id')->unsigned()->nullable()->index();
            $table->timestamps();
        });
    }
```

### Relationships and mass assignment
+ create roles()
```php
public function roles(){
        return $this->belongsToMany('App\Models\Role');
    }
```
+ role model
```php
protected $fillable = ['name'];
```

### Inserting data
+ create insert role
```php
use App\Models\User;
use App\Models\Role;

Route::get('/create', function(){
    $user = User::find(1);

    $role = new Role(['name'=>'Administrator']);

    $user->roles()->save($role);
});
```

### Reading data
+ read user role
```php
Route::get('/read', function(){
    $user = User::findOrFail(1);
    foreach($user->roles as $role){
        echo $role->name."<br>";
    }
});
```

### Updating data
+ update user role
```php
Route::get('/update', function(){
    $user = User::findOrFail(1);
    if($user->has('roles')){
        foreach($user->roles as $role){
            if($role->name == 'Administrator'){
                $role->name = 'subscriber';
                $role->save();
            }
        }
    }
});
```

### Deleting data
+ delete user role
```php
Route::get('/delete', function(){
    $user = User::findOrFail(1);
    foreach($user->roles as $role){
        $role->whereId(1)->delete();
    }
});
```

### Attaching, detaching and syncing
```php
#Attaching
Route::get('/attach', function(){
    $user = User::findOrFail(1);
    $user->roles()->attach(2);
});

#Detaching
Route::get('/detach', function(){
    $user = User::findOrFail(1);
    $user->roles()->detach(2);
});

#Syncing
Route::get('/sync', function(){
    $user = User::findOrFail(1);
    $user->roles()->sync([2,3]);
});
```

### Resources
[Eloquent: Relationships](https://laravel.com/docs/5.2/eloquent-relationships)
