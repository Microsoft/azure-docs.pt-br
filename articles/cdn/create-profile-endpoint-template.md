---
title: 'Início Rápido: Criar um perfil e um ponto de extremidade – modelo do Resource Manager'
titleSuffix: Azure Content Delivery Network
description: Saiba como criar um perfil e um ponto de extremidade da Rede de Distribuição de Conteúdo do Azure usando um modelo do Resource Manager
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: b215dfff3bf62b67b89aa260b0134381de6edeae
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695715"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Início Rápido: Criar um perfil e um ponto de extremidade da CDN do Azure – modelo do Resource Manager

Introdução à CDN (Rede de Distribuição de Conteúdo) do Azure usando um modelo do Azure Resource Manager. O modelo implanta um perfil e um ponto de extremidade.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Esse modelo foi configurado para criar um:

* Perfil
* Ponto de extremidade

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

Um recurso do Azure é definido no modelo:

* **[Microsoft.Cdn/profiles](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>Implantar o modelo

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portal

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Entre no [portal do Azure](https://portal.azure.com).

2. Escolha **Grupos de recursos** no painel esquerdo.

3. Selecione o grupo de recursos criado na seção anterior. O nome do grupo de recursos padrão é **myResourceGroupCDN**

4. Verifique se os seguintes recursos foram criados no grupo de recursos:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Grupo de recursos da CDN do Azure" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="azure-cli"></a>CLI do Azure

Quando não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos nele.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=latest) para remover o grupo de recursos e todos os recursos contidos nele.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portal

Quando o grupo de recursos, o perfil da CDN e todos os recursos relacionados não forem mais necessários, exclua-os. Selecione o grupo de recursos **myResourceGroupCDN** que contém o perfil e o ponto de extremidade da CDN e escolha **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou:

* Perfil da CDN
* Ponto de extremidade

Para saber mais sobre a CDN do Azure e o Azure Resource Manager, prossiga para os artigos abaixo.

* Leia uma [Visão geral da CDN do Azure](cdn-overview.md)
* Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
