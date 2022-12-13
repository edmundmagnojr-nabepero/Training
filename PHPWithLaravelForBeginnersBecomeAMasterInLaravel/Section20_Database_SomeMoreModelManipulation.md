# Section 20: Database - Some More Model Manipulation

### Section Intro with Edwin Diaz

### Dates
+ Carbon
```php
use Carbon\Carbon
Route::get('/dates', function(){
  $date = new DateTime('+1 week');
  echo $date->format('m-d-y');
  echo '<br>';
  echo Carbon::now()->addDays(10)->diffForHumans();
  echo '<br>';
  echo Carbon::now()->subMonths(5)->diffForHumans();
});
```
### Accessors
+ route
```php
Route::get('/getname', function(){
        $user = User::find(1);
        echo $user->name;
    });
```
+ user model
```php
public function getNameAttribute($value){
        return strtoupper($value);
    }
```

### Mutators
+ route
```php
Route::get('/setname', function(){
        $user = User::find(1);
        $user->name = "edmund magno";
        $user->save();
    });
```
+ user model
```php
public function setNameAttribute($value){
        $this->attributes['name'] = strtoupper($value);
    }
```

### Query Scope
+ post model
```php
public static function scopeLatest($query){
        return $query->orderBy('id', 'asc')->get();
    }
```
+ user model
```index function
$posts = Post::latest()->get();
```

### Resources
[Eloquent: Mutators](https://laravel.com/docs/5.2/eloquent-mutators)

[Query Scopes](https://laravel.com/docs/5.2/eloquent#query-scopes)
