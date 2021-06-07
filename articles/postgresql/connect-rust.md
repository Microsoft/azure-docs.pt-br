---
title: 'Início rápido: conectar-se com o Rust – Banco de Dados do Azure para PostgreSQL – Servidor Único'
description: Este início rápido fornece exemplos de código do Rust que você pode usar para se conectar e consultar dados do Banco de Dados do Azure para PostgreSQL – Servidor Único.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505545"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Início Rápido: usar o Rust para se conectar e consultar dados no Banco de Dados do Azure para PostgreSQL – Servidor Único

Neste artigo você aprenderá a usar o [driver do PostgreSQL para Rust](https://github.com/sfackler/rust-postgres) a fim de interagir com o Banco de Dados do Azure para PostgreSQL explorando as operações CRUD (criar, ler, atualizar e excluir) implementadas no código de exemplo. Por fim, você pode executar o aplicativo localmente para vê-lo em ação.

## <a name="prerequisites"></a>Pré-requisitos
Para este início rápido você precisa:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free).
- Uma versão recente do [Rust](https://www.rust-lang.org/tools/install) instalada.
- Um Banco de Dados do Azure para PostgreSQL, servidor único – Crie um usando o [portal do Azure](./quickstart-create-server-database-portal.md) <br/> ou a [CLI do Azure](./quickstart-create-server-database-azure-cli.md).
- Com base em se você está usando o acesso público ou privado, conclua **UMA** das ações abaixo para habilitar a conectividade.

  |Ação| Método de conectividade|Guia de instruções|
  |:--------- |:--------- |:--------- |
  | **Configurar regras de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Configurar Ponto de Extremidade de Serviço** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Configurar link privado** | Privados | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads) instalado.

## <a name="get-database-connection-information"></a>Obter informações da conexão de banco de dados
A conexão com um Banco de Dados do Azure para PostgreSQL requer o nome do servidor totalmente qualificado e as credenciais de logon. Você pode obter essas informações no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), procure e selecione o nome do servidor do Banco de Dados do Azure para PostgreSQL.
1. Na página **Visão Geral** do servidor, copie o **Nome do servidor** totalmente qualificado e o **Nome de usuário do administrador**. O **Nome do servidor** totalmente qualificado sempre está no formato *\<my-server-name>.postgres.database.azure.com* e **Nome do usuário administrador** sempre está no formato *\<my-admin-username>@\<my-server-name>* .

## <a name="review-the-code-optional"></a>Examinar o código (opcional)

Se você estiver interessado em saber como o código funciona, poderá examinar os trechos de código a seguir. Caso contrário, fique à vontade para pular para [Executar o aplicativo](#run-the-application).

### <a name="connect"></a>Conectar

A função `main` começa conectando-se ao Banco de Dados do Azure para PostgreSQL e depende das variáveis de ambiente a seguir para obter as informações de conectividade `POSTGRES_HOST`, `POSTGRES_USER`, `POSTGRES_PASSWORD` e `POSTGRES_DBNAME`. Por padrão, o serviço de banco de dados do PostgreSQL é configurado para exigir conexão `TLS`. Você pode optar por desabilitar a necessidade de `TLS` se o aplicativo cliente não oferecer suporte à conectividade `TLS`. Para obter detalhes, confira [Configurar a conectividade TLS no Banco de Dados do Azure para PostgreSQL – Servidor Ú/nico](./concepts-ssl-connection-security.md).

O aplicativo de exemplo neste artigo usa TLS com a [postgres-openssl crate](https://crates.io/crates/postgres-openssl/). A função [postgres::Client::connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) é usada para iniciar a conexão e o programa é encerrado no caso de falha.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Remover e criar tabela

O aplicativo de exemplo usa uma tabela `inventory` simples para demonstrar as operações CRUD (criar, ler, atualizar e excluir).

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

A função `drop_create_table` inicialmente tenta `DROP` a tabela `inventory` antes de criar outra. Isso facilita o aprendizado/a experimentação, pois você sempre começa com um estado conhecido (limpo). O método [Execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) é usado para operações de criação e remoção. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Inserir dados

`insert_data` adiciona entradas à tabela `inventory`. Ele cria uma [instrução preparada](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) com a função [Prepare](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare). 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Observe também o uso do método [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed), que permite que os tipos de parâmetros de consulta sejam especificados explicitamente.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Por fim, um loop `for` é usado para adicionar `item-3`, `item-4` e `item-5` com uma quantidade gerada aleatoriamente para cada.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Consultar dados

A função `query_data` demonstra como recuperar dados da tabela `inventory`. O método [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) é usado para obter um item por seu `id`. 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

Todas as linhas da tabela de estoque são buscadas usando uma consulta `select * from` com o método de [consulta](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query). As linhas retornadas são iteradas para extrair o valor de cada coluna usando o método [Get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get). 

> [!TIP]
> Observe como `get` torna possível especificar a coluna por seu índice numérico na linha ou por seu nome de coluna.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Atualizar dados

A função `update_date` atualiza aleatoriamente a quantidade de todos os itens. Como a função `insert_data` adicionou `5` linhas, o mesmo é levado em conta no loop `for` – `for n in 1..=5`

> [!TIP]
> Observe que usamos `query` em vez de `execute` porque pretendemos recuperar o `id` e o recém-gerado `quantity` (usando a cláusula de [RETORNO](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Excluir dados

Por fim, a função `delete` demonstra como remover um item da tabela `inventory` pelo `id`. O `id` é escolhido aleatoriamente – é um inteiro aleatório entre `1` e `5` (incluindo `5`), pois a função `insert_data` tinha adicionado `5` linhas para começar. 

> [!TIP]
> Observe que usamos `query` em vez de `execute`, pois pretendemos recuperar as informações sobre o item que acabamos de excluir (usando a [cláusula de RETORNO](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>Executar o aplicativo

1. Para começar, execute o seguinte comando para clonar o repositório de exemplo:

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Defina as variáveis de ambiente necessárias com os valores que você copiou do portal do Azure:

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Para executar o aplicativo, altere para o diretório em que você o clonou e execute `cargo run`:

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    Você deverá ver uma saída semelhante a esta:

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. Para confirmar, você também pode se conectar ao Banco de Dados do Azure para PostgreSQL [usando psql](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) e executar consultas no banco de dados, por exemplo:

    ```sql
    select * from inventory;
    ```

[Está com problemas? Fale conosco](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos usados durante este guia de início rápido, exclua o grupo de recursos usando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Gerenciar o servidor de Banco de Dados do Azure para PostgreSQL usando o portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gerenciar o servidor de Banco de Dados do Azure para PostgreSQL usando a CLI](./how-to-manage-server-cli.md)<br/>

[Não foi possível encontrar o que estava procurando? Fale conosco.](https://aka.ms/postgres-doc-feedback)
