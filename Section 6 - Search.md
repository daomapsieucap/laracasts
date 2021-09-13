# Section 6 - Search

---

- Use `request` to get request parameter
- The query will look something like this
  ```php
  $posts->where('title', 'like', '%' . request('search') . '%')
    ->orwhere('body', 'like', '%' . request('search') . '%'); 
   ```

- Create controller by
  ```bash
  php artisan make:controller PostController
   ```
  -> The new controller will be in `App/Http/Controller`
  
- Route to controller: Provide a full path to controller and the name of controller's action
  ```php
  Route::get('/', [PostController::class, 'index'])->name('home');
   ```
  
- Create scope in Eloquent by creating function with scope[YourScopeName], e.g: `scopeFilter`.
  --> This will turn `Post::newQuery()->scopeFilter` into `Post::newQuery()->filter()`
- `request('search')` returns string but `request(['search'])` or `request()->only('search')` will return array