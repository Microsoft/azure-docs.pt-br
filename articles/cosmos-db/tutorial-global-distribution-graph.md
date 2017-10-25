---
title: "Tutorial de distribuição global do Azure Cosmos DB para a API do Graph | Microsoft Docs"
description: "Saiba como configurar a distribuição global do Azure Cosmos DB usando a API do Graph."
services: cosmos-db
keywords: "distribuição global, graph, gremlin"
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.openlocfilehash: d05295f6b952209e6df88d9015153893fbbb9903
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Como configurar a distribuição global do Azure Cosmos DB usando a API do Graph

Neste artigo, mostraremos como usar o Portal do Azure para configurar a distribuição global do Azure Cosmos DB e, depois, conectar-se usando a API do Graph (visualização).

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a [API do Graph](graph-introduction.md) (visualização)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Conectar-se a uma região preferencial usando a API do Graph com o SDK do .NET

A API do Graph é exposta como uma biblioteca de extensão sobre o SDK do DocumentDB.

Para aproveitar a [distribuição global](distribute-data-globally.md), os aplicativos cliente podem especificar a lista de preferências ordenadas de regiões a serem usadas para executar operações de documento. Isso pode ser feito definindo a política de conexão. Com base na configuração da conta do Azure Cosmos DB, na disponibilidade regional atual e na lista de preferências especificada, o ponto de extremidade mais adequado será escolhido pelo SDK para executar operações de gravação e leitura.

Essa lista de preferências é especificada ao inicializar uma conexão usando os SDKs. Os SDKs aceitam um parâmetro opcional "PreferredLocations", que é uma lista ordenada de regiões do Azure.

* **Gravações**: o SDK enviará automaticamente todas as gravações para a região de gravação atual.
* **Leituras**: todas as leituras serão enviadas para a primeira região disponível na lista PreferredLocations. Se a solicitação falhar, o cliente não fará o envio para a próxima região da lista, e assim por diante. Os SDKs tentarão ler apenas das regiões especificadas em PreferredLocations. Desse modo, se a Conta do CosmosDB estiver disponível em três regiões, por exemplo, mas o cliente especificar apenas duas das regiões de não gravação para PreferredLocations, nenhuma leitura será atendida fora da região de gravação, mesmo no caso de failover.

O aplicativo pode verificar o ponto de extremidade de gravação e o ponto de extremidade de leitura atuais escolhidos pelo SDK marcando duas propriedades, WriteEndpoint e ReadEndpoint, disponíveis no SDK versão 1.8 e superiores. Se a propriedade PreferredLocations não estiver definida, todas as solicitações serão atendidas na região de gravação atual.

### <a name="using-the-sdk"></a>Usar o SDK

Por exemplo, no SDK do .NET, o parâmetro `ConnectionPolicy` para o construtor `DocumentClient` tem uma propriedade chamada `PreferredLocations`. Essa propriedade pode ser definida para uma lista de nomes de região. Os nomes de exibição para [Regiões do Azure][regions] podem ser especificados como parte de `PreferredLocations`.

> [!NOTE]
> As URLs para os pontos de extremidade não devem ser consideradas como constantes de vida longa. O serviço pode atualizá-las a qualquer momento. O SDK lida com essa alteração automaticamente.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

Assim, concluímos este tutorial. Aprenda a gerenciar a consistência de sua conta globalmente replicada lendo [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). E para saber mais sobre como a replicação de banco de dados global funciona no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a API do DocumentDB

Agora você pode prosseguir para o próximo tutorial e aprender a desenvolver localmente usando o emulador local do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

