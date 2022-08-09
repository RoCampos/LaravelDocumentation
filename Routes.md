# Rotas

*Romerito Campos, 07 Ago 2022.*


## Introducão

As rotas da nossa aplicacão são uma maneira prática de servirmos recursos aos clientes da mesma como um página, arquivo pdf etc.

Em laravel, podemos definir rotas de maneira bem simples para trabalharmos com requisicões HTTP. 

Quando criamos um projeto, uma pasta chamada `routes` é definida e dentro dela podemos encontrar 4 arquivos. Por hora, vamos focar no arquivo `web.php`.

Neste arquivo, por padrão vem definida uma rota e seu conteúdo é o seguinte:

```php
<?php

use Illuminate\Support\Facades\Route;
use Illuminate\Http\Request;

Route::get('/', function () {
    return view('welcome');
});

?>
```

## Classe Route e Componenentes de Rota

Conforme ilustrado no trecho de código acima, podemos observar que a classe `Route` é utilizada para definicão de rotas.

Isto acontece através do método de classe `get()`. 

Observe atentamente o código e veja que a função `Route::get()` recebe dois parâmetros. Vamos entender isso.

O primeiro parâmetro é **o caminho** que o usuário vai utilizar para navegar até obter a página que deseja.

Por exemplo, se você estiver desenvolvendo uma aplicacão provalvemente está rodando ela em ambiente local (localhost). Logo o endereço da aplicação será:

> http://localhost:8000

Ao adicionar o `/` ao final do endereço acima, estaremos acessando uma página (calma que você saberá que página é esta).

O segundo parâmetro da definição de um rota é **acão a ser realizada**, que pode se apenas mostrar uma página para o usuário ou realizar uma busca no banco de dados e adicionar o resultado a uma página. Muitas outras coisas podem ser realizadas, na verdade.

No trecho de código abaixo, temos apens uma definição de rota, observe:

```php
Route::get('/', function () {
    return view('welcome');
});
```

Observe que temos uma função anônima como segundo parâmetro utilizado na definição da rota. Esta função fará exatamente a ação necessário que desejarmos. Neste trecho de código específico, a acão é apenas devolver uma página para o usuário. OBSERVAção (gritando mesmo): o trecho `return view('welcome')` tem bastante detalhe. 


## Entendendo a ação de uma rota

Observe que a ação de uma rota está definida na função anônima ilustrada no bloco abaixo:

```php 
function () {
    return view('welcome');
}
```

Esta ação, que está associada ao caminho `/`, aplica a função `view()` a uma String `'welcome'`. O que isso significa?

Isso significa que estamos usando uma função do framewok Laravel, que também é chamada de *helper*. Essa função especificamente realiza a seguinte operação de acordo com a documentação: 
`retorna uma instacia de View`. Vamos facilitar a nossa vida!!!

A função *view* vai construir a nossa página para que o cliente receba ela(a página). Afinal de contas, é isso que o usuário está tentanto através da rota que estamos discutindo.

Mas de onde vem esse 'welcome'? Esta String representa o nome de um template (vamos chamar nossas páginas assim) armazenado na pasta `resources/views` (local padrão). Se você abrir o arquivo `welcome.blade.php`, verá o conteúdo da página (HTML e mais algumas coisas).

## Só existem rotas get()?

A resposta é não. Para entedermos melhor, vamos analisar como seria a definição da rota de login do usuário.

```php
<?php

use Illuminate\Support\Facades\Route;
use Illuminate\Http\Request;

// trecho do arquivo web.php
Route::get('/login', function () {
    return view('auth.login');
});

Route::post('/login', function (Request $request) {
    //aqui teremos uma avaliação dos dados do usuário
    return view('todolist.dashboard');
});
?>
```

### Rota login - obtendo formulário
A rota `login` definida por `Route::get` vai receber requisições do tipo HTTP GET. Imagine que você clica no botão `fazer login` em seu navegador e em seguida você recebe o formulário para inserir `email` e `senha`.

Considere o formulário abaixo. Ele representa um trecho do arquivo `login.blade.php`. Além disso, considere que ele não contém classes css nem nada que não seja relevante no momento.

Este formulário é rebido pelo usuário quando ele acessa `localhost:8000/login` no navegador. 

```html
<form action="{{url('/login')}}" method='POST'>
    <input type="email" name="email">
    <input type="password" name="passwrod">
    <button>Enviar</button>
</form>
```
### Rota login - processando o login do usuário

Na seção anterior, o usuário recebe o formulário HTML para realizar o login. Um vez que ele preencheu os dados, é hora de enviar esses dados para validar seu login. É aí que entra a rota login definida em `Route::post`.

Essa rota está relacionada a definição presente no formulário HTML. Quando o usuário clicar no botão `Enviar` no formulário apresentado, a requisição realizada será HTTP POST. Mas como? Na definição do `<form>`:

```html
...
<form action="{{url(/login)}}" method='POST'>
...
```

O método indicado no form `method=POST` indica que a requisição será HTTP POST, e a `action="{{url(/'login')}}"`.

A *action* definida no form recebe um endereço para onde a requisição será enviada. A sintaxe definida entre as aspas duplas `{{url('/login')}}` é especifica do Laravel. Quando utilizamos estas duas `{{` dentro do html, estamos indicado que dentro das chaves vamos colocar código php. Outra coisa: lembra do *helper* mencionado no início deste capítulo? Pois bem, `url` também é um *helper* laravel que utilizamos para criar o endereço completo para a rota `Login`. 

Portanto, se você estiver usando o ambiente local de desenvolvimento, o resultado do form será: `action=localhost:8000/login`. Quando você enviar o form, sendo ele method=POST, o laravel usará a rota `Route::post` definida para `/login`.

### As rotas podem receber parâmetros e injecão de dependência

Vamos analisar a seguinte situação. Mas antes você precisa ter certeza que entendeu a diferença entre POST e GET explicada anteriormente. Se você não entendeu, releia novamente. Eu espero.

Agora que eu sei que você entende a diferença entre POST e GET. Vamos aos parâmetros das rotas e injeção de dependência. Em primeiro lugar, temos que entender que uma aplicacão pode receber dados externos ao servidor. Por exemplo: imagine que você tem uma lista de tarefas e precisa cadastrar novas tarefas. Para simplicar o projeto, você decidiu que a tarefa só terá uma informacão que é a descricão.

*Injecão de dependência*

Sabemos que vamos mandar uma informacão para o servidor: descricão de uma tarefa que vamos salvar.

A rota `get` abaixo retorna o formulário para cadastro de uma tarefa:

```php
Route::get('/task/create', function(){
    return view('todolist.create');
});
```

O arquivo `create.blade.php` usado na função `view` e resumido abaixo(novamente sem css):

```html
<form action="{{url('/task/create')}}" method="POST">
    @csrf
    <input type="text" name="task" id="task">
    <button type="submit">Entrar</button>
<form>
```

Você deve estar se perguntando: mas o que significa a tag `@csrf`. Isso é uma tag. E logo mais será explicado. Vamos focar nos parâmetros.

Ao clicar no botão `Entrar` do form de create.blade.php, você está fazendo uma requisicão HTTP POST para `/task/create`. Portanto, você precisa ter uma rota POST com esse caminho. Ela é ilustrada abaixo:

```php
use Illuminate\Http\Request;
Route::post('/task/create', function(Request $request){
    //os dados ainda não são salvos no bd
    $dado = $request->post(['task']);
    return $dado;
});
```

Neste exemplo de rota, estamos injetando uma dependência na ação da rota (passando um objeto `$request` a função). Este objeto nos permite ter acesso a algumas informacões da requisicão. 

Observe o copo da função anônima da rota. Dentro dela obtemos o valor do input `task` definido no formulário presente em `create.blade.php`. Em seguida, o dado é armazenado em `$dado`. POr fim, esse dado é retornado e será mostrado em uma página. Observacão: em um projeto já avançado algumas tarefas seria realizadas: validar os dados, salvar no banco e redirecionar para uma rota com página contendo mais informacões. Faremos isso.

*Parâmetros*

Onde entram os parâmetros da rota? Até agora só foi falado sobre a injecão de dependência. Então vamos lá. Não explicarei detalhes que já foram mecionados acima.

Condidere que precisamos editar uma tarefa que está salva no banco de dados. Portanto, para mostrar os dados atual da tarefa para o usuário em um formulário html precisamos de alguma maneira de acessar esse dado. Uma meneira é pela rota GET definida para acessar um formulário de edicão. 

Imagine que no banco de dados existe uma tarefa com as seguintes informacões: `[id=1, description='primeira tarefa']`.

Você concorda que pode haver mais tarefas no banco com id`s diferentes? Espero que sim. Vamos direto ao ponto. Observe a rota abaixo:

```php 
Route::get('/task/edit/{id}', function($id){
    $data = [
        'id' => $id,
        'description' => 'Uma tarefa X',
    ];
    return view('todolist.edit', $data);
});
```

Essa rota é usada para acessar uma tarefa cujo id será definido pelo parâmetro `{id}` presente na rota. Esta é uma meneira de definir apenas uma rota para acessar o formulário de edicão e acessar qualquer tarefa. Afinal de contas basta passar o id correto no navegador. Por exemplo:

> localhost:8000/task/edit/10

> localhost:8000/task/edit/11

As requisicões acima permitem acessar tarefas diferentes no banco com base no valor passado. No caso, 10 e 11.

Mas como o servidor processa isso? Vamos a um pequeno exercício mental.

O servidor receberá uma requisicão `localhost:8000/task/edit/10` do tipo GET. Ele vai olhar na lista de rotas registradas em `web.php` e vai saber qual a rota que foi solicitada. Em seguida, ele vai pegar o valor 10 que é o `{id}` esperado pela rota. Esse valor será passado para a função definida na rota e ilustrada abaixo:
```php
function($id){
    $data = [
        'id' => $id,
        'description' => 'Uma tarefa X',
    ];
    return view('todolist.edit', $data);
}
```
O id definido em `/task/edit/{id}` é associado ao `$id` da definicão da função. 

Por fim, a função neste caso retorna um template definido na pasta `todolist` e de nome `edit.blade.php` que recebe o array de dados chamado `$data`. Esta é uma simulacão de recuperacão de dados do banco, mas na verdade não é isso que acontece. A view que será montada para edicão dos dados usará as informacões presentes em `$data` conforme ilustrado abaixo:

```html
<!-- trecho de edit.blade.php -->
<form action="{{url('/task/update', ['id'=>$id])}}" method="POST"> 
    <label for="task">Description</label>
    <input type="text" name="task" id="task" value="{{$description}}">
    <button type="submit">Entrar</button>
</form>
```

Na definição acima, temos dois detalhes importantes: a definicão do `action` e o uso de `$id` e `$description`. 

O primeiro detalhe é o uso de $id e $description está relacionado a definição da rota `/task/edit/{id}` onde, no corpo da função, passados os dados com os identificadores `id` e `description`. Isso foi feito para montar o formulário com os dados para edicão (lembre-se que estamos falando de editar um registro).

O segundo detalhe é a informacão do *action*. Note que vamos usar um rota post cujo caminho é '/task/update'. Até aí tudo bem. Mas é o restante da informacão significa o que? significa que vamos mandar o dado $id para a rota `/task/update` para que possamos salvar os novos dados no registro correto com base nesse id. Para fins de esclarecimento, veja o código da rota *update* abaixo:

```php
Route::post('/task/update/{id}', function(Request $request, $id){
    $data = $request->all(['task']);
    return $data;
});
```

O comportamento da rota definida acima é o mesmo que foi explicado sobreo login no início deste capítulo.

### Não esqueçamos da tag @csrf

Esta tag é sempre utilizada nos forms que são enviados com o método POST. Ele é compreendida pelos templates *blade* (que estamos utilizado e logo veremos mais detalhes). Por hora, apenas se comenda o seu uso nos formulários. Ao nos aprofundarmos no entedimento do framework, vamos observar os detalhes por trás da inclusão da tag no código.






