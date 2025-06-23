# Step by Step
### Model e Migration
1. Crea un nuovo progetto con `laravel new nome-progetto`
2. Lancia `php artisan serve` in un nuovo terminale
3. Apri un nuovo terminale, installa e lancia `npm i bootstrap` e successivamente `npm run dev`
4. Importa Bootstrap dentro `resources/css/app.css` e `resources/js/app.js`; Ricorda di importare `@vite([])`
5. Apri TablePlus e crea il database del progetto;
6. Configura il Database dentro il file `.env`

```

DB_CONNECTION=mysql 
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=nome_del_DB (il nome usato al punto 5)
DB_USERNAME=tuo_username (solitamente root)
DB_PASSWORD=tua_password (solitamente rootroot)
```

7. Crea la migrazione per la tabella `books` con il comando `php artisan make:migration create_books_table`
8. Andiamo ad aggiungere 3 colonne:
    
    ```php
    $table->string('name');
    $table->integer('years')
    $table->integer('pages')
    ```
    
9. Lancia la creazione della tabella con `php artisan migrate`

10. Definisci le rotte:

```php

  Route::get('/books', [BookController::class, 'index'])->name('books.index');
  Route::get('/books/create', [BookController::class, 'create'])->name('books.create');
  Route::post('/books/store', [BookController::class, 'store'])->name('books.store');

```

11. Crea il controller `BookController` con il comando `php artisan make:controller BookController`
12. Scrivi i 3 metodi nel controller:
    - index: Recupera tutti i libri con Book::all();
    - create: Un semplice form;
    - store: L’azione da eseguire quando si clicca su salva;
13. Crea le view relative utilizzando un layout ed eventualmente dei componenti per creare una struttura responsive con Bootstrap 5.
    - books/index
    - books/create
    - components/main
    - components/navbar 
14. Aggiungi la nuova colonna negli attributi `$fillable` del Model appena creato Book
```php artisan make:model Book ```

```php
  protected $fillable = [
        'name',  'pages', 'years'
    ];
```

15. Definisci le rotte facendo attenzione ad utilizzare la nomenclatura corretta anteponendo il nome del model avanti al metodo (book.index ad esempio):

```php 
Route::get('/books', [BookController::class, 'index'])
  ->name('books.index');

Route::get('/books/create', [BookController::class, 'create'])
  ->name('books.create');

Route::post('/books/store', [BookController::class, 'store'])
  ->name('books.store');

Route::get('/books/{book}/show', [BookController::class, 'show'])
  ->name('books.show');
```
16. Se non lo hai ancora fatto, crea il controller `BookController` con il comando `php artisan make:controller BookController`
17. Scrivi i 4 metodi nel controller:
    - index: Recupera tutti i libri con Book::all();
    - create: Un semplice form;
    - store: L’azione da eseguire quando si clicca su salva;
    - show: una pagina di dettaglio per visualizzare il dato singolo
18. Crea le view relative utilizzando un layout ed eventualmente dei componenti per creare una struttura responsive con Bootstrap 5.
    - books/index
    - books/create
    - books/show
    - components/main
    - components/navbar


### Laravel Fortify & Middleware

1. Installo fortify: composer require laravel/fortify

2. Porto i file di Fortify che mi servono da vendor al mio progetto:`php artisan fortify:install`

3. Aggiorno il database con le migrazioni di laravel `php artisan migrate`


4. In FortifyServiceProvider indico a Fortify come deve restituire le viste di login e register

    ```php
    Fortify::loginView(function () {
        return view('auth.login');
    });

    Fortify::registerView(function () {
        return view('auth.register');
    });
 
    // 
    ```
5. Creo il file `resources/views/auth/register.blade.php` .Questo blade ha bisogno di:

- method POST
- action {{ route('register') }}
- i campi: name, email, password, password_confirmation

6. Nella navbar, gestisco il logout (Scrivetelo a mano per abituarvi!)

```php
 <form action="{{ route('logout') }}" method="POST">
    @csrf
    <button class="btn btn-danger" type="submit">
        Esci
    </button>
</form>

```
7. Mi dara' errore, quindi gestisco la navbar con le direttive blade @auth @endauth o @guest @endguest

8. Creo il file `resources/views/auth/login.blade.php.` Questo blade ha bisogno di:

- method POST
- action {{ route('login') }}
- i campi: email, password
- Provo a loggarmi

Se tutto funziona, ho correttamente installato Laravel Fortify! 🚀
### CRUD
1. Aggiungo le rotte mancanti per compleatre il CRUD:
```php 
Route::get('/books/{book}/edit', [BookController::class, 'edit'])
  ->name('books.edit');

Route::put('/books/{book}/update', [BookController::class, 'update'])
  ->name('books.update');

Route::delete('/books/{book}', [BookController::class, 'destroy'])
  ->name('books.destroy');
```

2. Vado nel BookController e specifico i relativi metodi
3. Edit:

```php 
 public function edit(Book $book)
    {

        return view('books.edit', compact('book'));
    }
```

4. Update:
```php

 public function update(BookUpdateRequest $request, Book $book)
    {
       
    
        $book->update([
            'name' => $request->name,
            'pages' => $request->pages,
            'year' => $request->year,
        ]);

        return redirect()->route('books.index')->with('success', 'Libro Aggiornato');
    }

```

5. Destory:

```php

    public function destroy(Book $book)
    {
        $book->delete();
        return redirect()->route('books.index')->with('success', 'Libro Eliminato');
    }
```

6. View: In ultimo, creare la vista edit per gestire il form di modifica
7. Tutte le rotte pososno essere sostituite da: ```php Route::resource('books', BookController::class);```
