# Section 9: Laravel Fundamentals - Raw SQL Queries

### Section Intro to chapter with Edwin

### Inserting Data
```php
Route::get('/insert', function(){
    DB::insert('insert into posts(title, content) values(?, ?)',['PHP with Laravel', 'Laravel is the best thing that happened to PHP.']);
});
```

### Reading Data
```php
Route::get('/read', function(){
    $results = DB::select('select * from posts where id = ?', [1]);
    foreach($results as $post){
        return $post->title;
    }
});
```

### Updating Data
```php
Route::get('/update', function(){
    $updated = DB::update('update posts set title = "Updated Title" where id = ?', [1]);

    return $updated;
});
```

### Deleting Data
```php
Route::get('/delete', function(){
    $deleted = DB::delete('delete from posts where id = ?', [1]);

    return $deleted;
});
```

### Resources
[Database: Getting Started](https://laravel.com/docs/5.2/database)
