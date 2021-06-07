---
title: 'Tutorial: Criar aplicativos móveis com o Xamarin e o Azure Cosmos DB'
description: 'Tutorial: Um tutorial que cria um aplicativo iOS, Android ou Forms do Xamarin usando o Azure Cosmos DB. O Azure Cosmos DB é um banco de dados na nuvem para aplicativos móveis rápido e em escala mundial.'
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: d1164c14b406fb66638e6a5333fe38180272aa14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359317"
---
# <a name="tutorial-build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Tutorial: Criar aplicativos móveis com o Xamarin e o Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

A maioria dos aplicativos móveis precisa armazenar dados na nuvem e o Azure Cosmos DB é um banco de dados na nuvem para aplicativos móveis. Ele tem tudo o que um desenvolvedor móvel precisa. Ele é um banco de dados como serviço totalmente gerenciado que é dimensionado sob demanda. Ele pode trazer seus dados para seu aplicativo de modo transparente, independentemente de onde os usuários estejam localizados em todo o mundo. Usando o [SDK do .NET Core no Azure Cosmos DB](sql-api-sdk-dotnet-core.md), você pode permitir que aplicativos móveis do Xamarin interajam diretamente com o Azure Cosmos DB, sem uma camada intermediária.

Este artigo fornece um tutorial para a criação de aplicativos móveis com o Xamarin e o Azure Cosmos DB. Encontre o código-fonte completo para o tutorial em [Xamarin e Azure Cosmos DB no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin), incluindo como gerenciar usuários e permissões.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Funcionalidades do Azure Cosmos DB para aplicativos móveis
O Azure Cosmos DB fornece as seguintes funcionalidades principais para desenvolvedores de aplicativos móveis:

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-for-mobile.png" alt-text="Funcionalidades do Azure Cosmos DB para aplicativos móveis":::

* Consultas avançados sobre o repositório de dados. O Azure Cosmos DB armazena dados como documentos JSON sem esquemas em coleções heterogêneas. Ele oferece [consultas rápidas e avançadas](./sql-query-getting-started.md) sem a necessidade de se preocupar com esquemas ou índices.
* Taxa de transferência rápida. É necessário apenas alguns milissegundos para ler e gravar documentos com o Azure Cosmos DB. Desenvolvedores podem especificar a taxa de transferências que eles precisam, e o Azure Cosmos DB honra isso com 99,99% de SLA de disponibilidade para todas as contas de região única e todas as contas de várias regiões com consistências amena e 99,999% de leitura disponível em todas as contas de banco de dados de várias regiões.
* Escala ilimitada. Seus contêineres do Azure Cosmos [aumentam conforme seu aplicativo cresce](partitioning-overview.md). Você pode iniciar com tamanho de dados pequeno e taxa de transferência de centenas de solicitações por segundo. As coleções ou banco de dados podem crescer até petabytes de dados e uma taxa de transferência arbitrariamente grande com centenas de milhões de solicitações por segundo.
* Distribuído globalmente. Os usuários de aplicativos móveis costumam ser em outro lugar, em todo o mundo. O Azure Cosmos DB é um [banco de dados distribuído globalmente](distribute-data-globally.md). Clique no mapa para disponibilizar os dados para seus usuários.
* Autorização interna avançada. Com o Azure Cosmos DB, você pode implementar facilmente padrões populares como [dados por usuário](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems) ou dados compartilhados por vários usuários, sem códigos de autorização personalizados complexos.
* Consultas geoespaciais. Hoje, muitos aplicativos móveis oferecem experiências geográficas contextuais. Com o suporte de primeira classe para [tipos geoespaciais](./sql-query-geospatial-intro.md), o Azure Cosmos DB facilita a criação dessas experiências.
* Anexos binários. Os dados de aplicativo geralmente incluem blobs binários. O suporte nativo para anexos facilita o uso do Azure Cosmos DB como serviço único para seus dados de aplicativo.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Tutorial do Xamarin e do Azure Cosmos DB
O tutorial a seguir mostra como criar um aplicativo móvel usando o Xamarin e o Azure Cosmos DB. Encontre o código-fonte completo para o tutorial em [Xamarin e Azure Cosmos DB no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin).

### <a name="get-started"></a>Introdução
É fácil começar a usar o Azure Cosmos DB. Acesse o portal do Azure e crie uma nova conta do Azure Cosmos DB. Clique na guia **Início rápido**. Baixe a amostra de lista de tarefas pendentes do Xamarin Forms que já está conectada à sua conta do Azure Cosmos DB. 

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-quickstart.png" alt-text="Início rápido do Azure Cosmos DB para aplicativos móveis":::

Ou se você tiver um aplicativo do Xamarin existente, adicione o [pacote NuGet do Azure Cosmos DB](sql-api-sdk-dotnet-core.md). O Azure Cosmos DB dá suporte às bibliotecas compartilhadas do Xamarin.iOS, Xamarin.Android e Xamarin Forms.

### <a name="work-with-data"></a>Trabalhar com dados
Seus registros de dados são armazenados no Azure Cosmos DB como documentos JSON sem esquemas em coleções heterogêneas. Você pode armazenar documentos com estruturas diferentes na mesma coleção:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

No seus projetos do Xamarin, você pode usar consultas integradas à linguagem para dados sem esquema:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Adicionar usuários
Assim como em muitas amostras de introdução, a amostra do Azure Cosmos DB que você baixou faz a autenticação no serviço usando a chave primária codificada no código do aplicativo. Esse padrão não é uma boa prática para um aplicativo que você pretende executar em qualquer lugar, exceto o emulador local. Se um usuário não autorizado obtiver a chave primária, todos os dados de sua conta do Azure Cosmos DB poderão estar comprometidos. Em vez disso, você quer que seu aplicativo acesse somente os registros para o usuário conectado. O Azure Cosmos DB permite aos desenvolvedores conceder permissão de leitura/gravação ou de leitura de aplicativo a uma coleção, a um conjunto de documentos agrupados por uma chave de partição ou a um documento específico. 

Siga estas etapas para modificar o aplicativo de lista de tarefas pendentes para um aplicativo de lista de tarefas pendentes multiusuário: 

  1. Adicione logon ao seu aplicativo usando o Facebook, o Active Directory ou qualquer outro provedor.

  2. Crie uma coleção UserItems do Azure Cosmos DB com **/userId** como uma chave de partição. Especificar a chave de partição para a coleção permite que o Azure Cosmos DB seja dimensionado de forma ilimitada à medida que o número de usuários do aplicativo aumenta, ao mesmo tempo que oferece consultas rápidas.

  3. Adicione o Agente de Token de Recurso do Azure Cosmos DB. Essa API Web simples autentica usuários e emite tokens de curta duração para usuários conectados, com acesso exclusivamente aos documentos na partição do usuário conectado. Neste exemplo, o Agente de Token de Recurso é hospedado no Serviço de Aplicativo.

  4. Modifique o aplicativo para autenticar o recurso Agente de Token com o Facebook e solicite os tokens de recurso para os usuários conectados do Facebook. Você pode os dados deles na coleção UserItems.  

Você pode encontrar um exemplo de código completo desse padrão em [Agente de Token de Recurso no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems). Este diagrama ilustra a solução:

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png" alt-text="Agente de usuários e permissões do Azure Cosmos DB" border="false":::

Se você desejar que dois usuários tenham acesso à mesma lista de tarefas pendentes, poderá adicionar permissões adicionais para o token de acesso no Agente de Token de Recurso.

### <a name="scale-on-demand"></a>Dimensionar sob demanda
O Azure Cosmos DB é um banco de dados como serviço gerenciado. Conforme cresce a sua base de usuários, você não precisa se preocupar sobre o provisionamento de máquinas virtuais ou aumentando núcleos. Basta informar ao Azure Cosmos DB de quantas operações por segundo (produtividade) seu aplicativo precisa. Você pode especificar a taxa de transferência por meio da guia **Escala** usando uma medida de taxa de transferência chamada RUs (unidades de solicitação) por segundo. Por exemplo, uma operação de leitura em um documento de 1 KB exige uma RU. Você pode também adicionar alertas à métrica **Taxa de transferência** para monitorar o aumento do tráfego e alterar a taxa de transferência programaticamente conforme os alertas são disparados.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png" alt-text="Taxa de transferência de escala do Azure Cosmos DB sob demanda":::

### <a name="go-planet-scale"></a>Escala mundial
À medida que o aplicativo ganhar popularidade, talvez você ganhe usuários em todo o mundo. Ou talvez você queira estar preparado para eventos inesperados. Acesse o portal do Azure e abra sua conta do Azure Cosmos DB. Clique no mapa para fazer com que seus dados sejam replicados continuamente para qualquer número de regiões em todo o mundo. Essa funcionalidade torna os dados disponíveis, onde quer que os usuários estejam. Você também pode adicionar políticas de failover para estar preparado para contingências.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png" alt-text="Escala do Azure Cosmos DB entre regiões geográficas" border="false":::

Parabéns. Você concluiu a solução e tem um aplicativo móvel com o Xamarin e o Azure Cosmos DB. Siga etapas semelhantes para criar aplicativos Cordova usando o SDK do JavaScript do Azure Cosmos DB e aplicativos iOS/Android nativos usando APIs REST do Azure Cosmos DB.

## <a name="next-steps"></a>Próximas etapas
* Exiba o código-fonte do [Xamarin e do Azure Cosmos DB no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin).
* Baixe o [SDK do Azure Cosmos DB .NET Core](sql-api-sdk-dotnet-core.md).
* Encontre mais exemplos de código para [aplicativos .NET](sql-api-dotnet-samples.md).
* Saiba mais sobre as [funcionalidades de consulta avançadas do Azure Cosmos DB](./sql-query-getting-started.md).
* Saiba mais sobre o [suporte geoespacial no Azure Cosmos DB](./sql-query-geospatial-intro.md).