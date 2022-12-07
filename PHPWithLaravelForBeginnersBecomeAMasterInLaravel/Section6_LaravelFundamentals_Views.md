# Section 6: Laravel Fundamentals - Views

### Intro to chapter with Edwin

### Creating views and custom method
+ custom function
```php
public function contact(){
        return view('contact');
    }
```
+ route
```php
Route::get('/contact', [PostsController::class, 'contact']);
```
+ create view

### Passing data to views
###### 1 input
+ custom function
```php
public function show_post($id){
  return view('post')->with('id', $id);
}
```
+ route
```php
Route::get('posts/{id}', [PostsController::class, 'show_post']);
```
+ create view
```html
<h1>Post {{$id}}</h1>
```

###### Many inputs
+ custom function
```php
public function show_post($id, $name, $password){
  return view('post', compact('id', 'name', 'password'));
}
```
+ route
```php
Route::get('posts/{id}/{name}/{password}', [PostsController::class, 'show_post']);
```
+ create view
```html
<h1>Post {{$id}} {{$name}} {{$password}}</h1>
```

### Resources
[Views](https://laravel.com/docs/5.2/views)
