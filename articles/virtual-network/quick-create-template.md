---
title: 'Início Rápido: Criar uma rede virtual usando um modelo do Resource Manager'
titleSuffix: Azure Virtual Network
description: Saiba como usar um modelo do Resource Manager para criar uma rede virtual do Azure.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: bc0ac1a6e882f4197828bf79c7989c16b2eb16f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217661"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>Início Rápido: Criar uma rede virtual – modelo do Resource Manager

Neste início rápido, você aprenderá a criar uma rede virtual com duas sub-redes usando o modelo do Azure Resource Manager. Uma rede virtual é o bloco de construção fundamental de sua rede privada no Azure. Ela permite que os recursos do Azure, como VMs, comuniquem-se de maneira segura uns com os outros e usando a Internet.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também é possível concluir este início rápido usando o [portal do Azure](quick-create-portal.md), o [Azure PowerShell](quick-create-powershell.md) ou a [CLI do Azure](quick-create-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

Os seguintes recursos do Azure foram definidos no modelo:
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): criar uma rede virtual do Azure.
-  [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets) – criar uma sub-rede.

## <a name="deploy-the-template"></a>Implantar o modelo

Implantar o modelo do Resource Manager no Azure:

1. Selecione **Implantar no Azure** para entrar no Azure e abrir o modelo. O modelo cria uma rede virtual com duas sub-redes.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. No portal, na página **Criar uma Rede Virtual com duas Sub-redes**, digite ou selecione os seguintes valores:
   - **Grupo de recursos**: Selecione **Criar novo**, digite um nome para o grupo de recursos e selecione **OK**.
   - **Nome da Rede Virtual**: digite um nome para a nova rede virtual.
3. Selecione **Examinar + Criar** e, em seguida, selecione **Criar**.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Explore os recursos criados com a rede virtual.

Saiba mais sobre a sintaxe e as propriedades de JSON para uma rede virtual em um modelo em [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos criados com a rede virtual, exclua o grupo de recursos. Isso remove a rede virtual e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você implantou uma rede virtual do Azure com duas sub-redes. Para saber mais sobre as redes virtuais do Azure, prossiga para o tutorial de redes virtuais.

> [!div class="nextstepaction"]
> [Filtrar tráfego de rede](tutorial-filter-network-traffic.md)