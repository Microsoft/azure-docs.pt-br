---
title: 'Início Rápido: Usar o Cache do Azure para Redis no Node.js'
description: Neste guia de início rápido você aprenderá como usar o Cache do Azure para Redis com Node.js e node_redis.
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: e4c58d67668a67eee38a73d46a2a40ca29c1dfd8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121245"
---
# <a name="quickstart-use-azure-cache-for-redis-in-nodejs"></a>Início Rápido: Usar o Cache do Azure para Redis no Node.js

Neste guia de início rápido, você incorporará o Cache do Azure para Redis a um aplicativo Node.js para ter acesso a um cache seguro e dedicado que pode ser acessado de qualquer aplicativo no Azure.

## <a name="skip-to-the-code-on-github"></a>Pular para o código no GitHub

Se você quiser pular diretamente para o código, confira o [Guia de início rápido do Node.js](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/nodejs) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis), que você pode instalar com o comando `npm install redis`. 

Para exemplos de uso de outros clientes do Node.js, veja a documentação individual para os clientes do Node.js listado em [clientes do Redis Node.js](https://redis.io/clients#nodejs).

## <a name="create-a-cache"></a>Criar um cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Adicionar variáveis de ambiente para o seu **NOME DO HOST** e a chave de acesso **Primária**. Você usará essas variáveis no seu código em vez de incluir as informações confidenciais diretamente no seu código.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>Conectar-se ao cache

Os últimos builds do [node_redis](https://github.com/mranney/node_redis) dão suporte para se conectar ao Cache do Azure para Redis usando TLS. O exemplo a seguir mostra como se conectar ao Cache do Azure para Redis usando o ponto de extremidade TLS 6380. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Não crie uma conexão para cada operação em seu código. Em vez disso, reutilize conexões sempre que possível. 

## <a name="create-a-new-nodejs-app"></a>Criar um novo aplicativo Node.js

Criar um novo arquivo de script chamado *redistest.js*. Use o comando `npm install redis bluebird` para instalar os pacotes necessários.

Adicione o exemplo JavaScript a seguir ao arquivo. Este código mostra como se conectar a uma instância do Cache do Azure para Redis usando o nome de host de cache e as principais variáveis de ambiente. O código também armazena e recupera um valor de cadeia de caracteres no cache. Os comandos `PING` e `CLIENT LIST` também são executados. Para obter mais exemplos de como usar Redis com o cliente [node_redis](https://github.com/mranney/node_redis), veja [https://redis.js.org/](https://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the TLS port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Execute o script com Node.js.

```
node redistest.js
```

No exemplo abaixo, você pode ver que a chave `Message` já tinha um valor armazenado em cache, que foi definido por meio do Console do Redis no portal do Azure. O aplicativo atualizou esse valor armazenado em cache. O aplicativo também executou os comandos `PING` e `CLIENT LIST`.

![Aplicativo do Cache Redis concluído](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar para o próximo tutorial, mantenha os recursos criados neste início rápido e reutilize-os.

Caso contrário, se você não for mais usar o aplicativo de exemplo do início rápido, exclua os recursos do Azure criados neste início rápido para evitar encargos. 

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas respectivas folhas, em vez de excluir o grupo de recursos.
>

Entre no [portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

Na caixa de texto **Filtrar por nome**, digite o nome do seu grupo de recursos. As instruções deste artigo usaram um grupo de recursos chamado *TestResources*. Em seu grupo de recursos, na lista de resultados, selecione **...** e então **Excluir grupo de recursos**.

![Excluir um grupo de recursos do Azure](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Insira o nome do grupo de recursos para confirmar e selecione **Excluir**.

Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar o Cache do Azure para Redis de um aplicativo Node.js. Continue para o próximo início rápido usar o Cache do Azure para Redis com um aplicativo Web ASP.NET.

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET que usa um Cache do Azure para Redis.](./cache-web-app-howto.md)
