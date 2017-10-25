---
title: "Criar e modificar um circuito ExpressRoute: PowerShell: Azure clássico | Microsoft Docs"
description: "Este artigo fornece uma orientação sobre as etapas de criação e de provisionamento de um circuito do ExpressRoute. Este artigo também mostra como verificar o status, atualizar ou excluir e desprovisionar seu circuito."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 93ddc2975db34053c6a776d1c3b931536f3f8ec7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell-classic"></a>Criar e modificar um circuito da ExpressRoute usando o PowerShell (clássico)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo – Portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo fornece uma orientação pelas etapas de criação de um circuito da Azure ExpressRoute usando cmdlets do PowerShell e o modelo de implantação clássico. Este artigo também mostra a você como verificar o status, atualizar ou excluir e desprovisionar um circuito da ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de começar
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>Etapa 1. Examine os pré-requisitos e artigos de fluxo de trabalho
Leia os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.  

### <a name="step-2-install-the-latest-versions-of-the-azure-service-management-sm-powershell-modules"></a>Etapa 2. Instalar as versões mais recentes dos módulos do PowerShell do SM (Gerenciamento de Serviços) do Azure
Siga as instruções em [Introdução aos cmdlets do Azure PowerShell](/powershell/azure/overview) para obter orientações passo a passo sobre como configurar o computador para usar os módulos do Azure PowerShell.

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>Etapa 3. Entre em sua conta do Azure e selecione uma assinatura
1. Abra o console do PowerShell com direitos elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

        Login-AzureRmAccount

2. Verificar as assinaturas da conta.

        Get-AzureRmSubscription

3. Se você tiver mais de uma assinatura, selecione a assinatura que deseja usar.

        Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_id"

4. Confirme se a ID da assinatura selecionada está definida como padrão.

        Get-AzureSubscription -default

## <a name="create-and-provision-an-expressroute-circuit"></a>Criar e provisionar um circuito do ExpressRoute
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>Etapa 1. Importar os módulos do PowerShell para o ExpressRoute
 Se ainda não tiver feito isso, importe os módulos do Azure e de ExpressRoute na sessão do PowerShell para começar a usar os cmdlets de ExpressRoute. Importe os módulos do local onde foram instalados para o computador local. Dependendo do método usado para instalar os módulos, o local pode ser diferente do exemplo a seguir. Modifique o exemplo, se for necessário.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>Etapa 2. Obtenha a lista de provedores, de locais e de larguras de banda com suporte
Antes de criar um circuito de ExpressRoute você precisará de uma lista de provedores de conectividade com suporte, dos locais e de opções de largura de banda.

O cmdlet do PowerShell `Get-AzureDedicatedCircuitServiceProvider` retornará estas informações, que você usará em etapas posteriores:

    Get-AzureDedicatedCircuitServiceProvider

Verifique se o provedor de conectividade está listado. Anote as informações a seguir, pois você precisará delas mais tarde quando criar um circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

Agora você está pronto para criar um circuito do ExpressRoute.         

### <a name="step-3-create-an-expressroute-circuit"></a>Etapa 3. Criar um circuito do ExpressRoute
O exemplo a seguir mostra como criar um circuito do ExpressRoute de 200 Mbps por meio da Equinix, no Vale do Silício. Se estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a solicitação.

> [!IMPORTANT]
> O circuito do ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação quando o provedor de conectividade estiver pronto para provisionar o circuito.
> 
> 

A seguir, um exemplo de solicitação de uma nova chave de serviço:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Ou então, se quiser criar um circuito do ExpressRoute com o complemento premium, use o exemplo a seguir. Confira s [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md) para saber mais sobre o complemento premium.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


A resposta conterá a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>Etapa 4. Listar todos os circuitos do ExpressRoute
Você pode executar o comando `Get-AzureDedicatedCircuit` para obter uma lista de todos os circuitos do ExpressRoute que criou:

    Get-AzureDedicatedCircuit

A resposta será algo semelhante ao seguinte exemplo:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Você pode recuperar essas informações a qualquer momento usando o cmdlet `Get-AzureDedicatedCircuit` . Fazer a chamada sem parâmetros listará todos os circuitos. Sua chave de serviço estará listada no campo *ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Etapa 5. Enviar a chave de serviço ao seu provedor de conectividade para obter provisionamento
*ServiceProviderProvisioningState* fornece informações sobre o estado atual de provisionamento no lado do provedor de serviço. *Status* fornece o estado no lado da Microsoft. Para saber mais sobre estados de provisionamento do circuito, confira o artigo [Fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando você criar um novo circuito do ExpressRoute, ele estará no seguinte estado:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


O circuito assumirá o seguinte o estado quando o provedor de conectividade estiver habilitando-o para você:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Um circuito do ExpressRoute deverá estar no seguinte estado para você poder usá-lo:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Etapa 6. Verifique periodicamente o status e o estado da chave do circuito
Isso permite que você saiba quando seu provedor habilitou seu circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* será exibido como *Provisioned*, como mostrado neste exemplo:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>Etapa 7. Criar sua configuração de roteamento
Confira o artigo [Configuração de roteamento do circuito do ExpressRoute (criar e modificar emparelhamentos de circuito)](expressroute-howto-routing-classic.md) para obter instruções passo a passo.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IP VPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>Etapa 8. Vincular uma rede virtual a um circuito de ExpressRoute
Em seguida, vincule uma rede virtual a seu circuito do ExpressRoute. Confira [Vincular circuitos do ExpressRoute a redes virtuais](expressroute-howto-linkvnet-classic.md) para obter instruções passo a passo. Se você precisar criar uma rede virtual usando o modelo de implantação clássico para a ExpressRoute, confira [Criar uma rede virtual para o ExpressRoute](expressroute-howto-vnet-portal-classic.md).

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtendo o status de um circuito do ExpressRoute
Você pode recuperar essas informações a qualquer momento usando o cmdlet `Get-AzureCircuit` . Fazer a chamada sem parâmetros listará todos os circuitos.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Você pode obter informações sobre um circuito do ExpressRoute específico passando a chave do serviço como um parâmetro para a chamada.

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte exemplo:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modificando um circuito do ExpressRoute
Você pode modificar certas propriedades de um circuito do ExpressRoute sem afetar a conectividade.

Você pode fazer o seguinte sem tempo de inatividade:

* Como habilitar ou desabilitar o complemento premium do ExpressRoute para seu circuito do ExpressRoute.
* Aumente a largura de banda do circuito de ExpressRoute, desde que haja capacidade disponível na porta. Observe que não há suporte para o downgrade da largura de banda de um circuito. 
* Altere o plano de medição de Dados Limitados para Dados Ilimitados. Observe que a alteração do plano de medição de Dados Ilimitados para Dados Limitados não tem suporte.
* Você pode habilitar e desabilitar *Permitir Operações Clássicas*.

Confira as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md) para saber mais sobre limites e limitações.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para habilitar o complemento premium do ExpressRoute
Você pode habilitar o complemento premium do ExpressRoute para o circuito existente usando o seguinte cmdlet do PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

O seu circuito agora tem os recursos do complemento premium do ExpressRoute habilitados. Observe que passaremos a cobrar pelo recurso do complemento Premium assim que o comando for executado com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desabilitar o complemento premium do ExpressRoute
> [!IMPORTANT]
> Esta operação poderá falhar se você estiver usando recursos que ultrapassem o que é permitido para o circuito padrão.
> 
> 

#### <a name="considerations"></a>Considerações

* Verifique se o número de redes virtuais vinculadas ao circuito é menor do que 10 antes de fazer o downgrade de Premium para padrão. Caso contrário, sua solicitação de atualização falhará e você receberá uma cobrança com as taxas premium.
* Você precisa desvincular todas as redes virtuais em outras regiões geopolíticas. Caso contrário, sua solicitação de atualização falhará e você receberá uma cobrança com as taxas premium.
* Sua tabela de roteamento deve ter menos de 4.000 rotas para o emparelhamento privado. Se o tamanho da tabela de roteamento for maior que 4.000 rotas, a sessão BGP será descartada e não será reabilitada até que o número de prefixos anunciados fique abaixo de 4.000.

#### <a name="disable-the-premium-add-on"></a>Desabilitar o complemento premium
Você pode desabilitar o complemento premium do ExpressRoute para o circuito existente usando o seguinte cmdlet do PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito do ExpressRoute
Confira as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md) para obter opções de largura de banda com suporte para seu provedor. Você pode escolher um tamanho maior do que o tamanho do circuito existente, desde que a porta física (na qual o circuito foi criado) permita.

> [!IMPORTANT]
> Talvez seja necessário recriar o circuito de ExpressRoute se não houver capacidade adequada na porta existente. Você não pode atualizar o circuito não se houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito do ExpressRoute e um reprovisionamento de um novo circuito do ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Redimensionar um circuito

Após decidir de qual tamanho precisa, você pode usar o seguinte comando para redimensionar o circuito:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

O circuito será sido dimensionado no lado da Microsoft. Entre em contato com seu provedor de conectividade para que ele atualize as configurações a fim de corresponder a essa alteração. Observe que passaremos a lhe cobrar pela opção de largura de banda atualizada a partir desse momento.

Se você vir o seguinte erro quando aumentar a largura de banda do circuito, isso significa que não há largura de banda suficiente restante na porta física onde o circuito existente foi criado. Será necessário excluir este circuito e criar um novo circuito do tamanho que você precisa. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Desprovisionamento e exclusão de um circuito do ExpressRoute

### <a name="considerations"></a>Considerações

* Você deve desvincular todas as redes virtuais do circuto do ExpressRoute para que a operação seja bem-sucedida. Se essa operação falhar, verifique se você tem redes virtuais vinculadas ao circuito.
* Se o estado de provisionamento do provedor de serviço de circuito de ExpressRoute for **Provisionando** ou **Provisionado**, você deverá trabalhar com seu provedor de serviços para que ele desprovisione o circuito. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.
* Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como **Não provisionado**), exclua o circuito. Isso interromperá a cobrança do circuito.

#### <a name="delete-a-circuit"></a>Excluir um circuito

Você pode excluir o circuito do ExpressRoute executando o comando a seguir:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Próximas etapas
Depois de criar seu circuito, faça o seguinte:

* [Criar e modificar o roteamento do circuito do ExpressRoute](expressroute-howto-routing-classic.md)
* [Vincular a rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)

