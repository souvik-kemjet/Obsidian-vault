

```php
php artisan key:generate

php artisan migrate

php artisan db:seed

php artisan storage:link

npm install
php artisan serve 
php artisan tinker
php artisan route:list --except-vendor


# To insert an Super Admin Details using SQL
php artisan tinker
<?php

\App\Models\SuperAdmin::updateOrCreate(

    ['email' => 'you@example.com'],

    [

        'name' => 'Your Name',

        'password' => 'Use-a-long-unique-password',

        'status' => 'active',

    ]

);
```



---
```php
# To insert an Super Admin Details using SQL
php artisan tinker
<?php

\App\Models\SuperAdmin::updateOrCreate(
    ['email' => 'soruno@kemjethimalog.com'],
    [
        'name' => 'Soruno Kent',
        'password' => 'Kemjet@2026',
        'status' => 'active',
    ]
);
```


**Command 1:** `php artisan config:clear`
```php
php artisan config:clear

```
**Purpose:** Remove Laravel's cached configuration.
When you change `.env`, an old configuration cache can prevent the application from picking up the new values. This command clears that cache so Laravel can load configuration again.


**Command 2:** `php artisan config:cache`
```php
php artisan config:cache
```
**Purpose:** Build a fresh configuration cache.
Laravel reads the configuration and creates an optimized cache for production. This can improve performance and makes the current configuration available through the cache.

```bash
tail -f storage/logs/laravel.log
```