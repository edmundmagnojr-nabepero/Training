# Section 33: New Application - Laravel 7 - Admin Users

### Displaying all users part 1
+ create admin-sidebar-users-link component
```php
<li class="nav-item">
        <a class="nav-link collapsed" href="#" data-toggle="collapse" data-target="#collapseUsers" aria-expanded="true" aria-controls="collapseTwo">
          <i class="fas fa-fw fa-cog"></i>
          <span>Users</span>
        </a>
        <div id="collapseUsers" class="collapse" aria-labelledby="headingTwo" data-parent="#accordionSidebar">
          <div class="bg-white py-2 collapse-inner rounded">
            <h6 class="collapse-header">Users</h6>
            <a class="collapse-item" href="">Create a Post</a>
            <a class="collapse-item" href="{{route('users.index')}}">View All Users</a>
          </div>
        </div>
      </li>
```
+ create admin users route
```php
Route::get('/admin/users', [App\Http\Controllers\UserController::class, 'index'])->name('users.index');
```
+ insert under posts collapse menu in admin-master
```php
<x-admin-sidebar-users-link></x-admin-sidebar-users-link>
```
+ create users.index
```php
<x-admin-master>
    @section('content')
        <h1>Users</h1>
    @endsection
</x-admin-master>
```
+ create index function in User Controller
```php
public function index(){
        $users = User::all();
        return view('admin.users.index', ['users'=>$users]);
    }
```
### Displaying all users part 2
+ add datatable with id users-table
```php
<div class="card shadow mb-4">
            <div class="card-header py-3">
              <h6 class="m-0 font-weight-bold text-primary">DataTables Example</h6>
            </div>
            <div class="card-body">
              <div class="table-responsive">
                <table class="table table-bordered" id="users-table" width="100%" cellspacing="0">
                  <thead>
                    <tr>
                      <th>Id</th>
                      <th>Username</th>
                      <th>Avatar</th>
                      <th>Name</th>
                      <th>Registered Date</th>
                      <th>Updated Profile Date</th>
                    </tr>
                  </thead>
                  <tfoot>
                    <tr>
                      <th>Id</th>
                      <th>Username</th>
                      <th>Avatar</th>
                      <th>Name</th>
                      <th>Registered Date</th>
                      <th>Updated Profile Date</th>
                    </tr>
                  </tfoot>
                  <tbody>
                    @foreach($users as $user)
                    <tr>
                        <td>{{$user->id}}</td>
                        <td>{{$user->username}}</td>
                        <td>
                            <img height="50px" src="{{$user->avatar}}" alt="">
                        </td>
                        <td>{{$user->name}}</td>
                        <td>{{$user->created_at->diffForHumans()}}</td>
                        <td>{{$user->updated_at->diffForHumans()}}</td>
                    </tr>
                    @endforeach
                  </tbody>
                </table>
              </div>
            </div>
          </div>
         <div class="d-flex">
          <div class="mx-auto">
          </div>
         </div> 
    
```
+ change user profile icon
```php
<img height="60px" class="img-profile rounded-circle" src="{{auth()->user()->avatar}}">
```
+ add in userfactory definition
```php
'username' => $this->faker->userName,
```
+ change into 100 new entries
```php
public function run()
    {
        \App\Models\User::factory(100)->create()->each(function($user){
            $user->posts()->save(\App\Models\Post::factory()->make());
        });
    }
```
+ add users-table in datatables.js
```php
$(document).ready(function() {
  $('#dataTable').DataTable();

  $("#users-table").DataTable();
});
```
+ have 100 new entries
```php
$ php artisan db:seed
```
### Deleting Users
+ add session flash and delete button
```php
@if(session('user-deleted'))
        <div class="alert alert-danger">{{session('user-deleted')}}</div>
        @endif
<table class="table table-bordered" id="users-table" width="100%" cellspacing="0">
                  <thead>
                    <tr>
                      <th>Delete</th>
                    </tr>
                  </thead>
                  <tfoot>
                    <tr>
                      <th>Delete</th>
                    </tr>
                  </tfoot>
                  <tbody>
                    @foreach($users as $user)
                    <tr>
                        <td>
                          <form method="post" action="{{route('user.destroy', $user->id)}}">
                            @csrf
                            @method('DELETE')
                            <button class="btn btn-danger">DELETE</button>
                          </form>
                        </td>
                    </tr>
                    @endforeach
                  </tbody>
                </table>
```
+ add destroy function in user controller
```php
public function destroy(User $user){
        $user->delete();
        session()->flash('user-deleted', 'User has been deleted');
        return back();
    }
```
+ add delete user route
```php
Route::delete('/admin/users/{user}/destroy', [App\Http\Controllers\UserController::class, 'destroy'])->name('user.destroy');
```
### Creating some roles again
+ tinker
```bash
#run tinker
$ php artisan tinker
# find user 1
$user = App\Models\User::find(1)
# create admin role
$admin = App\Models\Role::create(['name'=>'Admin', 'slug'=>'admin'])
#attach admin role to user 1
$user->roles()->attach($admin)
#create view dashboard permission
$view_dashboard = App\Models\Permission::create(['name'=>'View Dashboard', 'slug'=>'view-dashboard'])
#attach view dashboard permission to admin role
$admin->permissions()->attach($view_dashboard)
#check if admin has users
$admin->users
#check if admin has permissions
$admin->permissions
#exit tinker
exit
```
### Creating and using Middlewares for Route Authorization
+ restrict users datatable to admin only
```php
@if(auth()->user()->userHasRole('Admin'))
        
        <x-admin-sidebar-users-link></x-admin-sidebar-users-link>

      @endif
```
+ add role middleware route
```php
Route::middleware('role:ADMIN')->group(function(){
    Route::get('/admin/users', [App\Http\Controllers\UserController::class, 'index'])->name('users.index');
    
});
```
+ change text to lower case
```php
public function userHasRole($role_name){
        foreach($this->roles as $role){
            if(Str::lower($role_name) == Str::lower($role->name)){
                return true;
            }
        }
    }
```
+ condition to view users link
```php
public function handle(Request $request, Closure $next, $role)
    {
        if(!$request->user()->userHasRole($role)){
            abort(403, 'You are not authorized');
        }
        return $next($request);
    }
```
+ register role in kernel
```php
'role' => \App\Http\Middleware\RoleMiddleware::class,
```+ create role middleware
```php
$ php artisan make:middleware RoleMiddleware
```
### A little Recap
+ 
```php

```
### Admin and Model Owner Same Access
+ 
```php

```
### Routes for large applications
+ 
```php

```
### Component nesting
+ 
```php

```
