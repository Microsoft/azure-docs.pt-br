---
title: 'Início rápido: criar um Servidor de Rota do Azure usando um modelo do ARM (Azure Resource Manager)'
description: Este início rápido mostra como criar um Servidor de Rota do Azure usando um modelo do ARM (Azure Resource Manager).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 3476e5fa2c274f0fc2c180711480375b0ebefaf2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388033"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Início rápido: criar um Servidor de Rota do Azure usando um modelo do ARM

Este início rápido descreve como usar um modelo do ARM (Azure Resource Manager) para implantar um Servidor de Rota do Azure em uma rede virtual nova ou existente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos do ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-route-server).

Neste início rápido, você implantará um Servidor de Rota do Azure em uma rede virtual nova ou existente. Uma sub-rede dedicada chamada `RouteServerSubnet` será criada para hospedar o Servidor de Rota. O Servidor de Rota também será configurado com o Par ASN e o Par IP a fim de estabelecer um emparelhamento via protocolo BGP.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Vários recursos do Azure foram definidos no modelo:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualNetworks/subnets) (duas sub-redes, uma chamada `routeserversubnet`)
* [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualhubs) (implantação do Servidor de Rota)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections) (configuração do Par ASN e do Par IP)


Para encontrar mais modelos relacionados ao ExpressRoute, confira [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

1. Clique em **Experimentar** no bloco de código a seguir para abrir o Azure Cloud Shell e então acompanhe as instruções para entrar no Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Saída de implantação do PowerShell do modelo do Resource Manager no Servidor de Rota.":::

O Azure PowerShell é usado para implantar o modelo. Além do Azure PowerShell, você também pode usar o portal do Azure, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implantação

1. Entre no [portal do Azure](https://portal.azure.com).

1. Escolha **Grupos de recursos** no painel esquerdo.

1. Selecione o grupo de recursos criado na seção anterior. O nome do grupo de recursos padrão é o nome do projeto com o acréscimo de **rg**.

1. O grupo de recursos deve conter apenas a rede virtual:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Grupo de recursos de implantação do Servidor de Rota com rede virtual.":::

1. Ir para https://aka.ms/routeserver.

1. Selecione o Servidor de Rota chamado **routeserver** para verificar se a implantação foi bem-sucedida.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Uma captura de tela da página de visão geral do Servidor de Rota.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais dos recursos criados com o Servidor de Rota, exclua o grupo de recursos. Isso remove o Servidor de Rota e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou:

* Servidor de Rota
* Rede Virtual
* Sub-rede

Depois de criar o Servidor de Rota do Azure, continue a aprender como o Servidor de Rota do Azure interage com os gateways de VPN e ExpressRoute: 

> [!div class="nextstepaction"]
> [Suporte à VPN do Azure e ao Azure ExpressRoute](expressroute-vpn-support.md)
