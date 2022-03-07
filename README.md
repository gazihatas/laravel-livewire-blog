<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400"></a></p>

# <font color="red"> Laravel 8 Livewire jetstream blog project  </font>

1. ## <font color="aqua"> Install Laravel  </font>
    - ```laravel new projectName```
2. ## <font color="aqua"> Install Jetstream </font>
    - ```composer require laravel/jetstream```
    - ```php artisan jetstream:install livewire```
    - ```npm install && npm run dev```
    - ```php artisan migrate```
    - ```php artisan vendor:publish --tag=jetstream-views```
    - ```php artisan migrate:fresh```
3. ## <font color="aqua"> Make Post model and migration </font>
    - ```php artisan make:model Post -m```
    - create_posts_table.php 
       ``` 
      Schema::create('posts', function (Blueprint $table) {
                $table->id();
                $table->unsignedBigInteger('user_id');
                $table->string('title');
                $table->string('slug')->unique();
                $table->text('body');
                $table->string('image');
                $table->boolean('active');
                $table->timestamps();
            });
        ```
    - ```php artisan migrate```
4. ## <font color="aqua"> Install role and permission </font>
  - ```
    composer require spatie/laravel-permission
    ```
  - ```
     php artisan optimize:clear
    ```
  - ```
     php artisan migrate
    ```
  - ```
    php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
    ```
  - ```
    php artisan migrate
    ```
  - App\Models\User.php
    ```
    use Illuminate\Contracts\Auth\MustVerifyEmail;
    use Illuminate\Database\Eloquent\Factories\HasFactory;
    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;
    use Laravel\Fortify\TwoFactorAuthenticatable;
    use Laravel\Jetstream\HasProfilePhoto;
    use Laravel\Sanctum\HasApiTokens;
    use Spatie\Permission\Traits\HasRoles; // write
    
    class User extends Authenticatable
    {
    use HasApiTokens;
    use HasFactory;
    use HasProfilePhoto;
    use Notifiable;
    use TwoFactorAuthenticatable;
    use HasRoles; // write
    }
    ```
  - Creating roles and permissions with Artisan Commands You can create a role or permission from the console with artisan commands.
    ```
       php artisan permission:create-role admin
       php artisan permission:create-role user
    ```
  - Package Middleware This package comes with RoleMiddleware, PermissionMiddleware and RoleOrPermissionMiddleware middleware. You can add them inside your app/Http/Kernel.php file.
    ```
     protected $routeMiddleware = [
        'role' => \Spatie\Permission\Middlewares\RoleMiddleware::class,
     ];
    ```
5. ## <font color="aqua"> Modify jetstream navbar </font>
    - routes/**web.php**
      ```
      Route::get('/', function () {
      // auth()->user()->assignRole('admin'); //added
      return view('welcome');
      });
      ```
    - **tailwind.config.js**
      ```
          const defaultTheme = require('tailwindcss/defaultTheme');
          module.exports = {
          darkMode: "class", // => !added
          content: [
           './vendor/laravel/framework/src/Illuminate/Pagination/resources/views/*.blade.php',
           './vendor/laravel/jetstream/**/*.blade.php',
           './storage/framework/views/*.php',
           './resources/views/**/*.blade.php',
          ],
          theme: {
            extend: {
               fontFamily: {
                   sans: ['Nunito', ...defaultTheme.fontFamily.sans],
               },
             },
          },
         plugins: [require('@tailwindcss/forms'), require('@tailwindcss/typography')],
         };
      ```
    - resources/views/layouts/**app.blade.php**
      ```
      <html lang="{{ str_replace('_', '-', app()->getLocale()) }}" class="dark">
      ```
    - resources/views/layouts/**guest.blade.php**
      ```
      <html lang="{{ str_replace('_', '-', app()->getLocale()) }}" class="dark">
      ```
    - resources/views/**navigation-menu.blade.php**
      ```
       <nav x-data="{ open: false }" class="bg-white dark:bg-gray-800 border-b border-gray-100">
        <x-jet-nav-link class="dark:text-gray-200 font-bold" href="{{ route('dashboard') }}" :active="request()->routeIs('dashboard')">
             {{ __('Dashboard') }}
        </x-jet-nav-link>     
      ```
6. ## <font color="aqua"> Create navbar component for guest </font>
    - **Create new folder**     => resources/views/**components**
    - [//]: # (Create new blade file => resources/views/components/navbar.blade.php)
    - <details>
      <summary>**Create new blade file** => resources/views/components/**navbar.blade.php**</summary>
    
      ```
         <div class="w-full text-gray-700 bg-white dark:text-gray-200 dark:bg-gray-800">
           <div x-data="{ open: false }" class="flex flex-col max-w-screen-xl px-4 mx-auto md:items-center md:justify-between md:flex-row md:px-6 lg:px-8">
            <div class="flex flex-row items-center justify-between p-4">
                <a href="#" class="text-lg font-semibold tracking-widest text-gray-900 uppercase rounded-lg dark:text-white focus:outline-none focus:shadow-outline">LION</a>
                <button class="rounded-lg md:hidden focus:outline-none focus:shadow-outline" @click="open = !open">
                   <svg fill="currentColor" viewBox="0 0 20 20" class="w-6 h-6">
                       <path x-show="!open" fill-rule="evenodd" d="M3 5a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 10a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM9 15a1 1 0 011-1h6a1 1 0 110 2h-6a1 1 0 01-1-1z" clip-rule="evenodd"></path>
                       <path x-show="open" fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd"></path>
                   </svg>
                </button>
            </div>
           <nav :class="{'flex': open, 'hidden': !open}" class="flex-col flex-grow hidden pb-4 md:pb-0 md:flex md:justify-end md:flex-row">
               <a class="px-4 py-2 mt-2 text-sm font-semibold text-gray-900 bg-gray-200 rounded-lg dark:bg-gray-700 dark:hover:bg-gray-600 dark:focus:bg-gray-600 dark:focus:text-white dark:hover:text-white dark:text-gray-200 md:mt-0 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">Blog</a>
               <a class="px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark:bg-transparent dark:hover:bg-gray-600 dark:focus:bg-gray-600 dark:focus:text-white dark:hover:text-white dark:text-gray-200 md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">Portfolio</a>
               <a class="px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark:bg-transparent dark:hover:bg-gray-600 dark:focus:bg-gray-600 dark:focus:text-white dark:hover:text-white dark:text-gray-200 md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">About</a>
               <a class="px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark:bg-transparent dark:hover:bg-gray-600 dark:focus:bg-gray-600 dark:focus:text-white dark:hover:text-white dark:text-gray-200 md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">Contact</a>
               @auth
                   <a class="px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark:bg-transparent dark:hover:bg-gray-600 dark:focus:bg-gray-600 dark:focus:text-white dark:hover:text-white dark:text-gray-200 md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="{{ route('dashboard') }}">Dashboard</a>
               @else
                   <a class="px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark:bg-transparent dark:hover:bg-gray-600 dark:focus:bg-gray-600 dark:focus:text-white dark:hover:text-white dark:text-gray-200 md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="{{ route('login') }}">Login</a>
               @endauth
           </nav>
         </div>
       </div>

      ```
      </details> 
    - [//]: # (navbar.blade.php added layouts/guest.blade.php)
    - <details>
      <summary>navbar.blade.php added layouts/guest.blade.php</summary>
    
      ```
      <body>
          <x-navbar></x-navbar> <!-- added -->
      </body>
      ```
      </details>
    - 
7. ## <font color="aqua"> Create livewire Posts component </font>
8. ## <font color="aqua"> Add table to display all posts </font>
9. ## <font color="aqua"> Add modal to create post and edit post </font>
10. ## <font color="aqua"> Create Post list component </font>
11. ## <font color="aqua"> Create livewire post show component </font>


---------------------------------------------------

# <font color="yellow" align="center">RESOURCES</font>

----------------------------------------------------

## [Laravel-Permission](https://spatie.be/docs/laravel-permission/v4/introduction)
**Associate users with roles and permissions**
<details>
    <summary><font color="#adff2f">Laravel-Permission</font></summary>

    ### Introduction
    **This package allows you to manage user permissions and roles in a database.**


Installing
1. Consult the Prerequisites page for important considerations regarding your User models!

2. This package publishes a config/permission.php file. If you already have a file by that name, you must rename or remove it.

3. You can install the package via composer:

```
composer require spatie/laravel-permission
```

4. Optional: The service provider will automatically get registered. Or you may manually add the service provider in your config/app.php file:

   ```
   'providers' => [
       // ...
       Spatie\Permission\PermissionServiceProvider::class,
       ];
   ```
5. You should publish the migration and the config/permission.php config file with:

```
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
```

6. NOTE: If you are using UUIDs, see the Advanced section of the docs on UUID steps, before you continue. It explains some changes you may want to make to the migrations and config file before continuing. It also mentions important considerations after extending this package's models for UUID capability.

7. Clear your config cache. This package requires access to the permission config. Generally it's bad practice to do config-caching in a development environment. If you've been caching configurations locally, clear your config cache with either of these commands:

   ```php artisan optimize:clear```<br>
   or<br>
   ```php artisan config:clear```

8. Run the migrations: After the config and migration have been published and configured, you can create the tables for this package by running:

```
php artisan migrate
```

9. Add the necessary trait to your User model: Consult the Basic Usage section of the docs for how to get started using the features of this package.

### DEFAULT CONFIG FILE CONTENTS
**You can view the default config file contents at:**

https://github.com/spatie/laravel-permission/blob/master/config/permission.php

---------------------------------------------------------------------------

## [Basic Usage](https://spatie.be/docs/laravel-permission/v4/basic-usage/basic-usage)
First, add the Spatie\Permission\Traits\HasRoles trait to your User model(s):

```
use Illuminate\Foundation\Auth\User as Authenticatable;
use Spatie\Permission\Traits\HasRoles;

class User extends Authenticatable
{
    use HasRoles;

    // ...
}
```

## [Using Artisan Commands](https://spatie.be/docs/laravel-permission/v4/basic-usage/artisan)
**Creating roles and permissions with Artisan Commands**
You can create a role or permission from the console with artisan commands.
```
   php artisan permission:create-role admin
   php artisan permission:create-role user
```

## [Using a Middleware](https://spatie.be/docs/laravel-permission/v4/basic-usage/middleware)
**Package Middleware**
This package comes with RoleMiddleware, PermissionMiddleware and RoleOrPermissionMiddleware middleware. You can add them inside your app/Http/Kernel.php file.
```
protected $routeMiddleware = [
    // ...
    'role' => \Spatie\Permission\Middlewares\RoleMiddleware::class,
    'permission' => \Spatie\Permission\Middlewares\PermissionMiddleware::class,
    'role_or_permission' => \Spatie\Permission\Middlewares\RoleOrPermissionMiddleware::class,
];
```

## [Using Permissions via Roles](https://spatie.be/docs/laravel-permission/v4/basic-usage/role-permissions)
Assigning Roles
A role can be assigned to any user:
```
$user->assignRole('writer');

// You can also assign multiple roles at once
$user->assignRole('writer', 'admin');
// or as an array
$user->assignRole(['writer', 'admin']);
```
A role can be removed from a user:
```
$user->removeRole('writer');
```
Roles can also be synced:
```
        // All current roles will be removed from the user and replaced by the array given
        $user->syncRoles(['writer', 'admin']);
```

</details>

-------------------------------------------

## [Alphine Toolbox](https://alpinetoolbox.com/)
  - [Examples](https://www.alpinetoolbox.com/examples/)
  - [responsive-navbar-with-dropdown | jan-heise](https://github.com/jan-heise/responsive-navbar-with-dropdown)
    - [Resource Code](https://github.com/jan-heise/responsive-navbar-with-dropdown/blob/master/public/navbar.html)






