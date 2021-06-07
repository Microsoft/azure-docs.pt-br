---
title: Modelos do Resource Manager para a API de Tabela do Azure Cosmos DB
description: Use modelos do Azure Resource Manager para criar e configurar a API de Tabela do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: e8fb47cc54d520d44085c8d007a4dfe92972d1d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340576"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gerenciar recursos da API de Tabela do Azure Cosmos DB usando modelos do Azure Resource Manager
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Neste artigo, você aprenderá a usar os modelos do Azure Resource Manager para ajudar a implantar e gerenciar contas, bancos de dados e contêineres do Azure Cosmos DB.

Este artigo tem exemplos apenas das contas de API de Tabela. Para encontrar exemplos de outras contas de tipo de API, consulte: usar modelos de Azure Resource Manager com a API do Azure Cosmos DB para artigos do [Cassandra](./templates-samples-cassandra.md), [Gremlin](./templates-samples-gremlin.md), [MongoDB](./templates-samples-mongodb.md), [SQL](./manage-with-templates.md).

> [!IMPORTANT]
>
> * Os nomes de conta são limitados a 44 caracteres, tudo em minúsculas.
> * Para alterar os valores de taxa de transferência, reimplante o modelo com RU/s atualizadas.
> * Quando você adiciona ou remove locais de uma conta do Azure Cosmos, não pode modificar outras propriedades simultaneamente. Essas operações devem ser feitas separadamente.

Para criar qualquer um dos recursos do Azure Cosmos DB abaixo, copie o modelo de exemplo a seguir em um novo arquivo JSON. Opcionalmente, você pode criar um arquivo JSON de parâmetros para usar ao implantar várias instâncias do mesmo recurso com nomes e valores diferentes. Há várias maneiras de implantar modelos do Azure Resource Manager, incluindo [portal do Azure](../azure-resource-manager/templates/deploy-portal.md), [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> Para habilitar a taxa de transferência compartilhada ao usar a API de Tabela, habilite a taxa de transferência no nível da conta no portal do Azure.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Conta do Azure Cosmos e de Tabela com taxa de transferência de dimensionamento automático

Este modelo criará uma conta do Azure Cosmos para a API de Tabela com uma tabela com taxa de transferência de dimensionamento automático. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Conta do Azure Cosmos para Tabela com taxa de transferência provisionada padrão

Este modelo criará uma conta do Azure Cosmos para a API de Tabela com uma tabela com taxa de transferência padrão. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

* [Documentação do Azure Resource Manager](../azure-resource-manager/index.yml)
* [Esquema de provedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de início rápido do Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Solução de erros comuns de implantação do Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)