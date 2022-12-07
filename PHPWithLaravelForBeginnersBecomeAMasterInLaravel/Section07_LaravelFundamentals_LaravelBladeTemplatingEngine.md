# Section 7: Laravel Fundamentals - Laravel Blade Templating Engine

### Intro to chapter with Edwin

### Master layout setup
+ create what to yield
```php
@yield('content')
```
+ extend the file that has the yield
```php
@extends('layouts.app')
```
+ input the part to be yield
```php
@section('content')
```
+ stop the yield
```php
@stop
```

### Some more blade features
+ with variables sample
```php
public function contact(){
        $people = ['Edmund', 'Jose', 'James', 'Peter', 'Maria'];
        return view('contact', compact('people'));
    }
```
+ display people array
```php
@if (count($people))
	@foreach($people as $person)
		<li>{{$person}}</li>
	@endforeach
@endif
```
### Resources
[Blade Templates](https://laravel.com/docs/5.2/blade)
