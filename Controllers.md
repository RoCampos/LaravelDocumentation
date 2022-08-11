# Controladores

No capítulo sobre rotas, utilizamos o arquivo `web.php` para registrar as nossas rotas e também para implementar as funções associadas a cada uma delas. Vamos manter o registro das nossas rotas neste arquivo, entretanto as funções que implementam as ações de cada rota serão transferidas para um arquivo a parte.

Neste momento, os `controladores` entram em cena. Na arquitetura MVC, um dos componentes é o Controller, que basicamente orquestra o trabalho de acesso ao banco de dados (Model) e a camada de visão (View). No nosso caso, teremos classes do tipo Controller que farão parte da camada controller do MVC. 

> O objetivo de um Controller é agrupar funções (ações) referentes a um modelo sobre o qual precisamos implementar rotinas para satisfazer os requisitos de uma aplicação. Vale ressaltar que podemos ter diferentes controladores em um mesmo projeto.

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

Observe que a medida que o projeto cresce, o arquivo `web.php` vai se tornar gigantesco!!! Vamos resolver esse problema.

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
    //aqui ficarão todas as funções associadas as nossas rotas
}
```

Com o controlador de tarefas `TaskController`, é possível separar a definição das rotas da ação que cada uma executa para entregar ao cliente o que ele deseja.

Por exemplo, as rotas para criar uma tarefa vão continuar sendo as mesmas:

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

## Controlador Resource

Até este ponto estamos discutindo sobre rotas, controladores, ações e como implementar estes recursos em nossas aplicações. Sobre o uso de controladores é importante enteder que:

> O controlador agrupa as ações (funções) associadas a um único recurso como criar um registro no banco, mostrar um registro específico para o usuário e mais alguma tarefa específica do problema. Portanto, um controlador está associado sempre a um único recurso.

Desta maneira, temos como gerar um controlador do tipo Resource e obter vantagens de usar um framework. A primeira vantagem é a geração de código e a segunda é uso de convenções bem estabelecidas que facilitam a comunicação entre os membros da equipe.

**Criar um controlador Resource**

Para criar um controlador Resource executeo seguinte comando:

> php artisan make:controler TaskController --resource

O parâmetro `TaskController` é o nome do controlador que estamos criando e o `--resource` indica o tipo do controlador: neste caso Resource.

**Importante:** considere que não temos mais o controlador criado na seção anterior deste texto. 

Alem disso, este controlador tem seu arquivo correspondente salvo em `app/Http/Controllers/TaskController.php`.

O resultado do comando acima será:

```php
//arquivo armazenado na pasta app/Http/Controllers
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class TaskController extends Controller
{ 
    public function index() {
        
    }
    
    public function create () {
       
    }

    public function store(Request $request) {

    }

    public function edit(Request $request, $id) {
        
    }

    public function update (Request $request, $id) {
        
    }

    public function show ($id) {
        
    }

    public function destroy ($id) {
       
    }

}
```

A classe acima mostra o resultado da criação de um controlador Resource. Você deve estar se perguntando: ao gerar um controlador eu já tenho as rotas todas prontas no arquivo web.php?

**Registrando Rotas com Controlador Resource**

A seção anterior foi finalizada com uma pergunta e sua resposta é *não*. Nesta caso, ainda precisamos vincular estas funções as rotas que temos lá no arquivo web.php. Entretanto, vamos ganhar muito mais simplicidade na definição das rotas. 

O código abaixo ilustra como é registrada cada rota associada a cada função do controlador TaskController:

```php
<?php

use Illuminate\Support\Facades\Route;
use Illuminate\Http\Request;
use App\Http\Controllers\TaskController;

//o restante do código de web.php

Route::resource('/tasks', TaskController::class);
```

A declaraçaão `Route::resource` é novidade até aqui. Essa declaração realiza muita coisa no projeto. Primeiro, vamos entedê-la e em seguida ver o resultado

Estamos declarando rotas cujo caminho básico é `/tasks`. Essas rotas vão ser associadas as funções definidas em TaskController. Por isso o controlador já vem com as funções ilustradas anteriomente neste texto e com aqueles nomes específicos (`destroy`, `index` e etc).

Se você quiser ver as rotas geradas, basta digitar o seguinte comando no CMD dentro da pasta do seu projeto:

> php artisan route:list

E obter o seguinte resultado: 

```
GET|HEAD    tasks        ............... tasks.index › TaskController@index

POST        tasks        ............... tasks.store › TaskController@store

GET|HEAD    tasks/create ............... tasks.create › TaskController@create

GET|HEAD    tasks/{task} ................ tasks.show › TaskController@show  

PUT|PATCH   tasks/{task} ................ tasks.update › TaskController@update  

DELETE      tasks/{task} ................ tasks.destroy › TaskController@destroy  

GET|HEAD    tasks/{task}/edit ........... tasks.edit › TaskController@edit
```

O resultado que você observa acima é o conjunto de rotas gerado pelo registro realizado em web.php pela declaração `Route::resource`. Vou tomar por exemplo a rota `/tasks`. 

A rota `/tasks` neste exemplo está associada a função `index` presente em TaskController. Isso pode ser visto no resultado de route:list acima: `TaskController@index`. Além disso, essa rota tem o seguinte caminho: `/tasks`. Outra informação importante é o fato de cada rota ter um nome específico pelo qual ela pode ser referenciada (vamos ver isso). O nome da rota para `/tasks` é `tasks.index`. Por fim, também é sinalizado o verbo HTTP da rota. Neste caso, `GET`.

As demais rotas tem a mesma interpretação do parágrafo anterior. Um detalhe importante a ser observado são os parâmetros da rota, que foram definidos como sendo `{task}`. Esse parâmetro fará mais sentido quando falarmos sobre a parte de modelos com a inclusão de um modelo chamado `Task`.

Por fim, você ainda está se perguntando  onde ficam as funções anônimas das rotas que tínhamos definido antes do uso de controlador Resource? Usarei o exemplo da antiga rota `Route::get('/task/create', function(){})`.

A antiga rota registrada para criar uma tarefa era a seguinte:

```php

Route::get('/task/create', function() {
    return view ('todolist.create');
})
```

Após o uso de `Route::resource`, podemos pegar a implementação da função anônima acima e adicioná-la a função `create` da classe TaskController conforme ilustrado abaixo:

```php
//aqui foi omitido o restante do código da classe TaskController
//o foco é somente mostrar a implementação da função create.
class TaskController extends Controller
{
    public function create()
    {
        return view ('todolist.create');
    }
}
```

A conclusão final é a seguinte: registramos no web.php o nosso controlador resource. Lá são definidas as roats conforme vimos e na classe TaskController implementamos as funções necessárias que antes estavam no web.php.







