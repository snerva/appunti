# Controller and view

```php

php artisan make:controller Admin/DashboardController

```

## 1
- Add in *DashboardController.php*:

```php

public function index()
{
    return view('dashboard');
}

```

## 2
- Add in *web.api*:

```php

// Rotte visibili solamente agli utenti loggati
Route::middleware(['auth', 'verified'])->name('admin.')->prefix('admin')->group(function() {
    Route:get('/', [DashboardController::class, 'index'])->name('dashboard');  
});

```

## 3
- Change in *RouteServiceProvider.php* from *dashboard* to *admin*:
- Change in *app.blade.php* from *url('dashboard')* to *url('admin')*:

```php

<a class="dropdown-item" href="{{url('admin')}}">{{__('Admin')}}</a>

```

## 4

- Change in *views/dashboard.blade.php* from *@extends('layouts.app')* to *@extends('layouts.admin')*


