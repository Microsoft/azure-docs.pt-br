---
title: Tutorial de distribuição global do Azure Cosmos DB para a API de Tabela
description: Saiba como a distribuição global funciona em contas da API de Tabela do Azure Cosmos DB e como configurar a lista de regiões preferida
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: f06fbe482327d01917f648ccdec34baa15c46e80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073903"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurar a distribuição global do Azure Cosmos DB usando a API de Tabela
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a [API de Tabela](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Conectar-se a uma região preferencial usando a API de Tabela

Para aproveitar a [distribuição global](distribute-data-globally.md), os aplicativos cliente devem especificar a localização atual em que o aplicativo deles está em execução. Isso é feito definindo a propriedade `CosmosExecutorConfiguration.CurrentRegion`. A propriedade `CurrentRegion` deve conter uma localização. Cada instância de cliente pode especificar a própria região para leituras de baixa latência. A região deve ser nomeada usando os [nomes de exibição](/previous-versions/azure/reference/gg441293(v=azure.100)) como "Oeste dos EUA". 

O SDK da API de Tabela do Azure Cosmos DB escolhe automaticamente o melhor ponto de extremidade para se comunicar com base na configuração da conta e na disponibilidade regional atual. Ele prioriza a região mais próxima para fornecer uma melhor latência aos clientes. Após definir a propriedade `CurrentRegion` atual, as solicitações de leitura e gravação são direcionadas da seguinte maneira:

* **Solicitações de leitura:** todas as solicitações de leitura são enviadas para o `CurrentRegion` configurado. Com base na proximidade, o SDK seleciona automaticamente uma região com replicação geográfica de fallback para alta disponibilidade.

* **Solicitações de gravação:** o SDK envia automaticamente todas as solicitações de gravação para a região de gravação atual. Em uma conta de gravação em várias regiões, a região atual também atenderá às solicitações de gravação. Com base na proximidade, o SDK seleciona automaticamente uma região com replicação geográfica de fallback para alta disponibilidade.

Se você não especificar a propriedade `CurrentRegion`, o SDK usará a região de gravação atual para todas as operações.

Por exemplo, se uma conta do Azure Cosmos estiver nas regiões "Oeste dos EUA" e "Leste dos EUA". Se "Oeste dos EUA" for a região de gravação e o aplicativo estiver presente no "Leste dos EUA". Se a propriedade CurrentRegion não estiver configurada, todas as solicitações de leitura e gravação serão sempre direcionadas à região "Oeste dos EUA". Se a propriedade CurrentRegion estiver configurada, todas as solicitações de leitura serão atendidas da região "Leste dos EUA".

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a API de Tabela Azure Cosmos DB