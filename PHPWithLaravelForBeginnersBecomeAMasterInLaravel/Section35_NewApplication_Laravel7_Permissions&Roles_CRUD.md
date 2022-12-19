# Section 35: New Application - Laravel 7 - Permissions & Roles - CRUD

### CRUD - Create & Read - part 1
+ Authorization-link.blade.php
```php
<li class="nav-item">
        <a class="nav-link collapsed" href="#" data-toggle="collapse" data-target="#collapseAuthorization" aria-expanded="true" aria-controls="collapseTwo">
          <i class="fas fa-fw fa-cog"></i>
          <span>Authorizations</span>
        </a>
        <div id="collapseAuthorization" class="collapse" aria-labelledby="headingTwo" data-parent="#accordionSidebar">
          <div class="bg-white py-2 collapse-inner rounded">
            <h6 class="collapse-header">Authorizations</h6>
            <a class="collapse-item" href="{{route('roles.index')}}">Roles</a>
            <a class="collapse-item" href="{{route('permissions.index')}}">Permissions</a>
          </div>
        </div>
      </li>
```
+ roles/index.blade.php
```php
<x-admin-master>
    @section('content')
        Roles
    @endsection
</x-admin-master>
```
+ permissions/index.blade.php
```php
<x-admin-master>
    @section('content')
        Permissions
    @endsection
</x-admin-master>
```
+ admin-master.blade.php
```php
<x-admin.sidebar.authorization-link></x-admin.sidebar.authorization-link>
```
+ web/roles.php
```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/roles', [App\Http\Controllers\RoleController::class, 'index'])->name('roles.index');
```
+ web/permissions.php
```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/permissions', [App\Http\Controllers\PermissionController::class, 'index'])->name('permissions.index');
```
+ RoleController
```php
public function index(){
        return view('admin.roles.index');
    }
```
+ PermissionController
```php
public function index(){
        return view('admin.permissions.index');
    }
```
+ RouteServiceProvider
```php
Route::middleware('web')
                ->namespace($this->namespace)
                ->group(base_path('routes/web/roles.php'));
            
            Route::middleware('web')
                ->namespace($this->namespace)
                ->group(base_path('routes/web/permissions.php'));
```

### CRUD - Create & Read - part 2
+ add form and datatable in role
+ create store role route
```php
Route::post('/roles', [App\Http\Controllers\RoleController::class, 'store'])->name('roles.store');
```
+ add store method in RoleController
```php
public function store(){
        dd(request('name'));
    }
```

### CRUD - Create & Read - part 3 - validation
+ add id, name and slug column in datatable
+ error message
```php
<div class="form-group">
                        <label for="name">Name</label>
                        <input type="text" id="name" name="name" class="form-control @error('name') is-invalid @enderror" >
                        <div>
                          @error('name')
                            <span><strong>{{$message}}</strong></span>
                          @enderror
                        </div>
                    </div>
```
+ RoleController
```php
public function index(){
        return view('admin.roles.index', [
            'roles'=>Role::all()
        ]);
    }

    public function store(){
        request()->validate([
            'name'=>['required']
        ]);
        Role::create([
            'name'=>Str::ucfirst(request('name')),
            'slug'=>Str::of(Str::lower(request('name')))->slug('-')
        ]);
        return back();
    }
```

### CRUD - Deleting
+ add delete role button
```php
<td>
                          <form method="post" action="{{route('roles.destroy', $role->id)}}">
                            @csrf
                            @method('DELETE')
                            <button type="submit" class="btn btn-danger">DELETE</button>
                          </form>
                        </td>
```
+ delete role route
```php
Route::delete('/roles/{role}/destroy', [App\Http\Controllers\RoleController::class, 'destroy'])->name('roles.destroy');
```
+ destroy role method
```php
public function destroy(Role $role){
        $role->delete();
        session()->flash('role-deleted', 'Deleted Role '. $role->name);
        return back();
    }
```
+ delete message flash session
```php
@if(session()->has('role-deleted'))
            <div class="alert alert-danger">
              {{session('role-deleted')}}
            </div>
          @endif
```

### CRUD - Updating part 1
+ add edit link on role name
```php
<td><a href="{{route('roles.edit', $role->id)}}">{{$role->name}}</a></td>
```
+ add edit route
```php
Route::get('/roles/{role}/edit', [App\Http\Controllers\RoleController::class, 'edit'])->name('roles.edit');
```
+ add edit role method
```php
public function edit(Role $role){
        return view('admin.roles.edit', ['role'=>$role]);
    }
```
+ add edit role view
```php
<x-admin-master>
    @section('content')
        <h1>Edit Role: {{$role->name}}</h1>
    @endsection
</x-admin-master>
```

### CRUD - Updating part 2
+ role update form
```php
<x-admin-master>
    @section('content')
        <div class="col-sm-6">
            <h1>Edit Role: {{$role->name}}</h1>

        <form method="post" action="{{route('roles.update', $role->id)}}">
            @csrf
            @method("PUT")
            <div class="form-group">
                <label for="name">Name</label>
                <input type="text" class="form-control" name="name" id="name" value="{{$role->name}}">
            </div>
            <button class="btn btn-primary">Update</button>
        </form>
        </div>
    @endsection
</x-admin-master>
```
+ update role route
```php
Route::put('/roles/{role}/update', [App\Http\Controllers\RoleController::class, 'update'])->name('roles.update');
```
+ update role method
```php
public function update(Role $role){
        dd($role);
    }
```

### CRUD - Updating part 3
+ modify update role method
```php
public function update(Role $role){
        $role->name = Str::ucfirst(request('name'));
        $role->slug = Str::of(Str::lower(request('name')))->slug('-');

        if($role->isDirty('name')){
            session()->flash('role-updated', 'Role Updated to '. request('name'));
            $role->save();
        } else {
            session()->flash('role-updated', 'Nothing has been updated');
        }
        return back();
    }
```
+ update flash session
```php
@if(session()->has('role-updated'))
            <div class="alert alert-success">
                {{session('role-updated')}}
            </div>
        @endif
```

### Displaying role permissions
+ add role permissions data table
```php
<div class="row">
		<div class="col-lg-12">
			@if($permissions->isNotEmpty())
			<div class="card shadow mb-4">
				<div class="card-header py-3">
					<h6 class="m-0 font-weight-bold text-primary">Permissions</h6>
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
									<th>Delete</th>
								</tr>
							</thead>
							<tfoot>
								<tr>
									<th>Options</th>
									<th>Id</th>
									<th>Name</th>
									<th>Slug</th>
									<th>Delete</th>
								</tr>
							</tfoot>
							<tbody>
								@foreach($permissions as $permission)
								<tr>
									<td><input type="checkbox"
															@foreach($role->permissions as $role_permission)
															@if($permission->slug == $role_permission->slug)
															checked
															@endif
															@endforeach
									></td>
									<td>{{$permission->id}}</td>
									<td>{{$permission->name}}</td>
									<td>{{$permission->slug}}</td>
									<td><button class="btn btn-danger">DELETE</button></td>
								</tr>
								@endforeach
							</tbody>
						</table>
					</div>
				</div>
			</div>
			@endif
		</div>
	</div>
```
+ include role permissions
```php
public function edit(Role $role){
        return view('admin.roles.edit', [
            'role'=>$role,
            'permissions'=> Permission::all()
        ]);
    }
```

### Attaching and Detaching role permissions
+ add Edit Post and Update Users permissions
```bash
$ php artisan tinker
> App\Models\Permission::create(['name'=>'Edit Post', 'slug'=>'edit-post'])
> App\Models\Permission::create(['name'=>'Update Users', 'slug'=>'update-users'])
> exit
```
+ add attach and detach functionalities
```php
<td>
										<form method="post" action="{{route('role.permission.attach', $role)}}">
                                @method('PUT')
                                @csrf
                                <input type="hidden" name="permission" value="{{$permission->id}}">
                                <button type="submit" 
                                        class="btn btn-primary"
                                        @if($role->permissions->contains($permission))
                                        disabled
                                        @endif
                                        >ATTACH</button>
                            </form>
									</td>
									<td>
										<form method="post" action="{{route('role.permission.detach', $role)}}">
                                @method('PUT')
                                @csrf
                                <input type="hidden" name="permission" value="{{$permission->id}}">
                                <button type="submit" 
                                        class="btn btn-danger"
                                        @if(!$role->permissions->contains($permission))
                                        disabled
                                        @endif
                                        >DETACH</button>
                            </form>
									</td>
```
+ add Attach and Detach permission role routes
```php
Route::put('/roles/{role}/attach', [App\Http\Controllers\RoleController::class, 'attach_permission'])->name('role.permission.attach');
Route::put('/roles/{role}/detach', [App\Http\Controllers\RoleController::class, 'detach_permission'])->name('role.permission.detach');
```
+ add Attach and Detach permission methods
```php
public function attach_permission(Role $role){
        $role->permissions()->attach(request('permission'));
        return back();
    }

    public function detach_permission(Role $role){
        $role->permissions()->detach(request('permission'));
        return back();
    }
```

### Displaying and deleting permissions
+ permissions index view
```php
<x-admin-master>
    @section('content')
        <div class="row">
            <div class="col-sm-3">
        <form method="post" action="{{route('permissions.store')}}">
          @csrf
          <div class="form-group">
            <label for="name">Name</label>
            <input type="text" id="name" name="name" class="form-control @error('name') is-invalid @enderror" >
            <div>
              @error('name')
              <span><strong>{{$message}}</strong></span>
              @enderror
            </div>
          </div>
          <button class="btn btn-primary btn-block" type="submit">Create</button>
        </form>
      </div>
			<div class="col-sm-9">
        <div class="row">
          <div class="col-sm-12">
            <div class="card shadow mb-4">
              <div class="card-header py-3">
                <h6 class="m-0 font-weight-bold text-primary">Permissions</h6>
              </div>
              <div class="card-body">
                <div class="table-responsive">
                  <table class="table table-bordered" id="permissions-table" width="100%" cellspacing="0">
                    <thead>
                      <tr>
                        <th>Id</th>
                        <th>Name</th>
                        <th>Slug</th>
                        <th>Delete</th>
                      </tr>
                    </thead>
                    <tfoot>
                      <tr>
                        <th>Id</th>
                        <th>Name</th>
                        <th>Slug</th>
                        <th>Delete</th>
                      </tr>
                    </tfoot>
                    <tbody>
                      @foreach($permissions as $permission)
                      <tr>
                        <td>{{$permission->id}}</td>
                        <td><a href="{{route('permissions.edit', $permission->id)}}">{{$permission->name}}</a></td>
                        <td>{{$permission->slug}}</td>
                        <td>
                          <form method="post" action="{{route('permissions.destroy', $permission->id)}}">
                            @csrf
                            @method('DELETE')
                            <button type="submit" class="btn btn-danger">DELETE</button>
                          </form>
                        </td>
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
      </div>
        </div>
    @endsection
</x-admin-master>
```
+ index, store, edit and destroy permission methods
```php
public function index(){
        return view('admin.permissions.index', [
            'permissions'=>Permission::all()
        ]);
    }

    public function store(){
        request()->validate([
            'name'=>['required']
        ]);
        Permission::create([
            'name'=>Str::ucfirst(request('name')),
            'slug'=>Str::of(Str::lower(request('name')))->slug('-')
        ]);
        return back();
    }

    public function edit(Permission $permission){
        return view('admin.permissions.edit', ['permission'=>$permission]);
    }

    public function destroy(Permission $permission){
        $permission->delete();
        return back();
    }
```
+ index, store, edit, and destroy permission routes
```php
Route::get('/permissions', [App\Http\Controllers\PermissionController::class, 'index'])->name('permissions.index');
Route::post('/permissions', [App\Http\Controllers\PermissionController::class, 'store'])->name('permissions.store');
Route::delete('/permissions/{permission}/destroy', [App\Http\Controllers\PermissionController::class, 'destroy'])->name('permissions.destroy');
Route::get('/permissions/{permission}/edit', [App\Http\Controllers\PermissionController::class, 'edit'])->name('permissions.edit');
```

### Updating permissions
+ permission edit view
```php
<x-admin-master>
	@section('content')
	@if(session()->has('permission-updated'))
	<div class="alert alert-success">
		{{session('permission-updated')}}
	</div>
	@endif
	<div class="row">
		<div class="col-sm-6">
			<h1>Edit Permission: {{$permission->name}}</h1>
			<form method="post" action="{{route('permissions.update', $permission->id)}}">
				@csrf
				@method("PUT")
				<div class="form-group">
					<label for="name">Name</label>
					<input type="text" class="form-control" name="name" id="name" value="{{$permission->name}}">
				</div>
				<button class="btn btn-primary">Update</button>
			</form>
		</div>
	</div>
	@endsection
</x-admin-master>
```
+ update permission route
```php
Route::put('/permissions/{permission}/update', [App\Http\Controllers\PermissionController::class, 'update'])->name('permissions.update');
```
+ update permission method
```php
public function update(Permission $permission){
        $permission->name = Str::ucfirst(request('name'));
        $permission->slug = Str::of(Str::lower(request('name')))->slug('-');

        if($permission->isDirty('name')){
            session()->flash('permission-updated', 'Permission Updated to '. request('name'));
            $permission->save();
        } else {
            session()->flash('permission-updated', 'Nothing has been updated');
        }
        return back();
    }
```

### Protecting routes globally & locally
+ global protection
```php
Route::middleware(['web', 'auth', 'role:admin'])
                ->namespace($this->namespace)
                ->group(base_path('routes/web/roles.php'));
            
            Route::middleware(['web', 'auth', 'role:admin'])
                ->namespace($this->namespace)
                ->group(base_path('routes/web/permissions.php'));
```
+ local protection
```php
Route::middleware('auth')->group(function(){
    Route::get('/admin/posts/create', [App\Http\Controllers\PostController::class, 'create'])->name('post.create');
    Route::post('/admin/posts', [App\Http\Controllers\PostController::class, 'store'])->name('post.store');
    Route::get('/admin/posts', [App\Http\Controllers\PostController::class, 'index'])->name('post.index');
    Route::delete('/admin/posts/{post}/destroy', [App\Http\Controllers\PostController::class, 'destroy'])->name('post.destroy');
    Route::patch('/admin/posts/{post}/update', [App\Http\Controllers\PostController::class, 'update'])->name('post.update');
    Route::get('/admin/posts/{post}/edit', [App\Http\Controllers\PostController::class, 'edit'])->name('post.edit');
});
```
+ restrict not admin users from accessing users and authorization side bar
```php
@if(auth()->user()->userHasRole('Admin'))
        
        <x-admin.sidebar.admin-sidebar-users-link></x-admin.sidebar.admin-sidebar-users-link>
      
        <x-admin.sidebar.authorization-link></x-admin.sidebar.authorization-link>
        
      @endif
```
