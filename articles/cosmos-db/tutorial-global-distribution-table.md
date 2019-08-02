---
title: Tutorial de distribuição global do Azure Cosmos DB para a API de Tabela
description: Saiba como configurar a distribuição global do Azure Cosmos DB usando a API de Tabela.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/13/2018
ms.reviewer: sngun
ms.openlocfilehash: 8562d37d81ce02e150e6ad1cc2a440cf7bb1e5e3
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693353"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurar a distribuição global do Azure Cosmos DB usando a API de Tabela

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a [API de Tabela](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Conectar-se a uma região preferencial usando a API de Tabela

Para aproveitar a [distribuição global](distribute-data-globally.md), os aplicativos cliente podem especificar a lista de preferências ordenadas de regiões a serem usadas para executar operações de documento. Isso pode ser feito configurando a propriedade [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet). O SDK da API de Tabela do Azure Cosmos DB escolhe o melhor ponto de extremidade para se comunicar com base na configuração da conta, disponibilidade regional atual e na lista de preferência fornecida.

O PreferredLocations deve conter uma lista separada por vírgulas de locais (hospedagem múltipla) preferenciais para leituras. Cada instância do cliente pode especificar um subconjunto dessas regiões na ordem preferida para leituras de baixa latência. As regiões devem ser nomeadas usando seus [nomes de exibição](https://msdn.microsoft.com/library/azure/gg441293.aspx), por exemplo, `West US`.

Todas as leituras são enviadas para a primeira região disponível na lista PreferredLocations. Se a solicitação falhar, o cliente não fará o envio para a próxima região da lista, e assim por diante.

Os SDKs tentam fazer a leitura de regiões especificadas na lista PreferredLocations. Desse modo, se a Conta do Banco de Dados estiver disponível em três regiões, por exemplo, mas o cliente especificar apenas duas das regiões de não gravação para PreferredLocations, nenhuma leitura será atendida fora da região de gravação, mesmo no caso de failover.

O SDK envia automaticamente todas as gravações para a região de gravação atual.

Se a propriedade PreferredLocations não estiver definida, todas as solicitações serão atendidas na região de gravação atual.

Assim, concluímos este tutorial. Aprenda a gerenciar a consistência de sua conta globalmente replicada lendo [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). E para saber mais sobre como a replicação de banco de dados global funciona no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a API de Tabela Azure Cosmos DB

