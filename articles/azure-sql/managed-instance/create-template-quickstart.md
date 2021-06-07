---
title: 'Azure Resource Manager: Criar uma Instância Gerenciada do SQL Azure'
description: Saiba como criar uma Instância Gerenciada de SQL do Azure usando um modelo do Azure Resource Manager.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/22/2020
ms.openlocfilehash: badeb850e8d0346347a994f053fb0f64fd01240a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91283318"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>Início Rápido: Criar uma Instância Gerenciada de SQL do Azure usando um modelo do ARM

Este guia de início rápido concentra-se no processo de implantar um modelo do ARM (modelo do Azure Resource Manager) para criar uma Instância Gerenciada de SQL do Azure e uma vNet. A [Instância Gerenciada de SQL do Azure](sql-managed-instance-paas-overview.md) é um banco de dados de nuvem inteligente, totalmente gerenciado e escalonável, com quase 100% de paridade de recursos com o mecanismo de banco de dados SQL Server.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Esses recursos do Azure estão definidos no modelo:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)

Mais exemplos de modelo podem ser encontrados em [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

Selecione **Experimentar** no seguinte bloco de código do PowerShell para abrir o Azure Cloud Shell.

> [!IMPORTANT]
> A implantação de uma instância gerenciada é uma operação de execução longa. A implantação da primeira instância na sub-rede normalmente demora muito mais do que a implantação em uma sub-rede com instâncias gerenciadas existentes. Para obter os tempos de provisionamento médios, confira [Operações de gerenciamento da Instância Gerenciada de SQL](sql-managed-instance-paas-overview.md#management-operations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Acesse o [portal do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) e verifique se a instância gerenciada está no grupo de recursos selecionado. Como a criação de uma instância gerenciada pode levar algum tempo, talvez seja necessário verificar o link **Implantações** na página **Visão Geral** do seu grupo de recursos.

- Para obter um início rápido que mostra como se conectar a uma Instância Gerenciada de SQL em uma máquina virtual do Azure, confira [Configurar uma conexão com uma máquina virtual do Azure](connect-vm-instance-configure.md).
- Para obter um início rápido que mostra como se conectar à Instância Gerenciada de SQL em um computador cliente local usando uma conexão ponto a site, confira [Configurar uma conexão ponto a site](point-to-site-p2s-configure.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Mantenha a instância gerenciada se desejar acessar as [Próximas etapas](#next-steps), mas exclua a instância gerenciada e os recursos relacionados após concluir os tutoriais adicionais. Após excluir uma instância gerenciada, confira [Excluir uma sub-rede depois de excluir uma instância gerenciada](virtual-cluster-delete.md).


Para excluir o grupo de recursos:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar uma VM do Azure para se conectar à Instância Gerenciada de SQL do Azure](connect-vm-instance-configure.md)
