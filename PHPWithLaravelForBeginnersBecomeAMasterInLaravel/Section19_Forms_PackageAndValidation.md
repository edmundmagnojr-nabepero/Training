# Section 19: Forms - Package and validation

### Section Intro with Edwin Diaz

### Installing package and testing
+ installation
```bash
$ composer require laravelcollective/html
```
+ composer.json
```php
Collective\Html\HtmlServiceProvider::class,

'Form' => Collective\Html\FormFacade::class,
      'Html' => Collective\Html\HtmlFacade::class,
```
+ create post    
```php
{!! Form::open() !!}
    <input type="text" name="title" placeholder="Enter Title">
    {{csrf_field()}}
    <input type="submit" name="submit">
{{ Form::close() }}
```
### Modifying our create form with the form package
+ posts route
```php
Route::resource('/posts', PostsController::class);
```

### Modifying our edit and delete forms with the form package
+ title form
```php
@extends('layouts.app')

@section('content')
<from method="post" action="/posts">
    <input type="text" name="title" placeholdeer="Enter Title">
    {{csrf_field()}}
    <input type="submit" name="submit">
</form>
```

### Basic Validation
```php
#create function
return view('posts.create');

#store function
return $request->title;
```

### Displaying Errors
+ delete post tags
```php
   Post::create($request->all());
   
   // $input = $request->all();
   // $input['title'] = $request->title;
   // Post::create($request->all());
   
   // $post = new Post;
   // $post->title = $request->title;
   // $post->save();
```

### Advance Validation
+ index view
```php
@extends('layouts.app')

@section('content')

<ul>
    @foreach($posts as $post)
        <li>{{$post->title}}</li>
    @endforeach
<ul>

@stop
```

### Creating Snippets with PhpStorm
+ delete post tags
```php
<h1>{{$post->title}}</h1>
```

### Resources
[Validation](https://laravel.com/docs/5.2/validation)
