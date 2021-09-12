# Section 3 - Blade

---

# 1. Basic

- Blade will be compiled to PHP file to execute

    ```php
    {{ $post->title }}

    //this code will be compiled to
    <?= echo $post->title; ?>
    ```

- Compiled blade files will be stored at **`storage/framework/views`** folder
- Unescaped data:

  By default `{{ }}` staements are automatically sent through PHP `htmlspecialchars` function to prevent XSS attacks.

    ```php
    //this will show escaped data by default
    Hello, {{ $name }}.

    //show unescaped data by using this structure
    Hello, {!! $name !!}.
    ```

- Blade directives: Don't need `<?php` or `?>` to use, it's shorter than the PHP `foreach`

    ```markdown
    //default php
    **<?php
    foreach($posts as $post){
    }
    ?>**

    //blade way
    **@foreach ($posts as $post)
    @endforeach**

    //shorthand for dd
    **@dd();**
    ```

    - Laravel has a `$loop` variable in side loop to get more information like `first, last, odd, even`...

# 2. Layout

## 2.1. Section

- `@yield`: Define a layout
- `@extends`: Extends a layout
- `@section` and `@endsection`: Define a section

```markdown
//build a layout
@yield('**content**')

//define content for this specific section
@extends ('layout')

@section('**content**')
    @foreach ($posts as $post)
        <article class="{{$loop->even ? 'mb-6' : ''}}">
            <h3>
                <a href="/post/{{$post->slug}}">
                    {{ $post->title }}
                </a>
            </h3>
            <div>{{ $post->excerpt }}</div>
        </article>
    @endforeach
@endsection
```

## 2.2. Component (recommended)

- `resources/views/components` folder
- Using `x-layout` and `x-slot`

    ```markdown
    //Show component
    <main>
        {{$**content**}}
    </main>

    //x-layout
    <x-layout **content**="Hello">
    </x-layout>

    //x-slot
    <x-layout>
        <x-slot name="**content**">
            Hello again
        </x-slot>
    </x-layout>
    ```

- `$slot`: Special variable name supported by Laravel used in `components`

    ```php
    //in components
    {{$slot}}

    //blade
    <x-layout>
        @foreach ($posts as $post)
            <article class="{{$loop->even ? 'mb-6' : ''}}">
                <h3>
                    <a href="/post/{{$post->slug}}">
                        {{ $post->title }}
                    </a>
                </h3>
                <div>{{ $post->excerpt }}</div>
            </article>
        @endforeach
    </x-layout>
    ```

- Component is like we built module in WordPress, for example we can build component button and use it globally.