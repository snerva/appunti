# Laravel Auth (Relationships) MANY TO MANY

Tables:

- users
- posts
- categories
- technologies

Models:

- User
- Post
- Category
- Technology

### Creazione modello Technology
`php artisan make:model Technology -ms -R`

### Creazione Request di Technology
`php artisan make:request StoreTechnolgyRequest`
`php artisan make:request UpdateTechnolgyRequest`

### Creazione controller Technology nel name space Admin
`php artisan make:controller Admin/TechnologyController -r --model=Technology`

### Creazione migration
`php artisan make:migration create_technologies_table`

### Modifico function up nel file migration creato

 ```php
public function up()
    {
        Schema::create('categories', function (Blueprint $table) {
            $table->id();
            $table->string('name', 50)->unique();
            $table->string('slug');
            $table->timestamps();
        });
    }
```
### Faccio migrazione
`php artisan make:migration create_post_tag_table`

 Vado nel file della migrazione 
 Implementation up method

 ```php
public function up()
    {
        Schema::create('post_tag', function (Blueprint $table) {

            $table->unsignedBigInteger('post_id');
            $table->foreign('post_id')->references('id')->on('posts')->cascadeonDelete();

	$table->unsignedBigInteger('tag_id');
            $table->foreign('tag_id')->references('id')->on('tags')->cascadeonDelete();

	$table->primary(['post_id', 'tag_id']);

        });
    }
```
Non faccio implementazione down method, ma lascio così com'è


Faccio la migrazione
php artisan migrate


Vado nel modello Post e inserisco relazione BelongstoMany
```php
@return \Illuminate\Database\Eloquent\Relations\BelongsToMany
     */
    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class);
    }
```
⚡ Ricorda di importare la classe HasMany use Illuminate\Database\Eloquent\Relations\BelongsToMany; all'inizio del Model dopo il namespace.



Vado nel modello Tag e inserisco relazione BelongstoMany
```php
@return \Illuminate\Database\Eloquent\Relations\BelongsToMany
     */
    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Post::class);
    }
```
⚡ Ricorda di importare la classe HasMany use Illuminate\Database\Eloquent\Relations\BelongsToMany; all'inizio del Model dopo il namespace.


Entro nel file Technology Seeder

```php
public function run()
    {
        $tags = ['Programming', 'Laravel', 'Vuejs', 'front-end', 'back-end', 'full-stack'];

        foreach ($tags as $tag) {
            $newTag = new Tag();
            $newTag->name = $tag;
            $newTag->slug = Str::slug($newTag->name);
            $newTag->save();
        }
    }
```
Importo Technology e Str tramite use sopra sotto il namespace



### Faccio Seeding DB

`php artisan db:seed --class=TechnologySeeder`


### Update CRUD ops for Posts

update Create operations
Inside the ProjectController pass all types to the create and update methods

 ```php
public function create()
    {
        $tags= Tag::all(); //👈 get all tags
       
        return view('admin.posts.create', compact('categories', 'tags'));
        // pass the tags to the view        👆
    }
```
collegamento Modello Tag --> use Tag


Edit the CREATE view to show a select for categories admin/posts/create.blade.php
```html

<div class="mb-3">
        <label for="technologies" class="form-label">technologies</label>
        <select multiple class="form-select form-select-lg @error('technologies') 'is-invalid' @enderror" name="technologies[]" id="technologies">
            <option value='' disabled>Select a tag</option>
	

            @forelse ($technologies as $technology )

	@if($errors ->any())
	<option value="{{$technology->id}}" {{ in_array($technology->id, old('technologies', [])) ? 'selected' : '' }}>{{$technology->name}}</option>
	@else
            <option value="{{$technology->id}}" {{ old('technologies') ? 'selected' : '' }}>{{$technology->name}}</option>
	@endif

	@empty
	<option value='' disabled>Sorry no technologies</option>
            @endforelse

        </select>
    </div>

    @error('tags')
    <div class="alert alert-danger" role="alert">
        {{$message}}
    </div>
    @enderror

```
Validate tags inside the StorePostRequest

```php
public function rules()
    {
        return [
            'title' => 'required|unique:posts,title|max:100',
            'cover_image' => 'nullable|image|max:300',
            'category_id' => 'nullable|exists:categories,id', // 👈 The category id must exist!!
	 'tags' => 'nullable|exists:tags,id', // 👈 The tag id must exist!!
            'body' => 'nullable'
        ];
    }
```
Aggiungo nella funzione Store in PostController 
```php
if ($request->has('tags')) {
            $post->tags()->attach($val_data['tags']);
        }
```

Aggiungo nella funzione Update in PostController (così possiamo andare a modificare in automatico, aggiorna ed elimina i record vecchi selezionati, grazie al metodo Sync() )
```php
if($request->has('tags')){
$post->tags()->sync($val_data['tags']);
} else {
$post->tags()->sync([]);
}
```

Andiamo nella View Show.blade.php e inseriamo i Tag

```html
<div class="tags">
    <strong>Tags:</strong>
    @if(count($post->tags) > 0)
    

    @foreach ($post->tags as $tag )
        <span> #{{$tag->name}}</span>
    @endforeach

    @else
    <span> No tag</span>
    @endif
</div>
```


Update validation in the UpdatePostRequest
```php
public function rules()
    {
        return [
            'title' => ['required', 'max:100', Rule::unique('posts')->ignore($this->post->id)],
            'cover_image' => ['nullable', 'image', 'max:300'],
            'category_id' => ['nullable', 'exists:categories,id'],
	'tags' => ['nullable', 'exists:tags,id'],
            'body' => ['nullable']
        ];
    }
```

Update Edit operation
Add all types to the method and pass them to the view
```php
 public function edit(Post $post)
    {
        $tags= Tag::all(); //👈 get all tags
        return view('admin.posts.edit', compact('post', 'categories', 'tags'));
        // pass the tags to the view                👆
    }

```

Andiamo nella view Edit e modifichiamo 

```html
<div class="mb-3">
            <label for="technologies" class="form-label">technologies</label>
            <select multiple class="form-select form-select-lg @error('technologies') 'is-invalid' @enderror" name="technologies[]" id="technologies">
                <option value='' disabled>Select a tech</option>
        
    
                @forelse ($technologies as $technology )
    
        @if($errors ->any())
        <option value="{{$technology->id}}" {{ in_array($technology->id, old('technologies', [])) ? 'selected' : '' }}>{{$technology->name}}</option>
        @else
                <option value="{{$technology->id}}" {{ $project ->technologies->contains($technology->id) ? 'selected' : '' }}>{{$technology->name}}</option>
        @endif
    
        @empty
        <option value='' disabled>Sorry no technologies</option>
                @endforelse
    
            </select>
        </div>
    @error('tags)
    <div class="alert alert-danger" role="alert">
        {{$message}}
    </div>
    @enderror

```

Show tag inside the posts.show view

```html
<div class="tag">
    <strong>Tag:</strong>
    {{ $post->tag ? $post->tag->name : 'No Tag inside'}}
</div>
```