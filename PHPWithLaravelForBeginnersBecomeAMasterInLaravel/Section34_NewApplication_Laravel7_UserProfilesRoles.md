# Section 34: New Application - Laravel 7 - User Profile Roles

### Displaying roles in User Profile
+ add roles datatables
```php
<div class="row">
            <div class="col-sm-12">
                <div class="card shadow mb-4">
            <div class="card-header py-3">
              <h6 class="m-0 font-weight-bold text-primary">Roles</h6>
            </div>
            <div class="card-body">
              <div class="table-responsive">
                <table class="table table-bordered" id="roles-table" width="100%" cellspacing="0">
                  <thead>
                    <tr>
                        <th>Options</th>
                        <th>Id</th>
                        <th>Name</th>
                        <th>Slug</th>
                        <th>Attach</th>
                        <th>Detach</th>
                    </tr>
                  </thead>
                  <tfoot>
                    <tr>
                        <th>Options</th>
                        <th>Id</th>
                        <th>Name</th>
                        <th>Slug</th>
                        <th>Attach</th>
                        <th>Detach</th>
                    </tr>
                  </tfoot>
                  <tbody>
                    @foreach($roles as $role)
                    <tr>
                        <td><input type="checkbox"></td>
                        <td>{{$role->id}}</td>
                        <td>{{$role->name}}</td>
                        <td>{{$role->slug}}</td>
                        <td> <button class="btn btn-primary">ATTACH</button> </td>
                        <td> <button class="btn btn-primary">DETACH</button> </td>
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
    
            </div>
        </div>
```
+ modify show function in UserController
```php
public function show(User $user){
        return view('admin.users.profile', [
            'user'=>$user,
            'roles'=>Role::all()
        ]);
    }
```

### Detecting roles for the user
+ create additional roles
```bash
$ php artisan tinker
> App\Models\Role::create(['name'=>'Manager','slug'=>'manager'])
> App\Models\Role::create(['name'=>'Author','slug'=>'author'])
> App\Models\Role::create(['name'=>'Subscriber','slug'=>'subscriber'])
> exit
```
+ check if user has role/s
```php
<td><input type="checkbox"
                          @foreach($user->roles as $user_role)
                            @if($user_role->slug == $role->slug)
                              checked
                            @endif
                          @endforeach
                        ></td>
```

### Attaching roles part 1
+ add route to attach button
```php
<td>
                            <form method="post"action="{{route('user.role.attach', $role->id)}}">
                                @method('PUT')
                                @csrf
                                <button class="btn btn-primary">ATTACH</button>
                            </form>
                        </td>
```
+ create new route for attach button
```php
Route::put('/admin/users/{role}/attach', [App\Http\Controllers\UserController::class, 'attach'])->name('user.role.attach');
```
+ attach function in usercontroller
```php
public function attach(Role $role){
        dd($role);
    }
```

### Attaching roles part 2
+ profile.php
```php
<td>
                            <form method="post" action="{{route('user.role.attach', $user)}}">
                                @method('PUT')
                                @csrf
                                <input type="hidden" name="role" value="{{$role->id}}">
                                <button type="submit" 
                                        class="btn btn-primary"
                                        @if($user->roles->contains($role))
                                        disabled
                                        @endif
                                        >ATTACH</button>
                            </form>
                        </td>
                        <td> 
                            <form method="post" action="{{route('user.role.detach', $user)}}">
                                @method('PUT')
                                @csrf
                                <input type="hidden" name="role" value="{{$role->id}}">
                                <button type="submit" 
                                        class="btn btn-danger"
                                        @if(!$user->roles->contains($role))
                                        disabled
                                        @endif
                                        >DETACH</button>
                            </form>    
                        </td>
```
+usercontroller
```php
public function attach(User $user){
        $user->roles()->attach(request('role'));
        return back();
    }

    public function detach(User $user){
        $user->roles()->detach(request('role'));
        return back();
    }
```
+ create new route for detach button
```php
Route::put('/admin/users/{user}/detach', [App\Http\Controllers\UserController::class, 'detach'])->name('user.role.detach');
```
