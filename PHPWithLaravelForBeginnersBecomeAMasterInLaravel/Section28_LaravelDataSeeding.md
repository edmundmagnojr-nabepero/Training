# Section 28: New - Laravel Data Seeding

### Creating a Simple Seeder
```bash
$ php artisan make:seeder UsersTableSeeder
$ php artisan db:seed
```
```php
#in UsersTableSeeder
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Str;

public function run()
    {
        DB::table('users')->insert([
            'name'=>Str::random(10),
            'role_id'=>2,
            'is_active'=>1,
            'email'=>Str::random(10).'@codingfaculty.com',
            'password'=>bcrypt('secret')
        ]);
    }
    
#DatabaseSeeder
public function run()
    {
        $this->call(UsersTableSeeder::class);
    }
```
### Creating a More Advanced Seeder with Factories
```php
#DatabaseSeeder
use App\Models\User;

public function run(){
  User::factory()->count(10)->create();
}
public function run()
    {
        DB::statement('SET FOREIGN_KEY_CHECKS=0');
        DB::table('users')->truncate();
        DB::table('posts')->truncate();
        DB::table('categories')->truncate();
        DB::table('roles')->truncate();
        DB::table('photos')->truncate();
        //factory(App\User::class, 10)->create();
        User::factory()->count(10)->create()->each(function($user){
            $user->posts()->save(Post::factory()->make());
        });
        Role::factory()->count(3)->create();
        Category::factory()->count(5)->create();
        Photo::factory()->count(1)->create();
        //Comment::factory()->count(3)->create();
        //$this->call(UsersTableSeeder::class);
    }
```
### Let's Create Factories for All Our Database Tables
