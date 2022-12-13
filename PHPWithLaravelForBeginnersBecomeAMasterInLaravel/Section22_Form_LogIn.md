# Section 22: Form - Login

### Section Intro with Edwin Diaz

### New Laravel Installation
```bash
$ laravel new login
$ cd login
$ git init
$ git add .
$ git commit -m "initial commit"
$ git remote add origin https://github.com/edmundmagnojr-nabepero/login.git
$ git push -u origin master
```
### Database Connection and Migrations
```bash
$ php artisan migrate
```

### Creating the Login System under 1 Minute
```bash
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
```

### Login Creation Overview
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

### Retrieving Authenticated User Data

### Resources
[Authentication](https://laravel.com/docs/5.2/authentication)
