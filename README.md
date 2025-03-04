## Real-time Notifications using Event Broadcasting Reverb 

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


## Real-Time Notifications using Echo Socket.IO and Redis

- Use a phpredis driver to send real-time notifications using the echo server with socket.io.

<h3>Socket.IO</h3>

- Socket.IO is a JavaScript library that enables real-time, bidirectional communication between web clients and servers.

<h3>Redis Server</h3>

- It supports various data structures like strings, lists, sets, and hashes. Redis is known for its speed, enabling quick data access and real-time applications. It's often used to enhance web performance by storing frequently accessed data in memory, reducing the need to access slower disk storage.

1) Create authentication (auth) scaffolding using Laravel UI.
2) Set up two types of users: a super admin and a normal user. will identify them using an "is_admin" column in the users table.
3) Create a posts table with columns for the title and body.
4) Allow users to create posts with title and body.
5) Once a post is created, the admin will get a realtime notification using socket.io.create PostCreate Event for send realtime notification.
6) setup echo server with socket.io
7) install redis server in system. then install predis/predis composer package to system.
8) setup server.js file to start 6001 port.

<h3>Install Redis Server</h3>

- Install redis server to the system: ```sudo apt-get update``` ```sudo apt-get install redis-server```
- Also install php-redis to system: ```sudo apt-get install php-redis```
- Start the redis server to system: ```sudo systemctl start redis```
- Check redis server status: ```sudo systemctl status redis```

<h3>Setup Echo Server with SocketIO</h3>

- First by default laravel has not enabled broadcasting, so need to run the command to enable it: ```php artisan install:broadcasting```

- Install predis/predis to use driver as redis: ```composer require predis/predis```

- Install laravel echo server and update code :

```
import Echo from 'laravel-echo';
import io from 'socket.io-client';

window.io = io;

window.Echo = new Echo({
    broadcaster: 'socket.io',
    host: window.location.hostname + ':6001'
});
```

- Set the BROADCAST_CONNECTION, REDIS_CLIENT environment variable to redis in application's :

```
BROADCAST_CONNECTION=redis

REDIS_CLIENT=phpredis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

- Again build JS:

```
npm run build
```

- Check configuration => config/database.php
- Add driver to the broadcasting.php file => config/database.php

<h3>Setup Node JS for 6001 Port</h3>

- Enable 6001 port for socket io channel ```npm install --save ioredis``` ```npm install --save socket.io```
- Create server.js file in root folder
- Run the node js for 6001 port ```node server.js``` 

## Real-Time Notifications using Pusher 

- Send real-time notifications with Pusher. use a pusher driver to send real-time notifications using the echo server.

1) Create authentication (auth) scaffolding using Laravel UI.
2) Set up two types of users: a super admin and a normal user. We will identify them using an "is_admin" column in the users table.
3) Create a posts table with columns for the title and body.
4) Allow users to create posts with title and body.
5) Once a post is created, the admin will get a realtime notification using pusher. we will create PostCreate Event for send realtime notification.

<h3>Create Pusher App</h3>

1) Create Account on Pusher Platform.
2) Next, click on Get Started button.
3) Add App Name and click to submit.
4) Now, go to App Keys section and copy App ID, Secret, Cluster and Key and paste in .env file

- Install pusher-php-server to use driver as pusher ````composer require pusher/pusher-php-server```

- Install laravel echo server ```npm install --save-dev laravel-echo pusher-js```

- Configure => resources/js/echo.js

```
import Echo from 'laravel-echo';
 
import Pusher from 'pusher-js';
window.Pusher = Pusher;
 
window.Echo = new Echo({
    broadcaster: 'pusher',
    key: import.meta.env.VITE_PUSHER_APP_KEY,
    cluster: import.meta.env.VITE_PUSHER_APP_CLUSTER,
    forceTLS: false
});
```

-  Set the BROADCAST_CONNECTION environment variable to pusher in application's :

```
BROADCAST_CONNECTION=pusher

PUSHER_APP_ID="1822914"
PUSHER_APP_KEY="b47df8a8ea52a814246e"
PUSHER_APP_SECRET="5f9397a869..."
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME="https"
PUSHER_APP_CLUSTER="ap2"

VITE_APP_NAME="${APP_NAME}"
VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
```

- again build js ```npm run build``` and run ```php artisan serve```

## User Roles and Permissions

<h3>Install spatie/laravel-permission Package</h3>

```
composer require spatie/laravel-permission
```

- Also customize changes on the Spatie package, and get the config file in config/permission.php and migration files : 

```
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
php artisan migrate
```

- Create Product Migration :

```
php artisan make:migration create_products_table
php artisan migrate
```

- Update User Models => app/Models/User.php

- Create Models ```php artisan make:model Product```

- Add Middleware on => bootstrap/app.php

<h3>Create Authentication</h3>

- install the laravel/ui package : ```composer require laravel/ui```
- generate auth scaffolding : ```php artisan ui bootstrap --auth```
- see the better layout of the login and register page :```npm install``` ```npm run build```

- Create Routes => routes/web.php
- Add Controllers :

```
php artisan make:controller UserController
php artisan make:controller ProductController
php artisan make:controller RoleController
```

<h3>Add Blade Files</h3>

- Theme Layout => app.blade.php
- Users Module => index.blade.php create.blade.php edit.blade.php show.blade.php
- Roles Module => index.blade.php create.blade.php edit.blade.php show.blade.php
- Product Module => index.blade.php create.blade.php edit.blade.php show.blade.php

<h3>Create Seeder For Permissions and AdminUser</h3>

- create a seeder for permissions : role-list role-create role-edit role-delete product-list product-create product-edit product-delete

- first create a seeder ```php artisan make:seeder PermissionTableSeeder```
- execute the PermissionTableSeeder seeder ```php artisan db:seed --class=PermissionTableSeeder```
- create a new seeder for creating an admin user ```php artisan make:seeder CreateAdminUserSeeder```
- run seeder ```php artisan db:seed --class=CreateAdminUserSeeder```
- run app ```php artisan serve``` ```http://localhost:8000```

```
Email: aditi@gmail.com
Password: 123456
```

## Socialite login with google account

- Install Socialite using composer package ```composer require laravel/socialite```

<h3>Create Google App</h3>

- Go to https://console.cloud.google.com/projectcreate and create a new project. 
- Click Select Project and go to APIs and Services->OAuth consent screen->add App information : app name-Laravel dev,user support email-aditisavaliya60@gmail.com and add Developer contact information-asavaliya093@rku.ac.in
- Choose User type - External
- Create Credentials : Select application type - web, add Authorized redirect URIs - http://127.0.0.1:8000/auth/google/callback.
- Copy client_id,client_secret and client_redirect
