```php

composer create-project laravel/laravel laravel-comics
cd laravel-comics
composer require pacificdev/laravel_9_preset
php artisan preset:ui bootstrap

```

// il seguente comando blocca il terminale (funziona solo su bash e non su powershell)
```php
npm i && npm run dev
```
// apri un nuovo terminale
```php
php artisan serve
```
LARAVEL AUTH
```php
 Laravel new nomecartella --git
 composer require laravel/breeze --dev
 php artisan breeze:install
 composer require pacificdev/laravel_9_preset
 php artisan preset:ui bootstrap --auth
 npm i
 npm run dev
 php artisan serve 
```
*github

Database
- mysql -uroot -ppassword
- create database nomedb;
- exit
*.env per collegamento
- php artisan migrate
