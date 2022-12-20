# Section 36: Application - Legacy Version 5.2

### Why this section? Please READ!

### Laravel Project Reference Files

### New Laravel Installation
```bash
$ laravel new codehackingApp
$ cd codehackingApp
$ git init
$ git add .
$ git commit -m "initial commit"
$ git remote add origin https://github.com/edmundmagnojr-nabepero/codehackingApp.git
$ git push -u origin master
```

### Database Configuration
+ create database
+ migrate pre-defined migrations to database
```bash
$ php artisan migrate
```

### Setting up Views
+ migrate pre-defined migrations to database
```bash
composer require laravel/ui
php artisan ui bootstrap --auth
npm install && npm run dev
npm install resolve-url-loader@^5.0.0 --save-dev --legacy-peer-deps
```
+ create admin folder inside views
+ create users, posts and categories folder inside admin
+ create index and edit files in users, posts and categories
+ create create file in users and posts

### Users Table Migration
+ Users table
```php
public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->integer('role_id')->index()->unsignedd()->nullable();
            $table->integer('is_active')->default(0);
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }
```
+ create Role model and migration
```bash
$ php artisan make:model Role -m
```
+ Roles table
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

### Relation Setup And Data Entry
+ role function in User model
```php
public function role(){
        return $this->belongsTo('App\Models\Role');
    }
```
+ insert administrator, author and subscriber roles in roles table
+ register a user

### Testing Relation with Tinker
+ 
```bash
# run tinker
$ php artisan tinker
# find user with id number 1
> $user = App\Models\User::find(1)
# check $user role
> $user->role
# exit tinker
> exit
```

### Admin Controller and Routes
+ add AdminUsersController resource route
```php
use App\Http\Controllers\AdminUsersController;

Route::resource('admin/users', AdminUsersController::class);
```
+ create AdminUsers Controller
```bash
$ php artisan make:controller --resource AdminUsersController
```

### Testing Methods
+ AdminUsersController index method
```php
public function index()
    {
        return view('admin.users.index');
    }
```

### Installing NodeJs and Files Download - UPDATE - Watch Entire Lecture
+ 
```php

```

### Gulp and Assets
+ 
```php

```

### Admin Master File - Download File
+ 
```php

```

### Version Control and Modifying Master Page
+ 
```php

```

### Displaying Users
+ 
```php

```

### Create Page
+ 
```php

```

### Laravel Collective HTML Package
+ 
```php

```

### Testing Form and Creating Form Fields
+ 
```php

```

### Working on User from Status Field
+ 
```php

```

### Populating the User Roles Select
+ 
```php

```

### Password Field and Custom Request
+ 
```php

```

### Displaying Errors and Including with Blade
+ 
```php

```

### Adding Upload File Feature to Form
+ 
```php

```

### Persisting data and Fixing Bug
+ 
```php

```

### User Photos Migration - Relation - Mass Assignment
+ 
```php

```

### Creating Links and More
+ 
```php

```

### Persisting User with Photo Part 1
+ 
```php

```

### Persisting User with Photo Part 2
+ 
```php

```

### Displaying Photos Using an Accessor
+ 
```php

```

### Edit Users Part 1 - Setting up the Form
+ 
```php

```

### Edit User Part 2 - Displaying Images and Status
+ 
```php

```

### Updating Part 1 and Displaying Errors
+ 
```php

```

### Updating Part 2
+ 
```php

```

### Updating Part 3 - Fixing Loose Ends
+ 
```php

```

### Security Part 1 - Middleware Registration
+ 
```php

```

### Security Part 2 - Middleware - Custom Method and 404 Page
+ 
```php

```

### Deleting Users
+ 
```php

```

### Some Flash Messages
+ 
```php

```

### Deleting Images from the Directory
+ 
```php

```

### Adding More Security
+ 
```php

```
