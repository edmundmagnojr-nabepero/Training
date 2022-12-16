# Section 32: New Application - Laravel 7 - Creating a User Profile

### Creating a User Profile part 1
+ copy Nav Item - User Information and Logout modal from admin master
+ paste on admin-top-navbar-user-information.blade.php
+ display user name
```php
                  @if(Auth::check())
                  {{auth()->user()->name}}
                  @endif
```
+ logout button
```php
          <form action="/logout" method="post">
            @csrf
            <button class="btn btn-secondary" type="button" data-dismiss="modal">Cancel</button>
            <!-- <a class="btn btn-primary" href="login.html">Logout</a> -->
            <button class="btn btn-danger">Logout</button>
          </form>
```
+ create profile.blade.php
```php
<x-admin-master>
    @section('content')
        <h1>User Profile of {{$user->name}}</h1>
    @endsection
</x-admin-master>
```
+ add user profile show route
```php
Route::get('/admin/users/{user}/profile', [App\Http\Controllers\UserController::class, 'show'])->name('user.profile.show');  
```
+ make User Controller
```bash
$ php artisan make:controller UserController
```
+ add show function in User Controller
```php
public function show(User $user){
        return view('admin.users.profile', ['user'=>$user]);
    }
```

### Creating a User Profile part 2
+ change profile link
```php
                <a class="dropdown-item" href="{{route('user.profile.show', auth()->user())}}">
                  <i class="fas fa-user fa-sm fa-fw mr-2 text-gray-400"></i>
                  Profile
                </a>
```
+ add user form
```php
          <div class="row">
            <div class="col-sm-6">
                <form action="post" action="" enctype="multipart/form-data">
                    @csrf
                    <div class="mb-4">
                        <img height="40px" width="40px" class="img-profile rounded-circle" src="https://source.unsplash.com/QAB-WJcbgJk/60x60">
                    </div>
                    <div class="form-group">
                        <input type="file">
                    </div>
                    <div class="form-group">
                        <label for="name">Name</label>
                        <input  type="text"
                                name="name"
                                class="form-control" 
                                id="name"
                                value="{{$user->name}}">
                    </div>
                    <div class="form-group">
                        <label for="email">Email</label>
                        <input  type="email"
                                name="email"
                                class="form-control" 
                                id="email"
                                value="{{$user->email}}">
                    </div>
                    <div class="form-group">
                        <label for="password">Password</label>
                        <input  type="password"
                                name="password"
                                class="form-control" 
                                id="password">
                    </div>
                    <div class="form-group">
                        <label for="password-confirmation">Confirm Password</label>
                        <input  type="password"
                                name="password_confimation"
                                class="form-control" 
                                id="password-confirmation">
                    </div>
                    <button type="submit" class="btn btn-primary">Submit</button>
                </form>
            </div>
        </div>
```

### Adding a Username
+ add username in show profile
```php
<div class="form-group">
                        <label for="username">Username</label>
                        <input  type="text"
                                name="username"
                                class="form-control" 
                                id="username"
                                value="{{$user->username}}">
                    </div>
```
+ modify create users table, add username and avator column
```php
public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('username')->unique();
            $table->string('name');
            $table->text('avatar')->nullable();
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }
```
+ add username in registration
```php
<div class="row mb-3">
                            <label for="username" class="col-md-4 col-form-label text-md-end">{{ __('Username') }}</label>

                            <div class="col-md-6">
                                <input id="username" type="text" class="form-control @error('username') is-invalid @enderror" name="username" value="{{ old('username') }}" required autocomplete="username" autofocus>

                                @error('username')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>
```
+ add register link in home
```php
<li class="nav-item">
            <a class="nav-link" href="/register">Register</a>
          </li>
```
+ add username in validation and create function
```php
protected function validator(array $data)
    {
        return Validator::make($data, [
            'username' => ['required', 'string', 'max:255', 'unique:users', 'alpha_dash'],
            'name' => ['required', 'string', 'max:255'],
            'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
            'password' => ['required', 'string', 'min:8', 'confirmed'],
        ]);
    }
    protected function create(array $data)
    {
        return User::create([
            'username' => $data['username'],
            'name' => $data['name'],
            'email' => $data['email'],
            'password' => $data['password'],
        ]);
    }
```
+ add username in fillables of user model
```php
protected $fillable = [
        'username',
        'name',
        'email',
        'password',
    ];
```
+ refresh migrations
```bash
$ php artisan migrate:refresh
```

### Updating the Profile
