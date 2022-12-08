# Section 12: Laravel Fundamentals - Database - Tinker

### Section Intro with Edwin Diaz

### Creating data with Tinker
```bash
#enter tinker
$ php artisan tinker

#create new complete post
$post = App\Models\Post::create(['title'=>'PHP post from tinker', 'content'=>'PHP content from tinker']);

#create new post
$post = new App\Models\Post

#add post title
$post->title = "New Title from this object"

#add post content
$post->content = "I\'m coding and doing awesome"

#save post
$post->save()
```


### Finding record and using constraints
```bash
#find()
$post = App\Models\Post::find(4);

#where()
$post = App\Models\Post::where('id', 4)->first();

#whereId()
$post = App\Models\Post::whereId(4)->first();
```

### Updating and deleting with tinker
+ update
```bash
#declare a variable
$post = App\Models\Post::find(4);

#update variable title
$post->title = "Updated record with ID 4";

#update variable content
$post->content = "Updated record content with ID 4";

#save changes
$post->save();
```
+ delete
```bash
#soft delete
$post->delete();

#declare variable to be deleted
$post = App\Models\Post::onlyTrashed()

#force delete variable
$post->forceDelete()
```

### Playing around with relations in tinker
```bash
#declare user
$user = App\Models\User::find(1);

#show roles of user
$user->roles
```
