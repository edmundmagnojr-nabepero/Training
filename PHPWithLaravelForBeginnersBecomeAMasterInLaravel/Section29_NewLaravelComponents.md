# Section 29: New - Laravel Components

### Creating components part 1
```bash
php artisan db:seed
composer require laravel/ui
php artisan ui bootstrap --auth
npm install resolve-url-loader@^5.0.0 --save-dev --legacy-peer-deps
npm install && npm run dev
```

### Creating components part 2
```php
#welcome.blade.php
@section('sidebar')
        <!-- Search Widget -->
        <x-home-search> </x-home-search>
 
        <!-- Categories Widget -->
        <x-categories> </x-categories>
 
        <!-- Side Widget -->
        <x-side-wiget> </x-side-wiget>
    @endsection
```

### Creating components part 3
```php
#change to
<!-- Categories Widget -->
<x-categories :users="$users"> </x-categories>

#categories.blade.php
<div class="col-lg-6">
  <ul class="list-unstyled mb-0">
    @foreach($users as $user)
    <li>
      <a href="#">{{$user->name}}</a>
    </li>
    @endforeach
  </ul>
</div>
```
