---
title: Criar um pool de SQL dedicado (antigo SQL DW) usando o modelo do Azure Resource Manager
description: Aprenda a criar um pool de SQL do Azure Synapse Analytics usando o modelo do Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.author: jrasnick
ms.date: 06/09/2020
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 73f4baf1e48b5495d513fbabe66763538c8cca57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568464"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Início rápido: Criar um pool de SQL dedicado do Azure Synapse Analytics (antigo SQL DW) usando um modelo do ARM

Este modelo do ARM (Azure Resource Manager) criará um pool de SQL dedicado (antigo SQL DW) com a Transparent Data Encryption habilitada. O pool de SQL dedicado (antigo SQL DW) refere-se aos recursos empresariais de data warehouse que estão em disponibilidade geral no Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

O modelo define um recurso:

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir o modelo. Este modelo cria um pool de SQL dedicado (antigo SQL DW).
   
   [![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Insira ou atualize os seguintes valores:

   * **Assinatura**: Selecione uma assinatura do Azure.
   * **Grupo de recursos**: Selecione **Criar** e insira um nome exclusivo para o grupo de recursos e selecione **OK**. Um novo grupo de recursos facilitará a limpeza de recursos.
   * **Região**: Selecione uma região.  Por exemplo, **Centro dos EUA**.
   * **Nome do SQL Server**: aceite o nome padrão ou insira um nome para o SQL Server.
   * **Logon de Administrador**: insira o nome de usuário do administrador do SQL Server.
   * **Senha do administrador do SQL**: insira a senha de administrador para o SQL Server.
   * **Nome do Data Warehouse**: insira um nome do pool de SQL dedicado.
   * **Transparent Data Encryption**: aceito o padrão, habilitado. 
   * **Objetivo de nível de serviço**: aceite o padrão, DW400c.
   * **Localização**: aceite o local padrão do grupo de recursos.
   * **Examinar e criar**: selecione.
   * **Criar**: selecione.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Você pode usar o portal do Azure para verificar os recursos implantados ou usar o script da CLI do Azure ou do Azure PowerShell para listar os recursos implantados.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais dele, exclua o grupo de recursos usando a CLI do Azure ou o Azure PowerShell:

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

Neste início rápido, você criou um pool de SQL dedicado (antigo SQL DW) usando um modelo do ARM e validou a implantação. Para saber mais sobre o Azure Synapse Analytics e o Azure Resource Manager, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Criar e implantar seu primeiro modelo do ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
