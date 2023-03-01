# Validation


## 1 Rules

- Aggiungi in  *StoreProductRequest.php* e *UpdateProductRequest.php* le seguenti funzioni:

```php

public function rules()
{
    return [
        'title' => 'required|unique:products,title|min:5|max:100',
        'src' => 'nullable|max:255',
        'description' => 'nullable',
        'type' => 'nullable|max:20',
        'cooking_time' => 'nullable|max:10',
        'weight' => 'nullable|max:10'
    ];
}

public function messages()
{
    return [
        'title.required' => 'Il titolo é obbligatorio',
        'title.min' => 'Il titolo deve essere almeno :min caratteri',
        'title.max' => 'Il titolo deve essere almeno :max caratteri',
        'src.max' => "L'immagine puó essere massimo :max caratteri",
        'type.max' => 'Il tipo di pasta puó essere massimo :max caratteri',
        'cooking_time.max' => 'Il tempo di cottura puó essere massimo :max caratteri',
        'weight.max' => 'Il tempo di cottura puó essere massimo :max caratteri'

    ];
}

```


- Change in *ProductController.php*:

```php

public function store(StoreProductRequest $request)
{
    // Validate data
    $val_data = $request->validated();
    // Save all data
    $product = Product::create($val_data);
    // redirect to a get route
    return to_route('products.index')->with('message', "$product->title added successfully");
}

public function update(UpdateProductRequest $request, Product $product)
{
    $val_data = $request->validated();

    $product->update($val_data);

    // return redirect()->route('products.index');
    return to_route('products.index')->with('message', "$product->title update successfully");
}

```

- Make *views/partials/errors.blade.php*:
- https://laravel.com/docs/9.x/validation#quick-displaying-the-validation-errors

```php

@if ($errors->any())
<div class="alert alert-danger">
    <ul>
        @foreach ($errors->all() as $error)
        <li>{{ $error }}</li>
        @endforeach
    </ul>
</div>
@endif

```

- Add in *create.blade.php*:

```php

@include('partials.errors')

```

- Add in *edit.blade.php*:

```php

@include('partials.errors')

```