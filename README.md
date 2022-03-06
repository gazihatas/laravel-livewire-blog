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
  - **Create new folder**     => resources/views/**components**
  - <details>
    <summary>**Create new blade file** => resources/views/components/**navbar.blade.php**</summary>
    
    ```
        <div class="w-full text-gray-700 bg-white dark-mode:text-gray-200 dark-mode:bg-gray-800">
    <div x-data="{ open: false }" class="flex flex-col max-w-screen-xl px-4 mx-auto md:items-center md:justify-between md:flex-row md:px-6 lg:px-8">
        <div class="flex flex-row items-center justify-between p-4">
            <a href="#" class="text-lg font-semibold tracking-widest text-gray-900 uppercase rounded-lg dark-mode:text-white focus:outline-none focus:shadow-outline">Flowtrail UI</a>
            <button class="rounded-lg md:hidden focus:outline-none focus:shadow-outline" @click="open = !open">
                <svg fill="currentColor" viewBox="0 0 20 20" class="w-6 h-6">
                    <path x-show="!open" fill-rule="evenodd" d="M3 5a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 10a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM9 15a1 1 0 011-1h6a1 1 0 110 2h-6a1 1 0 01-1-1z" clip-rule="evenodd"></path>
                    <path x-show="open" fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd"></path>
                </svg>
            </button>
        </div>
        <nav :class="{'flex': open, 'hidden': !open}" class="flex-col flex-grow hidden pb-4 md:pb-0 md:flex md:justify-end md:flex-row">
            <a class="px-4 py-2 mt-2 text-sm font-semibold text-gray-900 bg-gray-200 rounded-lg dark-mode:bg-gray-700 dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 md:mt-0 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">Blog</a>
            <a class="px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark-mode:bg-transparent dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">Portfolio</a>
            <a class="px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark-mode:bg-transparent dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">About</a>
            <a class="px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark-mode:bg-transparent dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">Contact</a>
            <div @click.away="open = false" class="relative" x-data="{ open: false }">
                <button @click="open = !open" class="flex flex-row items-center w-full px-4 py-2 mt-2 text-sm font-semibold text-left bg-transparent rounded-lg dark-mode:bg-transparent dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:focus:bg-gray-600 dark-mode:hover:bg-gray-600 md:w-auto md:inline md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline">
                    <span>Dropdown List</span>
                    <svg fill="currentColor" viewBox="0 0 20 20" :class="{'rotate-180': open, 'rotate-0': !open}" class="inline w-4 h-4 mt-1 ml-1 transition-transform duration-200 transform md:-mt-1"><path fill-rule="evenodd" d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z" clip-rule="evenodd"></path></svg>
                </button>
                <div x-show="open" x-transition:enter="transition ease-out duration-100" x-transition:enter-start="transform opacity-0 scale-95" x-transition:enter-end="transform opacity-100 scale-100" x-transition:leave="transition ease-in duration-75" x-transition:leave-start="transform opacity-100 scale-100" x-transition:leave-end="transform opacity-0 scale-95" class="absolute right-0 w-full mt-2 origin-top-right rounded-md shadow-lg md:w-48 z-30">
                    <div class="px-2 py-2 bg-white rounded-md shadow dark-mode:bg-gray-700">
                        <a class="block px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark-mode:bg-transparent dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 md:mt-0 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">Link #1</a>
                        <a class="block px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark-mode:bg-transparent dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 md:mt-0 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">Link #2</a>
                        <a class="block px-4 py-2 mt-2 text-sm font-semibold bg-transparent rounded-lg dark-mode:bg-transparent dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 md:mt-0 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">Link #3</a>
                    </div>
                </div>
            </div>
            <div @click.away="open = false" class="relative" x-data="{ open: false }">
                <button @click="open = !open" class="flex flex-row items-center w-full px-4 py-2 mt-2 text-sm font-semibold text-left bg-transparent rounded-lg dark-mode:bg-transparent dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:focus:bg-gray-600 dark-mode:hover:bg-gray-600 md:w-auto md:inline md:mt-0 md:ml-4 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline">
                    <span>Grid Dropdown</span>
                    <svg fill="currentColor" viewBox="0 0 20 20" :class="{'rotate-180': open, 'rotate-0': !open}" class="inline w-4 h-4 mt-1 ml-1 transition-transform duration-200 transform md:-mt-1"><path fill-rule="evenodd" d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z" clip-rule="evenodd"></path></svg>
                </button>
                <div x-show="open" x-transition:enter="transition ease-out duration-100" x-transition:enter-start="transform opacity-0 scale-95" x-transition:enter-end="transform opacity-100 scale-100" x-transition:leave="transition ease-in duration-75" x-transition:leave-start="transform opacity-100 scale-100" x-transition:leave-end="transform opacity-0 scale-95" class="absolute right-0 w-full md:max-w-screen-sm md:w-screen mt-2 origin-top-right z-30">
                    <div class="px-2 pt-2 pb-4 bg-white rounded-md shadow-lg dark-mode:bg-gray-700">
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <a class="flex flex row items-start rounded-lg bg-transparent p-2 dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">
                                <div class="bg-teal-500 text-white rounded-lg p-3">
                                    <svg fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" viewBox="0 0 24 24" class="md:h-6 md:w-6 h-4 w-4"><path d="M5 3v4M3 5h4M6 17v4m-2-2h4m5-16l2.286 6.857L21 12l-5.714 2.143L13 21l-2.286-6.857L5 12l5.714-2.143L13 3z"></path></svg>
                                </div>
                                <div class="ml-3">
                                    <p class="font-semibold">Appearance</p>
                                    <p class="text-sm">Easy customization</p>
                                </div>
                            </a>

                            <a class="flex flex row items-start rounded-lg bg-transparent p-2 dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">
                                <div class="bg-teal-500 text-white rounded-lg p-3">
                                    <svg fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" viewBox="0 0 24 24" class="md:h-6 md:w-6 h-4 w-4"><path d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"></path></svg>
                                </div>
                                <div class="ml-3">
                                    <p class="font-semibold">Comments</p>
                                    <p class="text-sm">Check your latest comments</p>
                                </div>
                            </a>

                            <a class="flex flex row items-start rounded-lg bg-transparent p-2 dark-mode:hover:bg-gray-600 dark-mode:focus:bg-gray-600 dark-mode:focus:text-white dark-mode:hover:text-white dark-mode:text-gray-200 hover:text-gray-900 focus:text-gray-900 hover:bg-gray-200 focus:bg-gray-200 focus:outline-none focus:shadow-outline" href="#">
                                <div class="bg-teal-500 text-white rounded-lg p-3">
                                    <svg fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" viewBox="0 0 24 24" class="md:h-6 md:w-6 h-4 w-4"><path d="M11 3.055A9.001 9.001 0 1020.945 13H11V3.055z"></path><path d="M20.488 9H15V3.512A9.025 9.025 0 0120.488 9z"></path></svg>
                                </div>
                                <div class="ml-3">
                                    <p class="font-semibold">Analytics</p>
                                    <p class="text-sm">Take a look at your statistics</p>
                                </div>
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        </nav>
    </div>
</div>

</details>

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


