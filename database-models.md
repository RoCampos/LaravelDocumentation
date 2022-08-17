# Configuração de Acesso ao Banco de Dados

Na capítulo sobre controladores, abordamos o seu papel de orquestrador das ações da aplicação. Uma das finalidades do controlador é usar modelos para acessar a camada de dados e obter e levar as informações ao banco de dados.

Agora vamos explorar o acesso ao banco de dados de acordo com o modelo `MVC` e os recursos do Laravel.

O primeiro passo é configurar o acesso ao banco de dados. Para tanto, vamos precisar configurar dois arquivos:
- config/database.php
- .env

O arquivo `config/database.php` é utilizado exclusivamente para guardar as configurações a respeito do banco de dados da aplicação. Já o arquivo `.env` guarda informações de configurações de modo geral, inclusive configurações do banco. As informações presentes no arquivo .env a respeito do banco de dados também estão presentes no arquivo. Antes de vermos as configurações em si, por que temos essa relação dos dois arquivos?

O arquivo `.env` não deve ser compartilhado publicamente, ele não fica no github ou outro repositório de código e, portanto, é o lugar onde podemos ter mais segurança para adicionar, por exemplo, senha de acesso ao banco. As informações necessárias no `database.php` pode ser definidas apenas do `.env`. Desse modo, não sendo necessário definí-las no database.php.

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

Na seção anterior, vimos como conectar nossa aplicação com o banco de dados SQLITE. Agora vamos ver como fazer o mesmo com o `MySQL`. O primeiro passo é ter o MySQL instalado em sua máquina. Neste documento, será considerado o ambiente local de desenvolvimento e isso é importante na configuração da conexão com banco de dados.

Após a instalação do MySQL, você deve criar o esquema de banco de dados da sua aplicação. Não precisa criar as tabelas em si e seus relacionamentos neste momento. Na próxima seção, vamos falar sobre as tabelas. Para criar o esquema de banco de dados do exemplo que estamos vendo, utilizarei o seguinte comando no MySQL:

```SQL
create database todolistapp;
```

Em seguida, vamos configurar o arquivo `.env` da seguinte forma:

```bash
# mysql connection
MYSQL_CONNECTION=mysql
DB_HOST=127.0.0.1 
DB_PORT=3306
DB_DATABASE=todolistapp
DB_USERNAME=root
DB_PASSWORD=romerito
```

Observe que a configuração para o MySQL possui mais informações. Vamos lá:
 - MYSQL_CONNECTION: indica o nome da conexão que vamos utilizar que é `mysql`. 
 - DB_HOST: indica o host do banco, ou seja, o lugar onde o banco está instalado. Neste exemplo, é o mesmo da aplicação.
 - DB_PORT: é a porta utilizada para comunicação com banco e está definida com a porta padrão do MySQL.
 - DB_DATABASE: indica o nome do esquema de banco de dados que você tem no MySQL - seu banco.
 - DB_USERNAME: nome do usuário que tem acesso ao banco para realizar operações. No exemplo, o usuário root padrão da instalação.
 - DB_PASSWORD: senha do usuário para acesso ao banco.

 Todas essas informações também podem ser inseridas no arquivo `database.php`. Entretanto, lembre-se que este arquivo por padrão fica disponível em repositórios caso você adicione seu projeto em dos repostórios disponíveis como Github.

 Mas como o Laravel obtém esses dados para conectar a aplicação com o banco e dados se elas não devem ficar no `database.php`? A Resposta é simples, da mesma forma que fez com o SQLITE. Veja o trecho do código de configuração do MySQL no arquivo `database.php`:

 ```php
 //trecho do arquivo database.php com configuração do mysql
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
 ```

 Nas configurações acima, observe o uso da funçao `env()` para obter os valores de configuração do banco que foram adicionados ao arquivov `.env`.

 Para testar se a conexão com MySQL e seu banco estão satisfeitas, use o mesmo procedimento indicado no teste do SQLITE.

# Migrations

## Criando Migrations

## Comandos Essenciais

# Models

## Criando Modelos

## Relationamentos entre modelos