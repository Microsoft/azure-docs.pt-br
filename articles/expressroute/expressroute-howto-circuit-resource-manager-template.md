---
title: 'Modelo do Azure ExpressRoute: criar um circuito do ExpressRoute'
description: Saiba como criar um circuito do Azure ExpressRoute implantando um modelo de Azure Resource Manager usando Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 2e9b6ddc9da4467590946af12a47f1473a4ea494
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202048"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Criar um circuito do ExpressRoute usando Azure Resource Manager modelo

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Saiba como criar um circuito do ExpressRoute implantando um modelo de Azure Resource Manager usando Azure PowerShell. Para obter mais informações sobre como desenvolver modelos do Resource Manager, confira [documentação do Resource Manager](../azure-resource-manager/index.yml) e a [referência de modelo](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Antes de começar

* Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Verifique se você tem permissões para criar novos recursos de rede. Se você não tiver as permissões corretas, entre em contato com o administrador da conta.
* Você pode [exibir um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar para entender melhor as etapas.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e provisionar um circuito do ExpressRoute

Os [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) têm uma boa coleção do modelo do Resource Manager. Você usa um dos [modelos existentes](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) para criar um circuito do ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Para ver mais modelos relacionados, selecione [aqui](https://azure.microsoft.com/resources/templates/?term=expressroute).

Para criar um circuito de ExpressRoute implantando um modelo:

1. Selecione **Experimente** no seguinte bloco de código e siga as instruções para entrar no Azure cloud Shell.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * A **camada de SKU** determina se um circuito de ExpressRoute é [local](expressroute-faqs.md#expressroute-local), Standard ou [Premium](expressroute-faqs.md#expressroute-premium). Você poderá especificar *Local*, *Standard ou *Premium*. Não é possível alterar o SKU de *Standard/Premium* para *Local*.
   * A **família de SKUs** determina o tipo de cobrança. Você pode especificar *Metereddata* para um plano de dados limitado e *Unlimiteddata* para um plano de dados ilimitado. Você pode alterar o tipo de cobrança de *Metereddata* para *Unlimiteddata*, mas não pode alterar o tipo de *Unlimiteddata* para *Metereddata*. Um circuito *local* é apenas *Unlimiteddata* .
   * **Local de Emparelhamento** é o local físico em que você está realizando o emparelhamento com a Microsoft.

     > [!IMPORTANT]
     > O Local de Emparelhamento indica o [local físico](expressroute-locations.md) em que você está realizando o emparelhamento com a Microsoft. Isso **não** tem vínculo à propriedade "Local", que se refere à posição geográfica na qual o Provedor de Recursos de Rede do Azure está localizado. Embora eles não estejam relacionados, é uma boa prática escolher um provedor de recursos de rede geograficamente próximo do Local de Emparelhamento do circuito.

    O nome do grupo de recursos é o nome do namespace do barramento de serviço com **RG** anexado.

2. Selecione **Copiar** para copiar o script do PowerShell.
3. Clique com o botão direito do mouse no console do Shell e selecione **Colar**.

Pode demorar alguns minutos para criar um hub de eventos.

Azure PowerShell é usado para implantar o modelo neste tutorial. Para outros métodos de implantação de modelo, consulte:

* [Usando o portal do Azure](../azure-resource-manager/templates/deploy-portal.md).
* [Usando CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).
* [Usando a API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Desprovisionamento e exclusão de um circuito do ExpressRoute

Você pode excluir o circuito do ExpressRoute selecionando o ícone **excluir** . Observe as seguintes informações:

* Você deve desvincular todas as redes virtuais do circuito do ExpressRoute. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.
* Se o estado de provisionamento do provedor de serviço de circuito de ExpressRoute for **Provisionando** ou **Provisionado**, você deverá trabalhar com seu provedor de serviços para que ele desprovisione o circuito. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.
* Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como **Não provisionado**), exclua o circuito. Isso interrompe a cobrança pelo circuito.

Você pode excluir o circuito do ExpressRoute executando o seguinte comando do PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar o circuito, continue para as próximas etapas:

* [Criar e modificar o roteamento do circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Vincular a rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)