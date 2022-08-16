# Configuração de Acesso ao Banco de Dados

Na seção sobre controladores, abordamos o papel do controlador como orquestrador das ações da aplicação. E uma das finalidades do controlador é usar modelos para acessar a camada de dados e obter as informações do banco e também levar informações para banco.

Neste capítulo, vamos explorar o acesso ao banco de dados de acordo com o modelo `MVC`.

O primeiro passo é configurar o acesso ao banco de dados. Para tanto, vamos precisar configurar dois arquivos:
- config/database.php
- .env

As informações presentes nos dois arquivos são, de certa forma, redutantes. O laravel procura as informações de conexão com o banco de dados no arquivo `database.php`. Neste arquivo, tem definições de informações de acesso ao banco que ficam definidas no arquivo `.env`. Antes de vermos as configurações em si, por que temos essa relação dos dois arquivos?

O arquivo `.env` não deve ser compartilhado publicamente, ele não fica no github ou outro repositório de código e, portanto, é o lugar onde podemos ter mais segurança para adicionar, por exemplo, senha de acesso ao banco. As informações necessárias no `database.php` são obtidas do `.env`.

## Usando banco de dados SQLITE

Vamos iniciar as nossas configurações com um banco mais simples de usar: O SQLITE. O banco será um arquivo no disco e ficará armazenado em nosso projeto.

O primeiro passo é criar o arquivo `database.sqlite` na pasta `database/`. O arquivo será armazenado neste local por conveniência. É possível armazená-lo em outro lugar.

O segundo passo é configurar o arquivo database para usar, por padrão, o banco sqlite que criamos. Para tanto, abra o arquivo `database/database.php`. 

```php
//trecho de código do arquivo database.php
<?php
use Illuminate\Support\Str;

return [

    'default' => env('SQLITE_CONNECTION', 'sqlite'),

    'connections' => [

        'sqlite' => [
            'driver' => 'sqlite',
            'url' => env('DATABASE_URL'),
            'database' => env('DB_DATABASE', database_path('database.sqlite')),
            'prefix' => '',
            'foreign_key_constraints' => env('DB_FOREIGN_KEYS', true),
        ],

        'mysql' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],
        
    //aqui tem mais conexão que foram omitidas porque não as utilizaremos

    ],

];
```

No trecho de código acima, temos alguns pontos importantes da relação de `database.php` e `.env`. No arquivo `database.php` sempre que houver o uso da função `env()`, estamos buscando uma informação presente no arquivo. Dito isso, vamos a configuração do SQLite.

Observe a seguinte linha no arquivo database.php ilustrada abaixo:

```php
'default' => env('SQLITE_CONNECTION', 'sqlite'),
```

A declaração `default` indica qual a conexão padrão que nosso projeto utilizará para o banco de dados. Neste caso, temos o uso da função `env`. O que essa declaração significa? Primeiro, que vamos procurar uma variável `SQLITE_CONNECTION` no arquivo `.env` caso ela não seja definida lá, então vamos usar o segundo argumento como sendo a conexão padrão. No exemplo ilsutrado, o argumento é `sqlite`.

O arquivo .env vem com várias definições padrão de configurações do projeto. A parte que interessa para o nosso banco de dados funcionar está destacada abaixo:

```php
#mysql connection
# MYSQL_CONNECTION=mysql
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=romerito

#sqlite connection
SQLITE_CONNECTION=sqlite
```

Observe que o arquivo tem duas configurações e o `#` é um comentário na configuração do MYSql que não estamos utilizando agora. Observe também que as declarações sempre são pares com a chave da configuração e o seu valor.

Por fim, para testar a sua conexão com o banco de dados basta fazer o seguinte procedimento:

```
php artisan tinker
```

O tinker permite que você  acesso um interpretador de comando que permite a você testar coisas da aplicação, digamos, de maneiar isolada. Neste exemplo de configuração do banco, você não precisa abrir uma págian para ver se o a conexão está `OK`. 

Com o tinker aberto, digite a seguinte declaração(código php):

```php
>>> \DB::connection()->getPDO()
```

O resultado esperado está ilustrado a seguir para a conexão utiliando o sqlite:

```
=> PDO {#4506
     inTransaction: false,   
     attributes: {
       CASE: NATURAL,        
       ERRMODE: EXCEPTION,   
       PERSISTENT: false,    
       DRIVER_NAME: "sqlite",
       ORACLE_NULLS: NATURAL,
       CLIENT_VERSION: "3.33.0",
       SERVER_VERSION: "3.33.0",
       STATEMENT_CLASS: [
         "PDOStatement",
       ],
       DEFAULT_FETCH_MODE: BOTH,
     },
   }
```


## Usando banco de dados MYSql

# Migrations

## Criando Migrations

## Comandos Essenciais

# Models

## Criando Modelos

## Relationamentos entre modelos