## Filtrare i risultati

# 1
- Creo un filtraggio dei dati
    ```php

    Route::get('/products', function () {
        $products_collection = collect(config('db.products'));
        $pasta_corta = $products_collection->where('tipo', '===', 'corta');

        return view('products', compact('products_collection', 'pasta_corta'));
    })->name('products');

    ```