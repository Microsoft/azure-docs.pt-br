---
title: 'Início Rápido: configurar a alta disponibilidade com o Azure Front Door – Azure PowerShell'
description: Este início rápido mostrará como usar o Azure Front Door para criar um aplicativo Web global de alta disponibilidade e de alto desempenho usando o Azure PowerShell.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: a3ecb8cacd8fa47709432e26243bd754511658d2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057908"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Início Rápido: criar um Front Door para um aplicativo Web global altamente disponível usando o Azure PowerShell

Comece a usar o Azure Front Door empregando o Azure PowerShell para criar um aplicativo Web global de alta disponibilidade e de alto desempenho.

O Front Door direciona o tráfego da Web para recursos específicos em um pool de back-end. Você define o domínio de front-end, adiciona recursos a um pool de back-end e cria uma regra de roteamento. Este artigo usa uma configuração simples de um pool de back-end com dois recursos de aplicativo Web e apenas uma regra de roteamento usando o caminho padrão correspondente a "/*".

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Criar grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo.

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Criar duas instâncias de um aplicativo Web

Este início rápido requer duas instâncias de um aplicativo Web em execução em diferentes regiões do Azure. Ambas as instâncias do aplicativo Web são executadas no modo ativo/ativo, portanto, qualquer uma pode aceitar tráfego. Essa configuração difere da configuração Ativo/Em Espera, em que uma atua como failover.

Se você ainda não tiver um aplicativo Web, use o script a seguir para configurar dois aplicativos Web de exemplo.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>Criar um Front Door

Esta seção fornece detalhes sobre como criar e configurar os seguintes componentes do Front Door:
    
* Um objeto de front-end contém o domínio padrão do Front Door.
* Um pool de back-end é um conjunto de back-ends equivalentes em que o Front Door balanceia a carga das solicitações de cliente.
* Uma regra de roteamento mapeia o host de front-end e o padrão de caminho da URL correspondente para um pool de back-end específico.

### <a name="create-a-frontend-object"></a>Criar um objeto de front-end

O objeto de front-end configura o nome do host para o Front Door. Por padrão, o nome do host terá o sufixo * *.azurefd.net*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Criar o pool de back-end

O pool de back-end consiste em dois aplicativos Web criados no início deste início rápido. As configurações de investigação de integridade e balanceamento de carga definidas nesta etapa usam valores padrão.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Criar uma regra de roteamento

A regra de roteamento mapeia o pool de back-end para o domínio de front-end e define o valor de correspondência do caminho padrão como "/*".

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Criar o Front Door

Agora que você criou os objetos necessários, crie o Front Door:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

Depois que a implantação for bem-sucedida, você poderá testá-la seguindo as etapas da próxima seção.

## <a name="test-the-front-door"></a>Testar o Front Door

Execute os comandos a seguir para obter o nome do host do Front Door.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Abra um navegador da Web e insira o nome do host obtido por meio dos comandos. O Front Door direcionará sua solicitação para um dos recursos de back-end. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Página de teste do Front Door":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos criados com o Front Door, exclua o grupo de recursos. Ao excluir o grupo de recursos, você exclui também o Front Door e todos os recursos relacionados. 

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou:
* Front Door
* Dois aplicativos Web

Para saber como adicionar um domínio personalizado ao Front Door, avance para os tutoriais do Front Door.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](front-door-custom-domain.md)
