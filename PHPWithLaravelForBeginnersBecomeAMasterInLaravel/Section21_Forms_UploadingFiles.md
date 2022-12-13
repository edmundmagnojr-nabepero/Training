# Section 21: Forms - Uploading Files

### Section Intro with Edwin Diaz

### Update - Important - About - Uploading
+ regular upload file form
```html
<form action="/uploadfile" method="post" enctype="multipart/form-data">
    @csrf
    <div class="form-group">
        <input type="file" class="form-control-file" name="fileToUpload" id="exampleInputFile">
    </div>
    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```
### Modifying our create view for file input
```php
<div class='form-group'>
  {!! Form::file('file', ['class'=>'form-control']) !!}
</div>
```
+ user model
```php
public function getNameAttribute($value){
  return strtoupper($value);
}
```

### Retrieving file data
```php
$file = $request->file('file');
echo '<br>';
echo $file->getClientOriginalName();
```
+ user model
```php
public function setNameAttribute($value){
  $this->attributes['name'] = strtoupper($value);
}
```

### Persisting file data into the database
+ store function
```php
$input = $request->all();
if($file = $request->file('file')){
  $name = $file->getClientOriginalName();
  $file->move('images', $name);
  $input['path'] = $name;
}
Post::create($input);
#att to post model
protected $fillable = ['title', 'content', 'path'];
```
+ user model
```index function
$posts = Post::latest()->get();
```

### Displaying images and using accessors to make it easy
+ index
```php
<div class="image-container">
  <img height="100" src="{{$post->path}}" alt="">
</div>
```
+ post model
```php
public function getPathAttribute($value){
  return $this->directory . $value;
}
```

### Resources
[Files](https://laravel.com/docs/5.2/requests#files)
