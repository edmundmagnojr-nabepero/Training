# Section 18: Forms and Validation

### Section Intro with Edwin Diaz

### Setting up migration and relations

### Setting up views and routes
+ posts route
```php
Route::resource('/posts', PostsController::class);
```

### 4-Setting the markup for create
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

### Setting up the controller and view
```php
#create function
return view('posts.create');

#store function
return $request->title;
```

### Persisting data to database
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

### Reading data
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

### Showing individual posts and updating
+ delete post tags
```php
<h1>{{$post->title}}</h1>
```

### Preparing the edit view and setting up the token
```php
<form method="post" action="/posts/{{$post->id}}">
    {{csrf_field()}}    
    <input type="hidden" name="_method" value="PUT">
    <input type="text" name="title" placeholder="Enter Title" value="{{$post->title}}">
    <input type="submit" name="submit">
</form>
```

### 10- Editing records and redirecting
+ delete post tags
```php
$post = Post::findOrFail($id);
        $post->update($request->all());
        return redirect('/posts');
```

### Deleting
```php
<form method="post" action="/posts/{{$post->id}}">
    {{csrf_field()}}    
    <input type="hidden" name="_method" value="DELETE">
    <input type="submit" value="DELETE">

</form>
```

### Resources
[Eloquent: Relationships](https://laravel.com/docs/5.2/eloquent-relationships)
