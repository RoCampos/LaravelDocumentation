# Controladores

No capítulo sobre rotas, utilizamos o arquivo `web.php` para registrar as nossas rotas e também para implementar as funções associadas a cada uma delas. Vamos manter o registro das nossas rotas neste arquivo, entretanto as funções que implementam as ações de cada rota vão ser transferidas para um arquivo a parte.

É aí onde entra em cena os `controladores`. Na arquitetura MVC, um dos componentes é o componente Controller. Que basicamente orquestra o trabalho de acessoa o banco de dados (Model) e a camada de visão (View). No nosso caso, teremos classes do tipo Controller que farão parte da camada controller do MVC.

> O objetivo de um Controller é agrupar funções (ações) referentes a um modelo sobre o qual precisamos implementar rotinas para satisfazer aos requisitos de uma aplicação. Vale ressaltar que podemos ter diferentes controladores em um mesmo projeto.

## Implementação de um Controller

Vamos tomar por base o nosso projeto de Criar/Gerenciar tarefas. O projeto tem as rotas definidas no arquivo `web.php` da seguinte maneira:

```php
//trecho do arquivo web.php

Route::get('/task/create', function(){
    return view('todolist.create');
});

Route::post('/task/create', function(Request $request){
    //os dados ainda não são salvos no bd
    $dado = $request->all(['task']);
    return $dado;
});

```

Observe que a medida que o projeto cresce, o arquivo `web.php` vai se tornar gigantesco. Vamos resolver esse problema.

Primeiro, execute o comando abaixo em seu terminal dentro da pasta do projeto:

> php artisan make:controller TaskController

Esse comando vai criar um arquivo chamado TaskController.php dentro da pasta `app/Http/Controller` e seu conteúdo é ilustrado abaixo:

```php
// Arquivo TaskController.php
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class TaskController extends Controller
{

}
```

Com o controlador de tarefas 'TaskController', é possível separar a definição das rotas da ação que cada uma executa para entregar ao cliente o que ele deseja.

Por exemplo, as rotas para editar um formulário vão continuar sendo as mesmas:

> localhost:8000/task/create

> localhost:8000/task/store

Entretanto, No arquivo `web.php`, as rotas ilustradas no início desta seção vão ser modificadas para o seguinte formato:

```php
use App\Http\Controllers\TaskController;

//novo formato de declação de rotas usando Controladores
Route::get('/task/create', [TaskController::class, 'create']);

Route::post('/task/create', [TaskController::class, 'store']);
```

Esta forma registrar rotas indica que vamos associar a rota `/task/create` para requisições GET a seguinte função do controlloador `create`. É isso que siginifica a declaração `[TaskController::class, 'create']` na função `Route::get`. 

Você deve estar se perguntando, mas onde fica código que havia associado a rota? A resposta é simples. O código associado a ação da rota mencionada ficará no controlador conforme ilustrado abaixo:

```php
// Arquivo TaskController.php
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class TaskController extends Controller
{
    public function create () {
        return view ('todolist.create');
    }

    public function store(Request $request) {
        $description = $request->post('task');
        return ['task'=>$description];
    }
}
``` 