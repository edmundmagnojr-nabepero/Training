# Section 5: Laravel Fundamentals - Controllers

### Intro to chapter with Edwin
+ **Controllers** gets information from models and display to views or from views to database

### Creating Controllers
```bash
namespace
```
+ allows to use same name in the application
```bash
use
``` 
+ import specif class or namespace in the file
```bash
php artisan make:controller controllerName
``` 
+ creates controller using terminal
```bash
php artisan make:controller --resource controllerName
``` 

### Laravel 8 - Important Update
+ change 
```php
Route::get('/', 'HomeController@index')
```
+ to
```php
Route::get('/checking', '\App\Http\Controllers\HomeController@index');
```
+ or
```php
use App\Http\Controllers\HomeController; //
 
 
Route::get('/', [EdwinsController::class, 'index']);
```

### Routing Controllers
```php
Route::get('/post', [PostsController::class, 'index']);
```

### Passing Data
```php
Route::get('/post/{id}', [PostsController::class, 'index']);
```

### Resources and Controllers
```php
Route::resource('posts', PostsController::class);
```
```bash
php artisan route:list
```
[Http Controllers](https://laravel.com/docs/5.2/controllers)
