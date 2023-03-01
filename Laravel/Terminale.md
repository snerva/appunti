# Install

```php

composer create-project laravel/laravel nome_progetto

composer require laravel/breeze --dev

php artisan breeze:install

composer require pacificdev/laravel_9_preset 

php artisan preset:ui bootstrap --auth

npm i && npm run dev

php artisan serve

```


# DB, MVC

```php

// - php artisan make:migration create_products_table
// - php artisan make:seeder ProductSeeder
// - php artisan make:model Product
// - php artisan make:resource ProductController
php artisan make:model Product -a

// Make a Seeder file
php artisan make:seeder ProductSeeder

// Fill column Database
php artisan db:seed --class=ProductSeeder

// Create a Table Database
php artisan make:migration create_products_table

// Add a column to table Database
php artisan make:migration add_cover_image_to_products_table

// Push migration
php artisan migrate

// Delete last migration
php artisan migrate:rollback

// Mostri tutti i comandi per il model
php artisan help make:model Product

```


# Pagination

```php

// Installa le cartelle per la paginazione tramite bootstrap
php artisan vendor:publish --tag=laravel-pagination

```


# Clone

```php

// Clona repo
git clone https:/github.com/PasqualeAragosa/laravel-one-to-many.git nome-nuova-repo

// Rimuove il file git
rm -rf .git

composer install

git init 

php artisan key:generate

```

# Branch

```php

// Ti permette di creare e spostarti nella branch
git checkout -b 3-creazione-seeder

// Ti permette di creare una nuova branch
git branch 3-creazione-seeder

// Ti permette di muoverti in una branch esistente
git checkout 3-creazione-seeder

// Cancella la branch
git branch -d 3-creazione-seeder

```