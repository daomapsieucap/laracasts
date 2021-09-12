# Section 2 - The Basics

---

# 1. Route

- It's how Laravel do with your URL
- `routes/web.php` will handle the result from your URL
- Template files called blade template, they can be found in `resources/views`
- The same workflow with Magento or Joomla
- Route will load blade template

    ```php
    Route::get('/', function () {
        return view('welcome');
    });
    ```

# 2. CSS and Javascript

- Laravel load assets from `public` folder
- Include assets in blade templates in `resources/view` folder

# 3. Route Wildcard Contraints

- Use `->where` after Route to create contraints with regular expression
- There are some helper functions to define the contraints like `where`, `whereAlpha`, `whereAlphanumeric`...

# 4. Cache

- Laravel support the cache functions while coding which we can define the cache expired time

    ```php
    $post = cache()->remember( "post.{$slug}", now()->addMinute( 20 ), function () use ( $path ) {
        return file_get_contents( $path );
    } );
    ```

# 5. Models

- Model is defined in `app/Models` folder

# 6. YAML

- Using YAML format in Laravel by this module: [https://github.com/spatie/yaml-front-matter](https://github.com/spatie/yaml-front-matter) (install via Composer)
- YAML format should be something like this

    ```yaml
    ---
    title: first post
    slug: first-post
    date: 2021-08-06
    excerpt: lorem ipsum
    ---

    <p>first post</p>
    ```

- To use YAML in Laravel

    ```php
    $document = YamlFrontMatter::parseFile( $file );

    //access object data
    $document->title;
    $document->excerpt;
    $document->date;
    $document->body();
    $document->slug;
    ```

# 7. Collection

- Use `collect()` for Collection object
- Can convert from `array_map` into Collection like this

    ```php
    //using array_map
    $posts = array_map( function ( $file ) {
        $document = YamlFrontMatter::parseFile( $file );

        return new Post(
            $document->title,
            $document->excerpt,
            $document->date,
            $document->body(),
            $document->slug
        );
    }, $files );

    //using collection instead
    $posts = collect( $files )->map( function ( $file ) {
        $document = YamlFrontMatter::parseFile( $file );

        return new Post(
            $document->title,
            $document->excerpt,
            $document->date,
            $document->body(),
            $document->slug
        );
    } );
    ```

- Can use map multiple times like this

    ```php
    $posts = collect( File::files( resource_path( "posts" ) ) )
            ->map( function ( $file ) {
                return YamlFrontMatter::parseFile( $file );
            } )
            ->map( function ( $document ) {
                return new Post(
                    $document->title,
                    $document->excerpt,
                    $document->date,
                    $document->body(),
                    $document->slug
                );
            } );
    ```

- Collection can be sorted ascending or descending

    ```php
    $posts = collect( File::files( resource_path( "posts" ) ) )
            ->map( function ( $file ) {
                return YamlFrontMatter::parseFile( $file );
            } )
            ->map( function ( $document ) {
                return new Post(
                    $document->title,
                    $document->excerpt,
                    $document->date,
                    $document->body(),
                    $document->slug
                );
            } )
    				->sortByDesc( 'date' );
    ```

# 8. Psy Shell

- Start via `php artisan tinker` command line
- Show cache

    ```php
    //init cache in PHP
    return cache()->rememberForever( 'posts.all', function () {
        return collect( File::files( resource_path( "posts" ) ) )
            ->map( function ( $file ) {
                return YamlFrontMatter::parseFile( $file );
            } )
            ->map( function ( $document ) {
                return new Post(
                    $document->title,
                    $document->excerpt,
                    $document->date,
                    $document->body(),
                    $document->slug
                );
            } )
            ->sortByDesc( 'date' );
    } );
    ```

    ```bash
    cache('posts.all');

    cache()->get('posts.all');
    ```

- Clear cache

    ```bash
    cache->forget('posts.all');
    ```