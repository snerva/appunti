# Laravel Auth (Relationships)


## 1

```php

php artisan make:model Type

php artisan make:controller Admin/TypeController -r --model=Type

php artisan make:migration create_types_table

php artisan make:seeder TypeSeeder 

php artisan make:migration add_type_id_foreign_to_projects_table


```

## 2

- Migration 

```php

 public function up()
    {
        Schema::create('types', function (Blueprint $table) {
            $table->id();
            $table->string('name', 100);
            $table->string('slug');
            $table->timestamps();
        });
    }

```

## 3

- Seeder

```php

  public function run()
    {
        $types = ['Front-end', 'Back-end', 'Full-Stack'];

        foreach ($types as $type) {
            $newType = new Type();
            $newType->name = $type;
            $newType->slug = Str::slug($newType->name);
            $newType->save();
        }
    }

```

## 4

- Migration

```php

public function up()
    {
        Schema::table('projects', function (Blueprint $table) {
            $table->unsignedBigInteger('type_id')->nullable()->after('id');
            $table->foreign('type_id')->references('id')->on('types')->onDelete('set null');
        });
    }


public function down()
    {
        Schema::table('projects', function (Blueprint $table) {
            $table->dropForeign('projects_type_id_foreign');
            $table->dropColumn('type_id');
        });
    }

```

## 5

```php

php artisan migrate

php artisan db:seed --class=TypeSeeder

```


## 6

- Add the hasMany relationship to the Type Model

```php

use Illuminate\Database\Eloquent\Relations\HasMany;

public function projects(): HasMany
{
    return $this->hasMany(Project::class);
}

```

## 7

- Add the inverse relationship inside the Project model

```php

use Illuminate\Database\Eloquent\Relations\BelongsTo;

public function type(): BelongsTo
{
    return $this->belongsTo(Category::class);
}

```

## 8

- In *Project.php*

```php

protected $fillable = ['title', 'body', 'slug', 'cover_image', 'type_id'];

```

## 9

- Inside the ProjectController pass all types to the create and update methods

```php

public function create()
{
    $types = Type::all();
        
    return view('admin.projects.create', compact('types'));
}

```

## 10

- Add in *create.blade.php* 

```php

<div class="mb-3">
    <label for="type_id" class="form-label">Types</label>
    <select class="form-select form-select-lg @error('type_id') 'is-invalid' @enderror" name="type_id" id="type_id">
        <option selected>Select one</option>

        @foreach ($types as $type )
        <option value="{{$type->id}}" {{ old('type_id') ? 'selected' : '' }}>{{$type->name}}</option>
        @endforeach

    </select>
</div>
@error('type_id')
<div class="alert alert-danger" role="alert">
    {{$message}}
</div>
@enderror
    
```

## 11

- Validate types inside the *StorePostRquest*

```php

public function rules()
    {
        return [
            'title' => 'required|unique:posts,title|max:100',
            'cover_image' => 'nullable|image|max:300',
            'type_id' => 'nullable|exists:types,id',
            'body' => 'nullable'
        ];
    }

```

## 12

- Add all types to the method and pass them to the view

```php

 public function edit(Project $project)
    {
        $types = Type::all();

        return view('admin.projects.edit', compact('project', 'types'));
    }

```

## 13

- Add a select to the *edit.blade.php*

```php
 
    <div class="mb-3">
        <label for="type_id" class="form-label">Types</label>
        <select class="form-select form-select-lg @error('type_id') 'is-invalid' @enderror" name="type_id" id="type_id">
            <option value="">No Type</option>

            @forelse ($types as $type )

            <!-- Check if the post has a category assigned or not 👇 -->
            <option value="{{$type->id}}" {{ $type->id == old('type_id',  $project->type ? $project->type->id : '') ? 'selected' : '' }}>
                {{$type->name}}
            </option>
            @empty
            <option value="">Sorry, no types in the system.</option>
            @endforelse
        </select>
    </div>
    @error('type_id')
    <div class="alert alert-danger" role="alert">
        {{$message}}
    </div>
    @enderror

```

## 14

- Update validation in the *UpdateProjectRequest*

```php

public function rules()
    {
        return [
            'title' => ['required', 'max:100', Rule::unique('posts')->ignore($this->post->id)],
            'cover_image' => ['nullable', 'image', 'max:300'],
            'type_id' => ['nullable', 'exists:types,id'],
            'body' => ['nullable']
        ];
    }

```

## 15

- Show category inside the *admin.projects.show*

```php

<div class="type">
    <strong>Type:</strong>
    {{ $project->type ? $project->type->name : 'No Type'}}
</div>

```