# Section 4 - Database

---

# 1. Database connection

- `php artisan migrate`: create empty data in a database defined in **.env** file.
- Laravel use **.env** file to define the configuration variables.

# 2. Basic Migration

- When we run `php artisan migrate`, Laravel will run the code inside **database/migrations** folder.
- `php artisan migrate:rollback` : Revert the last "**batch**".

  For example the last migration is `**php artisan migrate**`, when we run `**php artisan migrate:rollback**`, it will revert the last "**batch**" by delete all the tables (excluded the `migrations` table).

- "**batch**" is defined in `migrations` table

  If we update the batch of **create_users_table** to 2 then we run `**php artisan migrate:rollback**`, it will only delete **create_users_table**. Then it will re-create **create_users_table** again if we run `php artisan migrate`.

- `php artisan migrate:fresh`: drop all the tables and re-run from the beginning

  Running this command while **APP_ENV** is production, there is an alert need to be confirmed before it starts.

# 3. Eloquent

- Laravel way to interact with database
- Create user via `php artisan tinker`
    - Create new user object by `$user = new User;`
    - Assign user object data to `$user` variable
    - Then run `$user->save()` to let Laravel create new user.
    - To find a user we can use `User::find($user_id)` or `User::findOrFail($user_id)`
    - `User::all()` to get all users. It's a Collection of all users.

# 4. Model

- `php artisan make`: force generating files
- `php artisan make:migration [db_table_name]`: Create table
    - Laravel is smart enough to find the action, table name automatically in your command
    - For example when we call `php artisan make:migration create_posts_table` —> Laravel will knows you need to **create** a **posts** table
- `php artisan make:model [your_model_name]`: create Eloquent Model

  **Model name should be a singular name of table name**

    - `php artisan make:model Post`: This command will create a model named Post and this Model is connected with the table posts in previous command
- In `php artisan tinker`
    - `App\Models\Post:count();` or `App\Models\Post:all();`
    - Run `use App\Models\Post;` then you can use `Post:count();` or `Post:all();` after that
    - `Post::fresh()` after `$post→save()` will give the fresh instance from the database.
- **Model has the specific rule for foreign key as a default value**

    ```php
    public function user(){ // so the foreign key here is "user_id"
        return $this->belongsTo(User::class);
    }

    public function foobar(){ // so the foreign key here is "foobar_id"
        return $this->belongsTo(User::class);
    }
    ```

# 5. Mass Assignment

- Mass assignment can be done via `artisan tinker` by assign an data array into object

    ```php
    Post::create(['title'=>'My Third Post', 'excerpt' => 'excerpt post', 'body'=>'Dignissim convallis aenean et tortor at risus viverra adipiscing. Aliquam faucibus purus in massa tempor nec feugiat nisl. Es
    t sit amet facilisis magna etiam tempor. Massa tincidunt dui ut ornare lectus sit amet est placerat. Facilisis magna etiam tempor orci eu lobortis. Nisl vel pretium lectus quam id leo. Facilisi morbi tempus
    iaculis urna id volutpat lacus. Morbi blandit cursus risus at ultrices mi.']);
    ```

- Fields can be mass assigned should be define in `$fillable` variable in `App\Models\Post`.

    ```php
    protected $fillable = ['title', 'excerpt', 'body'];
    ```

- The opposite of `$fillable` variable is `$guarded`

    ```php
    protected $guarded = ['id'];
    ```

- `update()` can received an array for mass assignment like `create()`

    ```php
    $post->update(['excerpt'=>'changed']);
    ```

# 6. Route Model Binding

- Binding a route key into an eloquent model.

    ```php
    Route::get('/post/{**post**}', function(Post **$post**){
        return view('post', ['post' => **$post**]);
    });
    ```

- Putting slug into route so URL should be post/slug instead of post

    ```php
    Route::get('/post/{post:slug}', function(Post $post){ // Post::where('slug', $post)->firstOrFail()
        return view('post', ['post' => $post]);
    });
    ```

- Model method`getRouteKeyName()`: returns the name of the key or attribute that you should find an object according to

    ```php
    public function getRouteKeyName(){
        return 'slug';
    }

    //Using this in model so in route we can use like normal
    Route::get('/post/{post}', function(Post $post){ // Post::where('slug', $post)->firstOrFail()
        return view('post', ['post' => $post]);
    });
    ```

# 7. Eloquent Relationship

- Create model

    ```markdown
    **php artisan make:migration create_categories_table
    php artisan make:model**

    // Shorthand of these commands
    **php artisan make:model Category -m**
    ```

- Laravel has some functions like `hasOne`, `hasMany`, `belongsTo`, `belongsToMany` to return relationship in Model

    ```php
    // In Model Post, post can belong to a category
    public function category(){
        return $this->belongsTo(Category::class);
    }

    //In Model Category, a category can have many posts
    public function posts(){
        return $this->hasMany(Post::class);
    }
    ```

- We can access category after define in Model by `$post→category`. Laravel will understand to return the category object automatically.
- Wrap with Eloquent relationship in `php artisan tinker`

    ```markdown
    **App\Models\Post::with('user')->first();**

    *// This will show post with User and Category also*
    **App\Models\Post::with('user', 'category')->first();**
    ```

# 8. Optimize the database queries

- Equal loading all posts with their data by

    ```php
    // Return post with category and author
    Route::get('/', function(){
        return view('home', [
            'posts' => Post::with('category','author')->get()
        ]);
    });

    // We can use array inside with like this
    Route::get('/', function(){
        return view('home', [
            'posts' => Post::with(['category','author'])->get()
        ]);
    });
    ```

- Always check Clockwork. It should be a todo item when working with Laravel.

# 9. Database seeding

- `php artisan db:seed`: Run DB seed defined in `database/seeders/DatabaseSeeder.php`
- `php artisan migrate:fresh —seed`: Drop and re-run table, do DB seed after that.
- Empty table before running DB seed

    ```php
    User::truncate();
    Category::truncate();
    ```

- We can define the specific data in DB seed

    ```php
    User::factory()->create([
        'name' => 'John Doe'
    ]);

    // By that way we can use this to create multiple posts belong to a specific user
    $user = User::factory()->create([
        'name' => 'John Doe'
    ]);

    Post::factory(5)->create([
        'user_id' => $user->id
    ]);
    ```

# 10. Factory

- `App\Models\User::factory([range_of_user])→create();` - Create dummy new user. Eg: `App\Models\User::factory(10)→create();` - create 10 random users
- `php artisan make:factory PostFactory` - Create new factory
- `php artisan make:model Comment -mf` - Create Model with Factory and do the migration also
- Use `php artisan migrate:fresh —seed` so we don't need to truncate the database in DB Seed.

# 11. Eager Load Relationships

- Use `$with` variable in Model for the every query.

    ```php
    protected $with = ['category', 'author'];
    ```

    ```php
    // This will return with category and author also because we defined it in Model
    App\Models\Post::take(2)->get();
    ```

- We can use `without` to exclude the eager loading relationships

    ```php
    App\Models\Post::without('category')->first();
    ```