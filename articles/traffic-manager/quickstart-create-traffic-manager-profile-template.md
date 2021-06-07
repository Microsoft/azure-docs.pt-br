---
title: 'Início Rápido: Criar um Gerenciador de Tráfego usando o modelo do ARM (Azure Resource Manager)'
description: Este artigo de início rápido descreve como criar um perfil do Gerenciador de Tráfego do Azure usando um modelo do ARM (Azure Resource Manager).
services: traffic-manager
author: duongau
ms.author: duau
ms.date: 09/01/2020
ms.topic: quickstart
ms.service: traffic-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 03342d33731ed29b60908044cd29d529aaa5677b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531247"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Início Rápido: Criar um perfil do Gerenciador de Tráfego usando um modelo do ARM

Este guia de início rápido descreve como usar um modelo do ARM (Azure Resource Manager) para criar um perfil do Gerenciador de Tráfego com pontos de extremidade externos usando o método de roteamento de desempenho.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

Um recurso do Azure é definido no modelo:

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Para encontrar mais modelos relacionados ao Gerenciador de Tráfego do Azure, confira [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

1. Clique em **Experimentar** no bloco de código a seguir para abrir o Azure Cloud Shell e então acompanhe as instruções para entrar no Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Aguarde até ver o prompt do console.

1. Selecione **Copiar** no bloco de códigos anterior para copiar o script do PowerShell.

1. Clique com o botão direito do mouse no painel do console do shell e selecione **Colar**.

1. Insira os valores.

    A implantação de modelo cria um perfil com dois pontos de extremidade externos. **Endpoint1** usa o ponto de extremidade de destino `www.microsoft.com` com a localização **Norte da Europa**. **Endpoint2** usa o ponto de extremidade de destino `docs.microsoft.com` com a localização no **Centro-Sul dos EUA**.

    O nome do grupo de recursos é o nome do projeto com o acréscimo de **rg**.

    > [!NOTE]
    > **uniqueDNSname** precisa ser um nome global exclusivo para que o modelo seja implantado com êxito. Se a implantação falhar, comece novamente com a Etapa 1.

    O modelo leva alguns minutos para ser implantado. Quando tiver concluído, a saída deverá ser semelhante a:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Saída de implantação do PowerShell do modelo do Resource Manager no Gerenciador de Tráfego do Azure":::

O Azure PowerShell é usado para implantar o modelo. Além do Azure PowerShell, você também pode usar o portal do Azure, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implantação

1. Determine o nome DNS do perfil do Gerenciador de Tráfego usando [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Copie o valor **RelativeDnsName**. O nome DNS do seu perfil do Gerenciador de Tráfego é `<relativednsname>.trafficmanager.net`.

1. Em um PowerShell local, execute o comando a seguir substituindo a variável **{relativeDNSname}** por `<relativednsname>.trafficmanager.net`.

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    Você deve obter um NameHost igual a `www.microsoft.com` ou `docs.microsoft.com`, dependendo de qual região está mais perto de você.

1. Para verificar se você pode resolver o endereço para o outro ponto de extremidade, desabilite o ponto de extremidade no destino obtido na última etapa. Substitua o **{endpointName}** por **endpoint1** ou **endpoint2** para desabilitar o destino para `www.microsoft.com` ou `docs.microsoft.com`, respectivamente.

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. Execute o comando da Etapa 2 novamente em um PowerShell local. Desta vez, você deverá obter o outro NameHost para o outro ponto de extremidade.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais do perfil do Gerenciador de Tráfego, exclua o grupo de recursos. Isso removerá o perfil do Gerenciador de Tráfego e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um perfil do Gerenciador de Tráfego.

Para saber mais sobre o encaminhamento de tráfego, prossiga para os tutoriais do Gerenciador de Tráfego.

> [!div class="nextstepaction"]
> [Tutoriais do Gerenciador de Tráfego](tutorial-traffic-manager-improve-website-response.md)
