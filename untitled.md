### Install Composer from  here
https://getcomposer.org/download

### Install Lumen

https://lumen.laravel.com/docs/8.x

### Create a Lumen Project

`composer create-project --prefer-dist laravel/lumen PROJECTNAME`

`cd PROJECTNAME`

### Install Artisan Command

`composer requier flipbox/lumen-generator`

##### Configuration

Inside your bootstrap/app.php file, add:

`$app->register(Flipbox\LumenGenerator\LumenGeneratorServiceProvider::class);`
  
### Create an API
Create a DB  (On Xampp for example in Phpmyadmin ) and put the credential in the .env file of the lumen project

Create a key for the db access

`php artisan key:generate`

Paste it in the env file (APP_KEY= THEGENERATEDKEY)

 Create the necessary file with a single command

`php artisan make:model CONTROLLERNAME -fmc`

**Now we will add fields to the migration file**

Go in database folder of the lumen project and in the file that we create with the previous step we will add the fields

```php
$table->id();
$table->string(‘title’);
$table->text(‘body’);
$table->timestamps();
```


**Lets migrate the database**
`php artisan migrate`

To insert some dummy stuff in the table

Go in database/factories CONTROLLERNAMEFactory.php and in the **return**:

```php
[
	‘title‘ => $this->faker->sentence
	‘body‘=> $this->faker->paragraph
];
```
Go in app/Models CONTROLLERNAME.php and use HasFactory

```php
class CONTROLLERNAME extend Model  
{  
    use HasFactory;  
}
```
run   
  
`php artisan thinker`

and pass `App\Models\CONTROLLERNAME::factory()->count(NUMBEROFDUMMYROWS)->create()`

##### Create routes and the API

go in routes/web.php and create the route   
api as prefix is needed to don't repeat api everytime, post is an example name too.
For making things simple we will try to create route and controller for blog posts
  
```php
$router->group(['prefix' => 'api'], function () use ($router) {
    $router->get('/posts', 'PostController@index');
    $router->post('/posts', 'PostController@store');
    $router->put('/posts/{id}', 'PostController@update');
    $router->delete('/posts/{id}', 'PostController@destroy');  
});


```

in app/Http/Controllers/PostController.php

```php
<?php

namespace App\Http\Controllers;
use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        return Post::all();
    }

    public function store(Request $request)
    {
        try{
            $post = new Post();
            $post->title = $request->title;
            $post->body  = $request->body;
            if ($post->save()){
                return response()->json(['status' => 'success', 'message' => 'Post created successfully']);
            }
        } catch (\Exception $e) {
            return response()->json(['status' => 'error', 'message' => $e->getMessage()]);
        }
    }

    public function update(Request $request, $id)
    {
        try{
            $post = Post::findOrFail($id);
            $post->title = $request->title;
            $post->body  = $request->body;
            if ($post->save()){
                return response()->json(['status' => 'success', 'message' => 'Post updated successfully']);
            }
        } catch (\Exception $e) {
            return response()->json(['status' => 'error', 'message' => $e->getMessage()]);
        }
    }


    public function destroy($id)
    {
        try{
            $post = Post::findOrFail($id);
            if ($post->delete()){
                return response()->json(['status' => 'success', 'message' => 'Post deleted successfully']);
            }
        } catch (\Exception $e) {
            return response()->json(['status' => 'error', 'message' => $e->getMessage()]);
        }
    }

}
```
go in app/bootstrap/app.php and uncomment

`$app->withEloquent`  

Now try to do the request in your frontend or postman for example at:  
 `localhost/api/posts`





