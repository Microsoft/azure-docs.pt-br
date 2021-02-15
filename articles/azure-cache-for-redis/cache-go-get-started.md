---
title: Usar o Cache do Azure para Redis com Go
description: Neste início rápido, você aprenderá como criar um aplicativo Go com o Cache do Azure para Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: de7bdc22b4bfdf9ef5865b1b601b32672eb868d1
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165120"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Início rápido: Usar o Cache do Azure para Redis com Go

Neste artigo, você aprenderá a criar uma API REST em Go que armazenará e recuperará informações de usuário apoiadas por uma estrutura de dados [HASH](https://redis.io/topics/data-types-intro#redis-hashes) no [Cache do Azure para Redis](./cache-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install) (preferencialmente versão 1.13 ou superior)
- [Git](https://git-scm.com/downloads)
- Um cliente HTTP como [curl](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma instância de Cache do Azure para Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Examinar o código (opcional)

Se você estiver interessado em saber como o código funciona, poderá examinar os trechos de código a seguir. Caso contrário, fique à vontade para pular para [Executar o aplicativo](#run-the-application).

A biblioteca [go-redis](https://github.com/go-redis/redis) de software livre é usada para interagir com o Cache do Azure para Redis.

A função `main` começa lendo o nome do host e a senha (chave de acesso) para a instância do Cache do Azure para Redis.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Em seguida, estabelecemos uma conexão com o Cache do Azure para Redis. Observe que [tls.Config](https://golang.org/pkg/crypto/tls/#Config) está sendo usado – o Cache do Azure para Redis só aceita conexões seguras com o [TLS 1.2 como a versão mínima necessária](cache-remove-tls-10-11.md).

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

Se a conexão for bem-sucedida, [manipuladores HTTP](https://golang.org/pkg/net/http/#HandleFunc) serão configurados para lidar com as operações `POST` e `GET` e o servidor HTTP será iniciado. 

> [!NOTE] 
> A [gorilla mux library](https://github.com/gorilla/mux) é usado para roteamento (embora não seja estritamente necessário e poderíamos ter usado a biblioteca padrão para este aplicativo de exemplo).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

O struct `userHandler` encapsula um [redis.Client](https://pkg.go.dev/github.com/go-redis/redis/v8#Client), que é usado pelos métodos `createUser`, `getUser` – o código para esses métodos não foi incluído para fins de brevidade. 

- `createUser`: aceita conteúdo JSON (contendo as informações do usuário) e salva-o como um `HASH` no Cache do Azure para Redis.
- `getUser`: busca informações do usuário de `HASH` ou retorna uma resposta HTTP `404`, se não for encontrada.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
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
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>Executar o aplicativo

O aplicativo aceita conectividade e credenciais na forma de variáveis de ambiente. 

1. Busque o **Nome de host** e as **Chaves de Acesso** (disponíveis por meio de Chaves de Acesso) para a instância do Cache do Azure para Redis no [portal do Azure](https://portal.azure.com/)

1. Defina-as para as respectivas variáveis de ambiente:

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Na janela do terminal, mude para a pasta correta. Por exemplo:

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. No terminal, execute o comando a seguir para iniciar o aplicativo.

    ```shell
    go run main.go
    ```

O servidor HTTP será iniciado na porta `8080`.

## <a name="test-the-application"></a>Testar o aplicativo

1. Crie algumas entradas de usuário. O exemplo abaixo usa curl:

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Busque um usuário existente com seu `id`:

    ```bash
    curl -i localhost:8080/users/1
    ```

    Você deve obter a resposta JSON como:
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Se você tentar buscar um usuário que não existe, receberá um HTTP `404`. Por exemplo:

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
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

Neste guia de início rápido, você aprendeu a começar a usar o Go com o Cache do Azure para Redis. Você configurou e executou um aplicativo simples baseado na API REST para criar e obter informações do usuário apoiadas por uma estrutura de dados do Redis `HASH`.

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET simples que usa um Cache do Azure para Redis.](./cache-web-app-howto.md)
