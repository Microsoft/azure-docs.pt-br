---
title: 'Início Rápido: Criar um circuito do ExpressRoute usando um modelo do ARM (Azure Resource Manager)'
description: Este guia de início rápido mostra como criar um circuito do ExpressRoute usando um modelo do ARM (Azure Resource Manager).
services: expressroute
author: duongau
ms.author: duau
manager: kumud
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 3dc1d5f5ec3dfb004468deb2bec80927c7ec189d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529890"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Início Rápido: Criar um circuito do ExpressRoute com o emparelhamento privado usando um modelo do ARM

Este guia de início rápido descreve como usar um modelo do ARM (Azure Resource Manager) para criar um circuito do ExpressRoute com o emparelhamento privado.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet).

Neste guia de início rápido, você criará um circuito do ExpressRoute com o *Equinix* como o provedor de serviços. O circuito usará um *SKU Premium*, com uma largura de banda igual a *50 Mbps* e a localização de emparelhamento *Washington DC*. O emparelhamento privado será habilitado com as sub-redes primária e secundária *192.168.10.16/30* e *192.168.10.20/30*, respectivamente. Uma rede virtual também será criada junto com um *gateway de HighPerformance do ExpressRoute*.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json":::

Vários recursos do Azure foram definidos no modelo:

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (usado para habilitar o emparelhamento privado no circuito)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (o grupo de segurança de rede é aplicado às sub-redes da rede virtual)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (o IP público é usado pelo gateway do ExpressRoute)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (o gateway do ExpressRoute é usado para vincular a VNet ao circuito)

Para encontrar mais modelos relacionados ao ExpressRoute, confira [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

1. Clique em **Experimentar** no bloco de código a seguir para abrir o Azure Cloud Shell e então acompanhe as instruções para entrar no Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Aguarde até ver o prompt do console.

1. Selecione **Copiar** no bloco de códigos anterior para copiar o script do PowerShell.

1. Clique com o botão direito do mouse no painel do console do shell e selecione **Colar**.

1. Insira os valores.

    O nome do grupo de recursos é o nome do projeto com o acréscimo de **rg**.

    A implantação do modelo leva cerca de 20 minutos. Quando tiver concluído, a saída deverá ser semelhante a:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="Saída de implantação do PowerShell do modelo do Resource Manager no ExpressRoute":::

O Azure PowerShell é usado para implantar o modelo. Além do Azure PowerShell, você também pode usar o portal do Azure, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implantação

1. Entre no [portal do Azure](https://portal.azure.com).

1. Escolha **Grupos de recursos** no painel esquerdo.

1. Selecione o grupo de recursos criado na seção anterior. O nome do grupo de recursos padrão é o nome do projeto com o acréscimo de **rg**.

1. O grupo de recursos deve conter os seguintes recursos vistos aqui:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="Grupo de recursos de implantação do ExpressRoute":::

1. Selecione o circuito **er-ck01** do ExpressRoute para confirmar se o status do circuito é **Habilitado**, o status do provedor é **Não provisionado** e o emparelhamento privado tem o status **Provisionado**.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="Circuito de implantação do ExpressRoute":::

> [!NOTE]
> Você precisará chamar o provedor para concluir o processo de provisionamento para vincular a rede virtual ao circuito.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos criados com o circuito do ExpressRoute, exclua o grupo de recursos. Isso removerá o circuito do ExpressRoute e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou:

* Circuito do ExpressRoute
* Rede Virtual
* Gateway de VPN
* IP público
* grupos de segurança de rede

Para saber como vincular uma rede virtual a um circuito, prossiga para os tutoriais do ExpressRoute.

> [!div class="nextstepaction"]
> [Tutoriais do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
