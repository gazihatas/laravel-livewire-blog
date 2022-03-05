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
       ``` Schema::create('posts', function (Blueprint $table) {
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
- Modify jetstream navbar
- Create navbar component for guest
- Create livewire Posts component
- Add table to display all posts
- Add modal to create post and edit post
- Create Post list component
- Create livewire post show component
