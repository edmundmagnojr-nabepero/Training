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
+ create post
```php
{!! Form::open(['method'=>'POST', 'route'=>'posts.store']) !!}
      {{csrf_field()}}
      <div class='form-group'>
            {!! Form::label('title', 'Title:') !!}
            {!! Form::text('title', null, ['class'=>'form-control']) !!}
      </div>
      
      <div class='form-group'>
            {!! Form::submit('Create Post', ['class'=>'btn btn-primary']) !!}
      </div>

{{ Form::close() }}
```

### Modifying our edit and delete forms with the form package
+ title form
```php
{!! Form::model($post, ['method'=>'PATCH', 'route'=>['posts.update', $post->id]]) !!}
      {{csrf_field()}}
      {!! Form::label('title', 'Title:') !!}
      {!! Form::text('title', null, ['class'=>'form-control']) !!}

      {!! Form::submit('Update Post', ['class'=>'btn btn-info']) !!}
{{ Form::close() }}

{!! Form::open(['method'=>'DELETE', 'route'=>['posts.destroy', $post->id]]) !!}
      {{csrf_field()}}
      {!! Form::submit('Delete Post', ['class'=>'btn btn-danger']) !!}
{{ Form::close() }}
```

### Basic Validation
```php
#store function
$this->validate($request,[
            'title'=>'required|max:100'
        ]);
```

### Displaying Errors
+ change route
```php
   Route::group(['middleware'=>'web'], function(){
    Route::resource('/posts', PostsController::class);
});
```
+ display errors
```php
@if(count($errors) > 0)
        <div class="alert alert-danger">
            <ul>
                @foreach($errors->all() as $error)
                    <li>{{$error}}</li>
                @endforeach
            </ul>
        </div>
    @endif
```

### Advance Validation
+ make new request
```bash
$ php artisan make:request CreatePostRequest
```
+ edit request content
```php
public function rules()
    {
        return [
            'title'=>'required|max:100'
            //
        ];
    }
```
+ edit PostsController
```php
use App\Http\Requests\CreatePostRequest;

public function store(CreatePostRequest $request) {
      Post::create($request->all());

      return redirect('/posts');
}
```

### Creating Snippets with PhpStorm

### Resources
[Validation](https://laravel.com/docs/5.2/validation)
