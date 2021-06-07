---
title: 'Início Rápido: Usar o Cache do Azure para Redis no Java'
description: Neste guia de início rápido você criará um novo aplicativo Java que usa o Cache do Azure para Redis
author: yegu-ms
ms.author: yegu
ms.date: 05/22/2020
ms.topic: quickstart
ms.service: cache
ms.devlang: java
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 7128ff1e20439c57d3e6212f29e6f871997584c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538605"
---
# <a name="quickstart-use-azure-cache-for-redis-in-java"></a>Início Rápido: Usar o Cache do Azure para Redis no Java

Neste guia de início rápido, você incorporará o Cache do Azure para Redis a um aplicativo Java usando o cliente Redis do [Jedis](https://github.com/xetorthio/jedis) para ter acesso a um cache seguro e dedicado que pode ser acessado de qualquer aplicativo no Azure.

## <a name="skip-to-the-code-on-github"></a>Pular para o código no GitHub

Se você quiser pular diretamente para o código, confira o [Guia de início rápido do Java](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/java) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Criar um Cache do Azure para Redis

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

## <a name="setting-up-the-working-environment"></a>Como preparar o ambiente de trabalho 

Dependendo do seu sistema operacional, adicione variáveis de ambiente para o seu **Nome do host** e a **Chave de acesso primária**. Abra um prompt de comando ou uma janela de terminal e configure os seguintes valores:

```CMD 
set REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
set REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

```bash
export REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
export REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

Substitua os espaços reservados pelos seguintes valores:

- `<YOUR_HOST_NAME>`: o nome do host DNS, obtido na seção *Propriedades* do seu recurso do Cache do Azure para Redis no portal do Azure.
- `<YOUR_PRIMARY_ACCESS_KEY>`: a chave de acesso primária, obtida na seção *Chaves de acesso* do seu recurso do Cache do Azure para Redis no portal do Azure.

## <a name="create-a-new-java-app"></a>Criar um novo aplicativo Java

Usando o Maven, gere um novo aplicativo de início rápido:

```CMD
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.3 -DgroupId=example.demo -DartifactId=redistest -Dversion=1.0
```

Altere para o novo diretório do projeto *redistest*.

Abra o arquivo *pom.xml* e adicione uma dependência para [Jedis](https://github.com/xetorthio/jedis):

```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.2.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>
```

Salve o arquivo *pom.xml* .

Abra *App.java* e substitua o código pelo código a seguir:

```java
package example.demo;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

/**
 * Redis test
 *
 */
public class App 
{
    public static void main( String[] args )
    {

        boolean useSsl = true;
        String cacheHostname = System.getenv("REDISCACHEHOSTNAME");
        String cachekey = System.getenv("REDISCACHEKEY");

        // Connect to the Azure Cache for Redis over the TLS/SSL port using the key.
        JedisShardInfo shardInfo = new JedisShardInfo(cacheHostname, 6380, useSsl);
        shardInfo.setPassword(cachekey); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);      

        // Perform cache operations using the cache connection object...

        // Simple PING command        
        System.out.println( "\nCache Command  : Ping" );
        System.out.println( "Cache Response : " + jedis.ping());

        // Simple get and put of integral data types into the cache
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        System.out.println( "\nCache Command  : SET Message" );
        System.out.println( "Cache Response : " + jedis.set("Message", "Hello! The cache is working from Java!"));

        // Demonstrate "SET Message" executed as expected...
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        // Get the client list, useful to see if connection list is growing...
        System.out.println( "\nCache Command  : CLIENT LIST" );
        System.out.println( "Cache Response : " + jedis.clientList());

        jedis.close();
    }
}
```

Este código mostra como se conectar a uma instância do Cache do Azure para Redis usando o nome de host de cache e as principais variáveis de ambiente. O código também armazena e recupera um valor de cadeia de caracteres no cache. Os comandos `PING` e `CLIENT LIST` também são executados. 

Salve *App.java*.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Execute o seguinte comando do Maven para compilar e executar o aplicativo:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

No exemplo abaixo, você pode ver que a chave `Message` já tinha um valor armazenado em cache, que foi definido por meio do Console do Redis no portal do Azure. O aplicativo atualizou esse valor armazenado em cache. O aplicativo também executou os comandos `PING` e `CLIENT LIST`.

![Aplicativo do Cache do Azure para Redis concluído](./media/cache-java-get-started/azure-cache-redis-complete.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar para o próximo tutorial, mantenha os recursos criados neste início rápido e reutilize-os.

Caso contrário, se você não for mais usar o aplicativo de exemplo do início rápido, exclua os recursos do Azure criados neste início rápido para evitar encargos. 

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas respectivas folhas, em vez de excluir o grupo de recursos.
>

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

1. Na caixa de texto **Filtrar por nome**, digite o nome do seu grupo de recursos. As instruções deste artigo usaram um grupo de recursos chamado *TestResources*. Em seu grupo de recursos, na lista de resultados, selecione **...** e então **Excluir grupo de recursos**.

   ![Grupo de recursos do Azure excluído](./media/cache-java-get-started/azure-cache-redis-delete-resource-group.png)

1. Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos para confirmar e selecione **Excluir**.

Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar o Cache do Azure para Redis de um aplicativo Java. Continue para o próximo início rápido usar o Cache do Azure para Redis com um aplicativo Web ASP.NET.

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET que usa um Cache do Azure para Redis.](./cache-web-app-howto.md)
