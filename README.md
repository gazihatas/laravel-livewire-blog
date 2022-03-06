<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400"></a></p>

# Laravel 8 Livewire blog project

- Install Laravel
    - ```laravel new projectName```
- Install Jetstream 
    - ```composer require laravel/jetstream```
    - ```php artisan jetstream:install livewire```
    - ```npm install && npm run dev```
    - ```php artisan migrate```
    - ```php artisan vendor:publish --tag=jetstream-views```
    - ```php artisan migrate:fresh```
- Make Post model and migration
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
- Install role and permission
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
- Modify jetstream navbar
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
- Create navbar component for guest
- Create livewire Posts component
- Add table to display all posts
- Add modal to create post and edit post
- Create Post list component
- Create livewire post show component


---------------------------------------------------
## [Laravel-Permission](https://spatie.be/docs/laravel-permission/v4/introduction)
**Associate users with roles and permissions**

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
````
// All current roles will be removed from the user and replaced by the array given
$user->syncRoles(['writer', 'admin']);
```


