# Section 5 - Integrate the design

---

# 1. Convert HTML into Blade

- Assets should be stored in `/public` folder
- Source path to images in blade template start from public. ***For example: when we have `images` folder in `public`, the path should be `/images/illustrator-1.png`.***
- Basic partials should be named to start with an underscore, eg `_post-header.blade.php`.

# 2. Blade Components & CSS Grids

- You may specify which attributes should be considered data variables using the `@props` directive at the top of your component's Blade template

    ```php
    @props(['post'])

    //then we can use
    {{$post->title}}
    ```

- Pass `$posts` collection into blade by

    ```php
    <x-posts-grid :**posts**="$posts"/>

    // Get $posts collection in other blade
    @props(['**posts**'])

    // Then use it
    <x-post-featured-card :post="**$posts**[0]"/>
    ```

- `diffForHumans`: Take a timestamp difference and convert it to a human format

    ```php
    //2021-08-30 20:00:00
    {{$post->created_at}}

    //5 days ago
    {{$post->created_at->diffForHumans()}}
    ```

- Skip specific item in collection by using `$post→skip()`, for example

    ```php
    //skip the first item
    $post→skip(1)
    ```

- Pass attribute from template to template by using `$attributes`

    ```php
    // Merge all class attribute passed from home.blade.template into post.blade.template
    {{$attributes->merge(['class'=>'transition-colors duration-300 hover:bg-gray-100 border border-black border-opacity-0 hover:border-opacity-5 rounded-xl'])}}
    ```

- When in a loop, Laravel has an automatic variable called $loop

# 3. Category Dropdown

- To check if is current category or not, use

    ```php
    $currentCategory->is($category)
    ```

- Use `request()` to get `$_REQUEST`

    ```php
    request()->is('categories/'.$category->slug)

    //Or use regex in request
    request()->is('*'.$category->slug)
    ```

- Name a route in route/web.php so we can use request() to check

    ```php
    Route::get('/', function(){
        return view('home', [
            'posts'      => Post::latest()->get(),
            'categories' => Category::all()
        ]);
    })**->name('home');**

    <x-dropdown-item href="/" :active="**request()->routeIs('home')**">All</x-dropdown-item>
    ```

-