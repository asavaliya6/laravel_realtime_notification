## Reverb Real-time WebSocket using Event Broadcasting

- Laravel Reverb brings blazing-fast and scalable real-time WebSocket communication directly to Laravel application, and provides seamless integration with Laravel existing suite of event broadcasting tools.

1) Create authentication (auth) scaffolding using Laravel UI.
2) Set up two types of users: a super admin and a normal user. We will identify them using an "is_admin" column in the users table.
3) Create a posts table with columns for the title and body.
4) Allow users to create posts with title and body.
5) Once a post is created, the admin will get a realtime notification using Reverb. we will create PostCreate Event for send realtime notification.

- Install Laravel ```composer create-project laravel/laravel example-app```

- create an auth scaffold command to generate login, register, and dashboard functionalities :

```
composer require laravel/ui 
php artisan ui bootstrap --auth 
npm install
npm run build
```

- create posts and add is_admin column to users table :

```
php artisan make:migration add_is_admin_column_table
php artisan make:migration create_posts_table
php artisan migrate
```

- Create and Update Models : ```php artisan make:model Post``` 
- => app/Models/Post.php 
- => app/Models/User.php

- Configure laravel broadcast and Reverb as driver.and by default laravel has not enabled broadcasting,so need to run the command to enable it.

```
php artisan install:broadcasting
```

- laravel will ask to install Reverb and you need to select "Yes". then it will automatically installed it and need to run the following commands to install Reverb. it's option if already selected yes then.

```
composer require laravel/reverb
php artisan reverb:install
```

- install laravel echo server

```
npm install --save-dev laravel-echo
```

- Change code in => resources/js/echo.js
- set the BROADCAST_CONNECTION environment variable to Reverb in application's:

```
BROADCAST_CONNECTION=reverb

REVERB_APP_ID=256980
REVERB_APP_KEY=f4l2tmwqf6eg0f6jz0mw
REVERB_APP_SECRET=zioqeto9xrytlnlg7sj6
REVERB_HOST="localhost"
REVERB_PORT=8080
REVERB_SCHEME=http

VITE_REVERB_APP_KEY="${REVERB_APP_KEY}"
VITE_REVERB_HOST="${REVERB_HOST}"
VITE_REVERB_PORT="${REVERB_PORT}"
VITE_REVERB_SCHEME="${REVERB_SCHEME}"

```

- again build JS: ```npm run build```
- Create PostCreate Event ```php artisan make:event PostCreate``` and changes =>app/Events/PostCreate.php
- Create Routes => routes/web.php
- Create Controller => app/Http/Controllers/PostController.php
- Create and Update Blade Files => resources/views/layouts/app.blade.php resources/views/posts.blade.php
- Create Admin User ```php artisan make:seeder CreateAdminUser```
- Update CreateAdminUser seeder ```database/seeders/CreateAdminUser.php```
- Run seeder ```php artisan db:seed --class=CreateAdminUser```
- Run App ```php artisan serve```
- Also need to start reverb server ```php artisan reverb:start``` then run ```http://localhost:8000/```
- That have one admin user and register new normal user from registration form.create post normal user then Admin user will get the notification.