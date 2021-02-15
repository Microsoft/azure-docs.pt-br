---
title: Modelos de Azure Resource Manager para Azure Cosmos DB Core (API do SQL)
description: Use modelos do Azure Resource Manager para criar e configurar o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 52fa5957cdbc3ae73ebc4b36e04582741199414a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340593"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos do Azure Resource Manager para o Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo mostra apenas exemplos de modelo do Azure Resource Manager para contas da API do Core (SQL). Você também pode encontrar exemplos de modelo para APIs de [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md) e [Tabela](templates-samples-table.md).

## <a name="core-sql-api"></a>API do Core (SQL)

|**Modelo**|**Descrição**|
|---|---|
|[Criar conta do Azure Cosmos, banco de dados e contêiner com taxa de transferência de dimensionamento automático](manage-with-templates.md#create-autoscale) | Este modelo cria uma conta de API do Core (SQL) em duas regiões, um banco de dados e um contêiner com taxa de transferência de dimensionamento automático. |
|[Criar conta do Azure Cosmos, banco de dados e contêiner com armazenamento analítico](manage-with-templates.md#create-analytical-store) | Este modelo cria uma conta de API do Core (SQL) em uma região com um contêiner configurado com o TTL analítico habilitado e a opção de usar taxa de transferência manual ou de dimensionamento ou automático. |
|[Criar conta do Azure Cosmos, banco de dados e contêiner com taxa de transferência padrão (manual)](manage-with-templates.md#create-manual) | Este modelo cria uma conta de API do Core (SQL) em duas regiões, um banco de dados e um contêiner com taxa de transferência padrão. |
|[Criar conta do Azure Cosmos, banco de dados e contêiner com procedimento armazenado, gatilho e UDF (função definida pelo usuário)](manage-with-templates.md#create-sproc) | Este modelo cria uma conta de API do Core (SQL) em duas regiões com um procedimento armazenado, um gatilho e uma UDF (função definida pelo usuário) para um contêiner. |
|[Criar um ponto de extremidade privado para uma conta já existente do Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Este modelo cria um ponto de extremidade privado para uma conta já existente da API do Core (SQL) do Azure Cosmos em uma rede virtual existente. |
|[Criar uma conta de camada gratuita do Azure Cosmos](manage-with-templates.md#free-tier) |  Este modelo cria uma conta de API do Core (SQL) do Azure Cosmos DB na camada gratuita. |

Confira a página [Referência do Azure Resource Manager para Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) da documentação de referência.
