---
title: Usar o Cache do Azure para Redis com o Rust
description: Neste início rápido, você aprenderá a interagir com o Cache do Azure para Redis usando o Rust.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 17f38d79b75179d7a54ca5ed1d20dff18d0a0363
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121092"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Início rápido: Usar o Cache do Azure para Redis com o Rust

Neste artigo, você aprenderá a usar a [linguagem de programação Rust](https://www.rust-lang.org/) para interagir com o [Cache do Azure para Redis](./cache-overview.md). Demonstraremos exemplos de estruturas de dados Redis comumente usadas, como [String](https://redis.io/topics/data-types-intro#redis-strings), [Hash](https://redis.io/topics/data-types-intro#redis-hashes), [List](https://redis.io/topics/data-types-intro#redis-lists) etc. usando a biblioteca [redis-rs](https://github.com/mitsuhiko/redis-rs) para Redis. Esse cliente expõe APIs de nível alto e baixo e você verá esses dois estilos em ação com a ajuda do código de exemplo apresentado neste artigo.

## <a name="skip-to-the-code-on-github"></a>Pular para o código no GitHub

Se você quiser pular diretamente para o código, confira o [Guia de início rápido do Rust](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install) (versão 1.39 ou posterior)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma instância de Cache do Azure para Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Examinar o código (opcional)

Se você estiver interessado em saber como o código funciona, poderá examinar os trechos de código a seguir. Caso contrário, fique à vontade para pular para [Executar o aplicativo](#run-the-application).

A função `connect` é usada para estabelecer uma conexão com o Cache do Azure para Redis. Ela espera que o nome do host e a senha (Chave de Acesso) sejam passados por meio das variáveis de ambiente `REDIS_HOSTNAME` e `REDIS_PASSWORD` respectivamente. O formato da URL de conexão é `rediss://<username>:<password>@<hostname>` – O Cache do Azure para Redis aceita somente conexões seguras com o [TLS 1.2 como a versão mínima necessária](cache-remove-tls-10-11.md).

A chamada para [redis::Client::open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) executa a validação básica enquanto [get_connection()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) inicia realmente a conexão – o programa será interrompido se a conectividade falhar devido a qualquer motivo, como uma senha incorreta.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

A função `basics` abrange os comandos [SET](https://redis.io/commands/set), [GET](https://redis.io/commands/get) e [INCR](https://redis.io/commands/incr). A API de nível baixo é usada para `SET` e `GET`, que define e recupera o valor de uma chave chamada `foo`. O comando `INCRBY` é executado usando uma API de nível alto, ou seja, [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) incrementa o valor de uma chave (chamada `counter`) por `2` seguido por uma chamada para [get](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) para recuperá-lo.

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

O snippet de código abaixo demonstra a funcionalidade de uma estrutura de dados `HASH` do Redis. [HSET](https://redis.io/commands/hset) é invocado usando a API de nível baixo para armazenar informações (`name`, `version`, `repo`) sobre os drivers do Redis (clientes). Por exemplo, os detalhes do driver do Rust (um que está sendo usado neste código de exemplo!) são capturados na forma de um [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) e passados para a API de nível baixo. Em seguida, são recuperados usando [HGETALL](https://redis.io/commands/hgetall).

`HSET` também pode ser executado usando uma API de nível alto usando [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) que aceita matriz de tuplas. [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) é executado para buscar o valor de um só atributo (o `repo` nesse caso).

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

Na função abaixo, você pode ver como usar uma estrutura de dados `LIST`. [LPUSH](https://redis.io/commands/lpush) é executado (com a API de nível baixo) para adicionar uma entrada à lista e o método [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) de alto nível é usado para recuperá-la da lista. Em seguida, o método [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) é usado para adicionar algumas entradas à lista que são buscadas usando o método [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) de nível baixo.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

Aqui você pode ver algumas das operações `SET`. O método [sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (API de nível alto) é usado para adicionar algumas entradas a um `SET` chamado `users`. [SISMEMBER](https://redis.io/commands/hset) é executado (API de nível baixo) para verificar se `user1` existe. Por fim, [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) é usado para buscar e iterar todas as entradas definidas na forma de um vetor ([Vec<String>](https://doc.rust-lang.org/std/vec/struct.Vec.html)).

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

A função `sorted_set` abaixo demonstra a estrutura de dados do Conjunto Classificado. [ZADD](https://redis.io/commands/zadd) é invocado (com a API de nível baixo) para adicionar uma pontuação de inteiro aleatória para um jogador (`player-1`). Em seguida, o método [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) (API de nível alto) é usado para adicionar mais jogadores (`player-2` a `player-5`) e as respectivas pontuações (geradas aleatoriamente). O número de entradas no conjunto classificado é descoberto usando [ZCARD](https://redis.io/commands/zcard) e é usado como o limite para o comando [ZRANGE](https://redis.io/commands/zrange) (chamado com a API de nível baixo) para listar os jogadores com as pontuações em ordem crescente.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Comece clonando o aplicativo do GitHub.

1. Abra um prompt de comando e crie uma pasta chamada `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

1. Abra uma janela de terminal do Git, como git bash. Use o comando `cd` para alterar para a nova pasta, na qual você clonará o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

1. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>Executar o aplicativo

O aplicativo aceita conectividade e credenciais na forma de variáveis de ambiente. 

1. Busque o **Nome de host** e as **Chaves de Acesso** (disponíveis por meio de Chaves de Acesso) para a instância do Cache do Azure para Redis no [portal do Azure](https://portal.azure.com/).

1. Defina-as para as respectivas variáveis de ambiente:

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Na janela do terminal, mude para a pasta correta. Por exemplo:

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. No terminal, execute o comando a seguir para iniciar o aplicativo.

    ```shell
    cargo run
    ```
    
    Você verá uma saída assim:
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Se você quiser executar uma função específica, comente outras funções na função `main`:
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não pretende mais usar o grupo de recursos do Azure, ou os recursos criados neste início rápido, pode excluí-los para evitar encargos.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o que significa que todos os recursos nesse grupo são permanentemente excluídos. Se você criou sua instância do Cache do Azure para Redis em um grupo de recursos existente que deseja manter, poderá excluir apenas o cache selecionando **Excluir** na página **Visão geral** do cache. 

Para excluir o grupo de recursos e sua instância do Redis do Cache do Azure:

1. No [portal do Azure](https://portal.azure.com), procure e selecione **Grupos de recursos**.
1. Na caixa de texto **Filtrar por nome**, digite o nome do grupo de recursos que contém sua instância de cache e selecione-o nos resultados da pesquisa. 
1. Na página do grupo de recursos, selecione **Excluir grupo de recursos**.
1. Digite o nome do grupo de recursos e selecione **Excluir**.
   
   ![Exclua seu grupo de recursos do Cache do Azure para Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar o driver do Rust para Redis para conectar e executar operações no Cache do Azure para Redis.

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET simples que usa um Cache do Azure para Redis.](./cache-web-app-howto.md)
