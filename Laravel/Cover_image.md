# Permettere all'utente di salvare le immagini da interfaccia in storage>app>public

## 1

```php

php artisan storage:link

php artisan make:migration add_cover_image_to_projects_table

```

## 2

- config > filesystems.php  -> 'public'
- *.env*

```php

FILESYSTEM_DISK=public 

```


## 3

- 
```php

public function up()
    {
        Schema::table('projects', function (Blueprint $table) {
            
            $table->string('cover_img')->nullable()->after('id');
           
        });
    }
    
public function down()
    {
        Schema::table('projects', function (Blueprint $table) {
            $table->dropColumn('cover_image');
        });
    }

```

## 4

```php

php artisan migrate

```

## 5 (opzionale)

- Modifico il seeder della tabella aggiungendo la colonna, utilizzando in questo caso il faker
```php

  $project->cover_img = 'placeholders/' . $faker->image('storage/app/public/placeholders',400, 200, 'project', false, false);

```

```php

php artisan db:seed --class=ProjectSeeder 

```

- (se -> Cannot write to directory "storage/app/public/placeholders" creo la cartella storage a mano)


## 6

- Aggiungere il campo immagine nel form del file create
```php

<div class="mb-3">
    <label for="cover_image" class="form-label">Image</label>
    <input type="file" name="cover_image" id="cover_image" class="form-control @error('cover_image') is-invalid @enderror" placeholder="Insert an image" aria-describedby="helpImage" value="">
    <small id="helpImage" class="text-muted">Please enter the image</small>
</div>
<!-- /.Cover_image -->
@error('cover_image')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror

```

## 7

- (ricordandosi di aggiungere nello stesso file enctype)
<form action="{{route('admin.projects.store')}}" method="post" enctype="multipart/form-data">

## 8

- Modifico la validazione dei dati aggiungndo l'immagine
```php

return 
[
    'cover_img'=>'nullable|image|max:250',
    'title'=> 'required|min:3',
    'description'=> 'nullable',
];

```

## 9

- modifico il metodo store nel controller
```php

use Illuminate\Support\Facades\Storage;
 public function store(StoreProjectRequest $request)
    {
        //dd($request->all());
        $validated_data = $request->validated();

        // Se viene aggiunta dall'utente, inserisco il valore img in una variabile
        if($request->hasFile('cover_img')){
        $cover_img = Storage::put('uploads', $validated_data['cover_img']);
        //sostituisco il valore di cover_img nei dati validati
        $validated_data['cover_img'] = $cover_img;
        }

        // Project slug
        $project_slug = Project::createSlug($validated_data['title']);
        $validated_data['slug'] = $project_slug;

        // Creo una nuova istanza
        Project::create($validated_data);

        return to_route('admin.projects.index')->with('message', 'New Project added');
    }

```

## 10

- Aggiungi al massive assignment in Models>Nomemodello la colonna dell'immagine

```php

protected $fillable = ['cover_img','title','slug','description'];

```

## 11

- Aggiungo l'immagine alla tabella nell'index

```php

<th>image</th>
 
<td><img src="{{asset('storage/' . $project->cover_img)}}" alt="">{{$project->title}}</td>

```

## 12

- Aggiungo il campo immagine nella view show per vederla solo se è presente
```php

@if($project->cover_img)
<img class="img-fluid" src="{{asset('storage/' . $project->cover_img)}}" alt="">
@endif

```

## 13

- Modificare la view edit incollando il pezzo di form crato precedentemente in create e fare le seguenti modifiche
```php

<form action="{{route('admin.projects.update', $project->slug)}}" method="post" enctype="multipart/form-data">
<div class="mb-3">
    <img class="edit_form_img" src="{{asset('storage/' . $project->cover_img)}}" alt="">
    <label for="cover_img" class="form-label">Edit cover image</label>
    <input type="file" name="cover_img" id="cover_img" class="form-control @error('cover_img') is-invalid @enderror" placeholder="" aria-describedby="helpId">
    <small id="helpId" class="text-muted">Edit cover image, must be max 250kb</small>
</div>
@error('cover_img')
<div class="alert alert-danger">{{$message}}</div>
@enderror

```

## 14

- Lavoro sul metodo update nel controller
```php

public function update(UpdateProjectRequest $request, Project $project)
    {
        $validated_data = $request->validated();

        if($request->hasFile('cover_img')){
            //se il post ha già un'immagine, la cancello, per poi inserirne un'altra
            if($project->cover_img){
                Storage::delete($project->cover_img);
            }
            $cover_img = Storage::put('uploads', $validated_data['cover_img']);
            //sostituisco il valore di cover_img nei dati validati
            $validated_data['cover_img'] = $cover_img;
        }
      
        //project slug
        $project_slug = Project::createSlug($validated_data['title']);
        $validated_data['slug'] = $project_slug;

        $project->update($validated_data);

        return to_route('admin.projects.index')->with('message', " Project $project->title modified");
    }

```

## 15

- Modifico la validazione dei dati aggiungndo l'immagine

```php

return [
    'cover_img'=>'nullable|image|max:250',
    'title'=> 'required|min:3',
    'description'=> 'nullable',
];

```

## 16

- Implementare il metodo delete per l'immagine
```php

public function destroy(Project $project)
    {
        //se esiste un'immagine, cancellala
        if($project->cover_img){
            Storage::delete($project->cover_img);
        }
        $project->delete();
        return to_route('admin.projects.index')->with('message', " Project $project->title deleted");
    }

```