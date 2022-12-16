# Section 31: New Application - Laravel 7 - Roles & Permissions - Migrations & Relationships

### Creating migrations part 1
```bash
$ php artisan make:model Permission -mc
$ php artisan make:model Role -mc
$ php artisan make:migration create_users_permissions_table --create=permission_user
$ php artisan make:migration create_users_roles_table --create=role_user
$ php artisan make:migration create_roles_permissions__table --create=permission_role
```

### Creating migrations part 2
+ permission table
```php
public function up()
    {
        Schema::create('permissions', function (Blueprint $table) {
            $table->id();
            $table->string('name')->unique();
            $table->string('slug')->unique();
            $table->timestamps();
        });
    }
```
+ roles table
```php
public function up()
    {
        Schema::create('roles', function (Blueprint $table) {
            $table->id();
            $table->string('name')->unique();
            $table->string('slug')->unique();
            $table->timestamps();
        });
    }
```
+ permission user pivot table
```php
public function up()
    {
        Schema::create('permission_user', function (Blueprint $table) {
            $table->primary(['user_id', 'permission_id']);
            $table->foreignId('user_id')->constrained()->onDelete('cascade');
            $table->foreignId('permission_id')->constrained()->onDelete('cascade');
            $table->timestamps();
        });
    }
```
+ role user pivot table
```php
public function up()
    {
        Schema::create('role_user', function (Blueprint $table) {
            $table->primary(['user_id', 'role_id']);
            $table->foreignId('user_id')->constrained()->onDelete('cascade');
            $table->foreignId('role_id')->constrained()->onDelete('cascade');
            $table->timestamps();
        });
    }
```
+ permission role pivot table
```php
public function up()
    {
        Schema::create('permission_role', function (Blueprint $table) {
            $table->primary(['permission_id', 'role_id']);
            $table->foreignId('permission_id')->constrained()->onDelete('cascade');
            $table->foreignId('role_id')->constrained()->onDelete('cascade');
            $table->timestamps();
        });
    }
```
+ migrate migrations
```bash
$ php artisan migrate
```

### Setting up relationships
+ Permission model
```bash
public function roles(){
        return $this->belongsToMany(Role::class);
    }

    public function users(){
        return $this->belongsToMany(User::class);
    }
```
+ Role model
```bash
public function permissions(){
        return $this->belongsToMany(Permission::class);
    }

    public function users(){
        return $this->belongsToMany(User::class);
    }
```
+ User model
```bash
public function permissions(){
        return $this->belongsToMany(Permission::class);
    }

    public function roles(){
        return $this->belongsToMany(Role::class);
    }
```

### Using Tinker to Insert Roles and Permissions
+ add in role and permission model
```php
protected $guarded = [];
```
+ tinker
```bash
#run tinker
$ php artisan tinker
# find user 1
$user = App\Models\User::find(1)
# create admin role
$admin = App\Models\Role::create(['name'=>'Admin', 'slug'=>'admin'])
#attach admin role to user 1
$user->roles()->attach($admin)
#create view dashboard permission
$view_dashboard = App\Models\Permission::create(['name'=>'View Dashboard', 'slug'=>'view-dashboard'])
#attach view dashboard permission to admin role
$admin->permissions()->attach($view_dashboard)
#check if admin has users
$admin->users
#check if admin has permissions
$admin->permissions
#exit tinker
exit
```

### Using Permission on Dashboard Example
+ make userHasRole function in user model
```php
public function userHasRole($role_name){
        foreach($this->roles as $role){
            if($role_name == $role->name){
                return true;
            }
        }
    }
```
+ check if user role is admin
```php
@if(auth()->user()->userHasRole('Admin'))
<h1 class="h3 mb-4 text-gray-800">Dashboard</h1>
@endif
```
