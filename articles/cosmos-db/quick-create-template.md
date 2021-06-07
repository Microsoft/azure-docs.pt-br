---
title: Início Rápido – Criar um Azure Cosmos DB e um contêiner usando um modelo do Azure Resource Manager
description: Início Rápido mostrando como criar um banco de dados do Azure Cosmos e um contêiner usando um modelo do Azure Resource Manager
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: quickstart
ms.date: 06/01/2020
ms.custom: subject-armqs
ms.openlocfilehash: b97702bb84eb38d185fe97ffcc596e374f28fbe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339658"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-an-arm-template"></a>Início Rápido: Criar um Azure Cosmos DB e um contêiner usando um modelo do Resource Manager
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O Azure Cosmos DB é o banco de dados NoSQL rápido da Microsoft com APIs abertas para qualquer escala. É possível usar o Azure Cosmos DB para criar e consultar rapidamente bancos de dados de chave/valor, de documentos e de grafo. Este guia de início rápido tem como foco o processo de implantação de um modelo do Azure Resource Manager para criação de um banco de dados do Azure Cosmos e um contêiner nesse banco de dados. Posteriormente, você poderá armazenar dados nesse contêiner.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure ou uma conta de avaliação gratuita do Azure Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

Há três recursos do Azure definidos no modelo:

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): criar uma conta do Azure Cosmos.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Criar um banco de dados do Azure Cosmos.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Criar um contêiner do Azure Cosmos.

Mais exemplos de modelos do Azure Cosmos DB podem ser encontrados na [galeria de modelos de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria uma conta, um banco de dados e um contêiner do Azure Cosmos.

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

2. Selecione ou insira os seguintes valores.

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="Modelo do Resource Manager, integração do Azure Cosmos DB, portal de implantação":::

    A menos que seja especificado, use os valores padrão para criar os recursos do Azure Cosmos.

    * **Assinatura**: selecione uma assinatura do Azure.
    * **Grupo de recursos**: selecione **Criar novo**, insira um nome exclusivo para o grupo de recursos e, em seguida, clique em **OK**.
    * **Local**: selecione um local.  Por exemplo, **Centro dos EUA**.
    * **Nome da Conta**: o nome da conta do Azure Cosmos. Deve ser globalmente exclusivo.
    * **Localização**: insira uma localização em que você deseja criar sua conta do Azure Cosmos. A conta do Azure Cosmos pode estar na mesma localização que o grupo de recursos.
    * **Região Primária**: a região da réplica primária para a conta do Azure Cosmos.
    * **Região secundária**: a região da réplica secundária para a conta do Azure Cosmos.
    * **Nível de consistência padrão**: O nível de consistência padrão para a conta do Azure Cosmos.
    * **Prefixo máximo de desatualização**: máximo de solicitações obsoletas. Exigido para BoundedStaleness.
    * **Intervalo máximo em segundos**: tempo de retardo máximo. Exigido para BoundedStaleness.
    * **Nome do Banco de Dados**: o nome do banco de dados do Azure Cosmos.
    * **Nome do Contêiner**: o nome do contêiner do Azure Cosmos.
    * **Taxa de transferência**:  a taxa de transferência do contêiner, cujo valor mínimo é de 400 RU/s.
    * **Concordo com os termos e condições declarados acima**: selecione.

3. Selecione **Comprar**. Depois que a conta do Azure Cosmos for implantada com êxito, você receberá uma notificação:

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="Modelo do Resource Manager, integração do Cosmos DB, notificação do portal de implantação":::

O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Validar a implantação

Você pode usar o portal do Azure para verificar a conta do Azure Cosmos, o banco de dados e o contêiner ou usar o script da CLI do Azure ou do Azure PowerShell a seguir para listar o segredo criado.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar trabalhando com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não for mais necessário, exclua o grupo de recursos, o que excluirá a conta do Azure Cosmos e os recursos relacionados. Para excluir o grupo de recursos usando a CLI do Azure ou o Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma conta, um banco de dados e um contêiner do Azure Cosmos usando um modelo do Resource Manager e validou a implantação. Para saber mais sobre o Azure Cosmos DB e o Azure Resource Manager, continue com os artigos abaixo.

- Leia uma [Visão geral do Azure Cosmos DB](introduction.md)
- Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Obtenha outros [Modelos do Azure Resource Manager para o Azure Cosmos DB](./templates-samples-sql.md)