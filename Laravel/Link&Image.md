## Image and Active link


# 1

- In *welcome.blade.php* 
```php
    
<header>
    <ul>
        <li>
            <!-- Logo -->
            <div class="logo">
                // Immagine
                <img src="{{ Vite::asset('resources/img/logo.png') }}" alt="">
            </div>
        </li>
        <li>
            <!-- Link Page and Active class -->
            <a class="{{Route::currentRouteName() === 'welcome' ? 'active' : '' }}" href="{{route('welcome')}}">Home Page</a>
        </li>
    </ul>
</header>

```
