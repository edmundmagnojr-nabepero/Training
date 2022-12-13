# Section 24: Laravel Sessions

### Section Intro with Edwin Diaz

### Setting and Reading Sessions
```php
public function index(Request $request){
  $request->session()->put(['edwin'=>'master instructor']);
  //global sessions
  //session(['peter'=>'student']);
  echo $request->session()->get('edwin');
  //return $request->session()->all();
  //return view('home');
}
```
### Global Session Function Deleting
```php
#delete one session
$request->session()->forget('edwin');

#delete all sessions
$request->session()->flush();
```

### Flashing Data
```php
$request->session()->flash('message', 'Post has been created');
$request->session()->reflash();
$request->session()->keep('message');
return $request->session()->get('message');
```

### Resources
[Session](https://laravel.com/docs/5.2/session)
