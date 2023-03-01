# CreateReadUpdateDelete

```php

public function index()
{
    $products = Product::oderByDesc('id')->get();

    return view('admin.products.index', compact('products'))
}

public function show(Product $product)
{
    return view('admin.products.show', compact('product'));
}

public function create()
{
    return view('admin.products.create');
}

public function store(StoreProductRequest $request)
{
    $val_data = $request->validated();

    $product = Product::create($val_data);

    return to_route('admin.products.index')->with('message', "$product->name added successfully");
}

public function edit(Product $product)
{
    return view('admin.products.edit', compact('product'));
}

public function update(UpdateProductRequest $request, Product $product)
{
    $val_data = $request->validated();

    $product->update($val_data);

    return to_route('admin.products.index')->with('message', "$product->name update successfully");
}

public function destroy(Product $product)
{
    $product->delete();

    return to_route('admin.products.index')->with('message', "$product->name deleted successfully");
}

```

```php

// Route::get('products', [ProductController::class, 'index'])->name('products.index');
// Route::get('products/create', [ProductController::class, 'create'])->name('products.create');
// Route::post('products', [ProductController::class, 'store'])->name('products.store');
// Route::get('products/{product}', [ProductController::class, 'show'])->name('products.show');
// Route::get('products/{product}/edit', [ProductController::class, 'edit'])->name('products.edit');
// Route::put('products/{product}', [ProductController::class, 'update'])->name('products.update');
// Route::delete('products/{product}', [ProductController::class, 'destroy'])->name('products.destroy');
Route::resouce('admin/products', ProductController::class);

```