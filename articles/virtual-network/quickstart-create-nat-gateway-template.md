---
title: Criar um gateway da NAT – modelo do Resource Manager
titleSuffix: Azure Virtual Network NAT
description: Este guia de início rápido mostra como criar um gateway de NAT usando o modelo do ARM (Azure Resource Manager).
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: ea214cb98e1f75daccf783333a67c6d1b0eacfeb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776597"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Início Rápido: Criar um gateway NAT – Modelo do ARM

Comece a usar o NAT da Rede Virtual usando um modelo do ARM (Azure Resource Manager). Este modelo implanta uma rede virtual, um recurso de gateway da NAT e uma máquina virtual Ubuntu. A máquina virtual Ubuntu é implantada em uma sub-rede associada ao recurso de gateway da NAT.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm).

Esse modelo foi configurado para criar um:

* Rede virtual
* Recurso de gateway da NAT
* Máquina virtual Ubuntu

A VM do Ubuntu é implantada em uma sub-rede associada ao recurso de gateway da NAT.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

Nove recursos do Azure são definidos no modelo:

* **[Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)** : Cria um grupo de segurança de rede.
* **[Microsoft.Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)** : cria uma regra de segurança.
* **[Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)** : Cria um endereço IP público.
* **[Microsoft.Network/publicIPPrefixes](/azure/templates/microsoft.network/publicipprefixes)** : cria um prefixo IP público.
* **[Microsoft.Compute/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)** : Cria uma máquina virtual.
* **[Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)** : Cria uma rede virtual.
* **[Microsoft.Network/natGateways](/azure/templates/microsoft.network/natgateways)** : Cria um recurso de gateway da NAT.
* **[Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets)** : cria uma sub-rede de rede virtual.
* **[Microsoft.Network/networkinterfaces](/azure/templates/microsoft.network/networkinterfaces)** : Cria um adaptador de rede.

## <a name="deploy-the-template"></a>Implantar o modelo

**CLI do Azure**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**PowerShell do Azure**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure portal**

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Entre no [portal do Azure](https://portal.azure.com).

1. Escolha **Grupos de recursos** no painel esquerdo.

1. Selecione o grupo de recursos criado na seção anterior. O nome do grupo de recursos padrão é **myResourceGroupNAT**

1. Verifique se os seguintes recursos foram criados no grupo de recursos:

    ![Grupo de recursos da NAT da Rede Virtual](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Limpar os recursos

**CLI do Azure**

Quando não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos contidos nele.

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**PowerShell do Azure**

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos contidos nele.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure portal**

Quando não forem mais necessários, exclua o grupo de recursos, o gateway da NAT e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupNAT** que contém o gateway da NAT e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou:

* Um recurso de gateway da NAT
* Rede virtual
* Uma máquina virtual Ubuntu

A máquina virtual é implantada em uma sub-rede da rede virtual associada ao gateway da NAT.

Para saber mais sobre a NAT da Rede Virtual e o Azure Resource Manager, prossiga para os artigos abaixo.

* Leia uma [Visão geral da NAT da Rede Virtual](nat-overview.md)
* Leia mais sobre o [Recurso do Gateway da NAT](nat-gateway-resource.md)
* Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
