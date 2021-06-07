---
title: Criar e gerenciar Azure Cosmos DB com modelos do Resource Manager
description: Use modelos do Azure Resource Manager para criar e configurar o Azure Cosmos DB para API do Core (SQL).
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: dd70e196180c68d6a498d147493411e6d1703e59
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034037"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Gerenciar recursos da API do Core (SQL) do Azure Cosmos DB com modelos do Azure Resource Manager

[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Neste artigo, você aprenderá a usar os modelos do Azure Resource Manager para ajudar a implantar e gerenciar contas, bancos de dados e contêineres do Azure Cosmos DB.

Este artigo mostra apenas exemplos de modelo do Azure Resource Manager para contas da API do Core (SQL). Você também pode encontrar exemplos de modelo para APIs de [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md) e [Tabela](templates-samples-table.md).

> [!IMPORTANT]
>
> * Os nomes de conta são limitados a 44 caracteres, tudo em minúsculas.
> * Para alterar os valores de taxa de transferência, reimplante o modelo com RU/s atualizadas.
> * Quando você adiciona ou remove locais de uma conta do Azure Cosmos, não pode modificar outras propriedades simultaneamente. Essas operações devem ser feitas separadamente.
> * Azure Cosmos DB recursos não podem ser renomeados, pois isso viola como o Azure Resource Manager funciona com URIs de recursos.

Para criar qualquer um dos recursos do Azure Cosmos DB abaixo, copie o modelo de exemplo a seguir em um novo arquivo JSON. Opcionalmente, você pode criar um arquivo JSON de parâmetros para usar ao implantar várias instâncias do mesmo recurso com nomes e valores diferentes. Há várias maneiras de implantar modelos do Azure Resource Manager, incluindo [portal do Azure](../azure-resource-manager/templates/deploy-portal.md), [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Conta do Azure Cosmos com taxa de transferência de dimensionamento automático

Este modelo cria uma conta do Azure Cosmos em duas regiões, com opções de consistência e failover, com banco de dados e contêiner configurados para taxa de transferência de dimensionamento automático com a maioria das opções de política habilitadas. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Conta do Azure Cosmos com repositório analítico

Este modelo cria uma conta do Azure Cosmos em uma região com um contêiner com TTL analítico habilitado e opções taxa de transferência manual ou de dimensionamento automático. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Conta do Azure Cosmos com taxa de transferência provisionada padrão

Este modelo cria uma conta do Azure Cosmos em duas regiões, com opções de consistência e failover, com banco de dados e contêiner configurados para taxa de transferência padrão com a maioria das opções de política habilitadas. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Contêiner do Azure Cosmos DB com funcionalidade no servidor

Este modelo cria uma conta do Azure Cosmos, banco de dados e contêiner com procedimento armazenado, gatilho e função definida pelo usuário. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Azure Cosmos DB conta com o Azure AD e o RBAC

Este modelo criará uma conta do SQL Cosmos, uma definição de função mantida nativamente e uma atribuição de função mantida nativamente para uma identidade do AAD. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-rbac%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-rbac/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Conta de camada gratuita do Azure Cosmos DB

Este modelo cria uma conta de camada gratuita do Azure Cosmos e um banco de dados com taxa de transferência compartilhada que pode ser compartilhada com até 25 contêineres. Este modelo também está disponível para implantação de um clique na galeria de modelos de início rápido do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

* [Documentação do Azure Resource Manager](../azure-resource-manager/index.yml)
* [Esquema de provedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de início rápido do Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Solução de erros comuns de implantação do Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)