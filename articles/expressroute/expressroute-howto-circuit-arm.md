---
title: 'Criar e modificar um circuito do ExpressRoute – PowerShell: Azure | Microsoft Docs'
description: Criar, provisionar, verificar, atualizar, excluir e desprovisionar um circuito do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 02/20/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.custom: seodec18
ms.openlocfilehash: 2ff22a3c0087ff7e24517f792ba9abacbae76e3a
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846632"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Criar e modificar um circuito do ExpressRoute usando o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo ajuda você a criar um circuito do ExpressRoute usando os cmdlets do Windows PowerShell e o modelo de implantação do Azure Resource Manager. Você também pode verificar o status, atualizar, excluir ou desprovisionar um circuito.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configuração, examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md).

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>Criar e provisionar um circuito do ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Entre na sua conta do Azure e selecione sua assinatura

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Obtenha a lista de provedores, de locais e de larguras de banda com suporte
Antes de criar um circuito de ExpressRoute você precisará de uma lista de provedores de conectividade com suporte, dos locais e de opções de largura de banda.

O cmdlet **Get-AzExpressRouteServiceProvider** do PowerShell retorna essas informações, que você usará em etapas posteriores:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Verifique se o provedor de conectividade está listado. Anote as informações a seguir, pois você precisará delas mais tarde quando criar um circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

Agora você está pronto para criar um circuito do ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Criar um circuito do ExpressRoute
Se você ainda não tiver um grupo de recursos, deverá criar um antes de criar o circuito do ExpressRoute. Faça isso ao executar o seguinte comando:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

O exemplo a seguir mostra como criar um circuito do ExpressRoute de 200 Mbps por meio da Equinix, no Vale do Silício. Se estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a solicitação. Use o exemplo a seguir para solicitar uma nova chave de serviço:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Especifique a camada da SKU e a família de SKUs corretas:

* A camada da SKU determina se um complemento padrão ou premium do ExpressRoute está habilitado. Você pode especificar *Standard* para obter o SKU padrão ou *Premium* para o complemento premium.
* A família da SKU determina o tipo de cobrança. Você pode especificar *Metereddata* para um plano de dados limitado e *Unlimiteddata* para um plano de dados ilimitado. É possível alterar o tipo de cobrança de *Metereddata* para *Unlimiteddata*, mas não é possível alterar o tipo de *Unlimiteddata* para *Metereddata*.

> [!IMPORTANT]
> O circuito do ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação quando o provedor de conectividade estiver pronto para provisionar o circuito.
>
>

A resposta conterá a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Listar todos os circuitos do ExpressRoute
Para obter uma lista de todos os circuitos do ExpressRoute que você criou, execute o comando **Get-AzExpressRouteCircuit** :

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A resposta se assemelha à seguinte:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Você pode recuperar essas informações a qualquer momento usando o cmdlet `Get-AzExpressRouteCircuit` . Fazer a chamada sem parâmetros listará todos os circuitos. Sua chave de serviço está listada no campo *ServiceKey*:

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


A resposta se assemelha à seguinte:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Enviar a chave de serviço ao seu provedor de conectividade para obter provisionamento
*ServiceProviderProvisioningState* fornece informações sobre o estado atual de provisionamento no lado do provedor de serviço. Status fornece o estado no lado da Microsoft. Para saber mais sobre os estados de provisionamento do circuito, consulte [Fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando você cria um novo circuito do ExpressRoute, ele está no seguinte estado:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



O circuito assumirá o seguinte o estado quando o provedor de conectividade estiver habilitando-o para você:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Para que você consiga usar um circuito do ExpressRoute, ele deverá estar no seguinte estado:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Verifique periodicamente o status e o estado da chave do circuito
A verificação do status e o estado da chave do circuito informará quando o provedor tiver habilitado seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* será exibido como *Provisioned*, como mostrado neste exemplo:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta se assemelha à seguinte:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. Criar sua configuração de roteamento
Para obter instruções passo a passo, confira o artigo [configuração do roteamento de circuito do ExpressRoute](expressroute-howto-routing-arm.md) para criar e modificar os emparelhamentos de circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IP VPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Vincular uma rede virtual a um circuito do ExpressRoute
Em seguida, vincule uma rede virtual a seu circuito do ExpressRoute. Use o artigo [Vincular redes virtuais a circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) ao trabalhar com o modelo de implantação do Gerenciador de Recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtendo o status de um circuito do ExpressRoute
Você pode recuperar essas informações a qualquer momento usando o cmdlet **Get-AzExpressRouteCircuit** . Fazer a chamada sem parâmetros listará todos os circuitos.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


A resposta é semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                            "ServiceProviderName": "Equinix",
                                            "PeeringLocation": "Silicon Valley",
                                            "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Você pode obter informações sobre um circuito do ExpressRoute específico passando o nome do grupo de recursos e o nome do circuito como um parâmetro para a chamada:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta se assemelha à seguinte:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                            "Tier": "Standard",
                                            "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```azurepowershell-interactive
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Modificar um circuito do ExpressRoute
Você pode modificar certas propriedades de um circuito do ExpressRoute sem afetar a conectividade.

É possível executar as seguintes tarefas sem tempo de inatividade:

* Como habilitar ou desabilitar o complemento premium do ExpressRoute para seu circuito do ExpressRoute.
* Aumente a largura de banda do circuito de ExpressRoute, desde que haja capacidade disponível na porta. Não há suporte para o downgrade da largura de banda de um circuito.
* Altere o plano de medição de Dados Limitados para Dados Ilimitados. Não há suporte para alteração do plano de medição de Dados Ilimitados para Dados Limitados.
* Você pode habilitar e desabilitar *Permitir Operações Clássicas*.

Para saber mais sobre limites e limitações, confira as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para habilitar o complemento premium do ExpressRoute
Você pode habilitar o complemento premium do ExpressRoute para o circuito existente usando o seguinte snippet do PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Agora, o circuito terá os recursos do complemento ExpressRoute Premium habilitados. Começaremos a cobrar pela funcionalidade do complemento Premium assim que o comando for executado com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desabilitar o complemento premium do ExpressRoute
> [!IMPORTANT]
> Se usar recursos que ultrapassam o permitido para o circuito padrão, a operação poderá falhar.
>
>

Observe as seguintes informações:

* Antes de fazer o downgrade de premium para standard, verifique se o número de redes virtuais vinculadas ao circuito é menor que 10. Se você não fizer isso, sua solicitação de atualização falhará e você será cobrado conforme as tarifas premium.
* Você precisa desvincular todas as redes virtuais em outras regiões geopolíticas. Se você não fizer isso, sua solicitação de atualização falhará e você será cobrado conforme as tarifas premium.
* Sua tabela de roteamento deve ter menos de 4.000 rotas para o emparelhamento privado. Se o tamanho da tabela de roteamento for maior que 4.000 rotas, a sessão BGP será descartada e não poderá ser reabilitada até que o número de prefixos anunciados fique abaixo de 4.000.

Você pode desabilitar o complemento premium do ExpressRoute para o circuito existente usando o seguinte cmdlet do PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito do ExpressRoute
Para obter opções de largura de banda com suporte para seu provedor, confira as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md). Você pode escolher qualquer tamanho maior do que o tamanho do circuito existente.

> [!IMPORTANT]
> Talvez seja necessário recriar o circuito de ExpressRoute se não houver capacidade adequada na porta existente. Você não pode atualizar o circuito não se houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito do ExpressRoute e um reprovisionamento de um novo circuito do ExpressRoute.
>

Depois de decidir sobre qual tamanho você precisa, use o seguinte comando para redimensionar o circuito:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


O circuito será dimensionado no lado da Microsoft. Entre em contato com seu provedor de conectividade para que ele atualize as configurações para corresponder a essa alteração. Depois que você fizer essa notificação, começaremos a cobrar pela opção de largura de banda atualizada.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover a SKU de limitado para ilimitado
Você pode alterar a SKU de um circuito de ExpressRoute usando o seguinte snippet de código do PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso aos ambientes clássico e do Resource Manager
Confira as instruções em [Mover os circuitos de ExpressRoute do modelo de implantação Clássico para o Resource Manager](expressroute-howto-move-arm.md).

## <a name="delete"></a>Desprovisionamento e exclusão de um circuito do ExpressRoute
Observe as seguintes informações:

* Você deve desvincular todas as redes virtuais do circuito do ExpressRoute. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.
* Se o estado de provisionamento do provedor de serviço de circuito de ExpressRoute for **Provisionando** ou **Provisionado**, você deverá trabalhar com seu provedor de serviços para que ele desprovisione o circuito. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.
* Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como **Não provisionado**), exclua o circuito. Isso interrompe a cobrança pelo circuito.

Você pode excluir o circuito do ExpressRoute executando o comando a seguir:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar o circuito, não deixe de executar as seguintes etapas:

* [Criar e modificar o roteamento do circuito do ExpressRoute](expressroute-howto-routing-arm.md)
* [Vincular a rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
