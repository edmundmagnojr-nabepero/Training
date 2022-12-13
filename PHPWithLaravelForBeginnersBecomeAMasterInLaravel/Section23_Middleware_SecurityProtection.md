# Section 23: Middleware - Security / Protection

### Section Intro

### Create New Laravel Installation
```bash
$ laravel new middleware
$ cd middleware
$ git init
$ git add .
$ git commit -m "initial commit"
$ git remote add origin https://github.com/edmundmagnojr-nabepero/middleware.git
$ git push -u origin master

composer require laravel/ui
php artisan ui vue --auth
php artisan migrate
#change to
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
#from this
<link href="{{ asset('css/app.css') }}" rel="stylesheet">
#change to
<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
#from this
<link href="{{ asset('js/app.js') }}" rel="stylesheet">
#make middleware
$ php artisan make:middleware RoleMiddleware
```
### Registering a New Middleware and Using it
```php
#add to routeMiddleware
'role' => \App\Http\Middleware\RoleMiddleware::class,

#new route
Route::get('/admin/user/roles', ['middleware'=>'role', function(){
  return "Middleware role";
}]);

#modify handle function in roleMiddleware
public function handle(Request $request, Closure $next){
  return redirect('/');
}
```

### A More Practical Way to Use Middlewares 1 - Roles, Migration and Relations Setup
```bash
#add IsAdmin middleware
$ php artisan make:middleware IsAdmin
```
```php
#add isAdmin function in user model
public function isAdmin(){
  if($this->role->name == 'administrator'){
    return true;
  }
  return false;
}
    
#add in kernel
'isAdmin' => \App\Http\Middleware\IsAdmin::class,

#route
Route::get('/', function () {
    $user = Auth::user();
    if($user->isAdmin()){
        echo "this user is an administrator";
    }
});
```

### A More Practical Way to Use Middlewares 2 - Custom Method
```bash
#add AdminController
$ php artisan make:controller AdminController
```
```php
#add admin route
Route::get('/admin', 'AdminController@index');

#add in AdminController
public function __construct(){
  $this->middleware('IsAdmin');
}
```

### A More Practical Way to Use Middlewares 3 - Custom Method
```php
#transfer to IsAdmin middleware
$user = Auth::user();
if($user->isAdmin()){
  return redirect()->intended('/admin');
}

public function index(){
  return "You are an administrator because you are seeing this page";
}
```
### A More Practical Way to Use Middlewares 4 - Custom Method
```php
#change route
Route::get('/admin', [AdminController::class, 'index']);

#revise condition
if(!($user->isAdmin())){
  return redirect('/');
}
```
### Resources
[HTTP Middleware](https://laravel.com/docs/5.2/middleware)
