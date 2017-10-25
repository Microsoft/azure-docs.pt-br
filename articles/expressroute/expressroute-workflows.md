---
title: "Fluxos de trabalho para a configuração de um circuito da ExpressRoute | Microsoft Docs"
description: "Esta página fornece uma orientação pelos fluxos de trabalho de configuração de emparelhamentos e circuito de ExpressRoute"
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 55e0418c-e0bf-44a7-9aa1-720076df9297
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: cherylmc
ms.openlocfilehash: cba1b2cfee379e7d2b079bcb3089981ef1044d66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Fluxos de trabalho do ExpressRoute para provisionamento e estados do circuito
Esta página fornece uma orientação de alto nível pelos fluxos de trabalho de provisionamento do serviço e de configuração do roteamento.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

A figura e as etapas correspondentes a seguir mostram as tarefas que você deve executar para provisionar um circuito do ExpressRoute de ponta a ponta. 

1. Use o PowerShell para configurar um circuito do ExpressRoute. Siga as instruções no artigo [Criar circuitos do ExpressRoute](expressroute-howto-circuit-classic.md) para obter mais detalhes.
2. Solicite conectividade do provedor de serviço. Esse processo varia. Entre em contato com o provedor de conectividade para obter mais detalhes sobre a solicitação de conectividade.
3. Confira se o circuito foi provisionado com sucesso verificando o estado de provisionamento do circuito do ExpressRoute por meio do PowerShell. 
4. Configure os domínios de roteamento. Se seu provedor de conectividade gerencia a camada 3 para você, ele configurará o roteamento para o circuito. Se o seu provedor de conectividade oferecer somente os serviços de Camada 2, configure o roteamento de acordo com as diretrizes descritas nas páginas [requisitos de roteamento](expressroute-routing.md) e [configuração de roteamento](expressroute-howto-routing-classic.md).
   
   * Habilitar o emparelhamento privado do Azure - Você deve habilitar esse emparelhamento para se conectar a VMs/serviços de nuvem implantados dentro das redes virtuais.
   * Habilitar o emparelhamento público do Azure - Você deve habilitar o emparelhamento público do Azure se quiser se conectar aos serviços do Azure hospedados em endereços IP públicos. Esse é um requisito para acessar os recursos do Azure se você tiver optado por habilitar o roteamento padrão para emparelhamento privado do Azure.
   * Habilitar o emparelhamento da Microsoft – você deve habilitar isso para acessar o Office 365 e o Dynamics 365. 
     
     > [!IMPORTANT]
     > Use um proxy/borda diferente da usada para a Internet para se conectar à Microsoft. Usar a mesma borda para o ExpressRoute e para a Internet causará o roteamento assimétrico e falhas de conectividade em sua rede.
     > 
     > 
     
     ![](./media/expressroute-workflows/routing-workflow.png)
5. Vinculando redes virtuais aos circuitos do ExpressRoute - Você pode vincular redes virtuais ao circuito do ExpressRoute. Siga as instruções [para vincular redes virtuais](expressroute-howto-linkvnet-arm.md) ao seu circuito. Essas redes virtuais podem estar na mesma assinatura do Azure que o circuito do ExpressRoute ou podem estar em uma assinatura diferente.

## <a name="expressroute-circuit-provisioning-states"></a>Estados de provisionamento de circuito do ExpressRoute
Cada circuito de ExpressRoute tem dois estados:

* Estado de provisionamento do provedor de serviço
* Status

O status representa o estado de provisionamento da Microsoft. Essa propriedade é definida como Habilitada quando você cria um circuito de ExpressRoute

O estado de provisionamento do provedor de conectividade representa o estado no lado do provedor de conectividade. Ele pode ser *Não Provisionado*, *Provisionando* ou *Provisionado*. O circuito do ExpressRoute deverá estar no estado Provisionado para que possa usá-lo.

### <a name="possible-states-of-an-expressroute-circuit"></a>Possíveis estados de um circuito do ExpressRoute
Esta seção lista os possíveis estados de um circuito do ExpressRoute.

**No momento da criação**

Você verá o circuito do ExpressRoute no seguinte estado assim que executar o cmdlet do PowerShell para criar um circuito do ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Quando o provedor de conectividade estiver no processo de provisionamento do circuito**

Você verá o circuito do ExpressRoute no estado a seguir assim que passar a chave de serviço para o provedor de conectividade, e ele tiver iniciado o processo de provisionamento.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Quando o provedor de conectividade tiver concluído o processo de provisionamento**

Você verá o circuito do ExpressRoute no seguinte estado assim que o provedor de conectividade tiver concluído o processo de provisionamento.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Provisionado e Habilitado são os únicos estados nos quais o circuito pode estar para você poder usá-lo. Se você estiver usando um provedor de camada 2, configure o roteamento para o circuito somente quando ele estiver nesse estado.

**Quando o provedor de conectividade estiver desprovisionando o circuito**

Se você tiver solicitado ao provedor de serviços o desprovisionamento do circuito do ExpressRoute, verá o circuito definido com o estado a seguir, após o provedor de serviços ter concluído o processo de desprovisionamento.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Você pode optar por habilitá-lo novamente se for necessário, ou executar cmdlets do PowerShell para excluir o circuito.  

> [!IMPORTANT]
> Se você executar o cmdlet do PowerShell para excluir o circuito quando a ServiceProviderProvisioningState for Provisionando ou Provisionado, a operação falhará. Trabalhe com seu provedor de conectividade para desprovisionar o circuito de ExpressRoute primeiro e, em seguida, exclua o circuito. A Microsoft continuará a cobrar pelo circuito até que você execute o cmdlet do PowerShell para exclui-lo.
> 
> 

## <a name="routing-session-configuration-state"></a>Estado de configuração da sessão de roteamento
O estado de provisionamento BGP permite que você saiba se a sessão BGP foi habilitada na borda da Microsoft. O estado deve ser habilitado para que você possa usar o emparelhamento.

É importante verificar o estado da sessão BGP, especialmente para o emparelhamento da Microsoft. Além do estado de provisionamento BGP, há outro estado chamado *estado de prefixos públicos anunciados*. O estado de prefixos públicos anunciados deve ser *configurado* , tanto para que a sessão BGP fique ativa quanto para o roteamento funcionar completamente. 

Se o estado de prefixo público anunciado for definido como *validação necessária* , a sessão BGP não estará habilitada, pois os prefixos anunciados não corresponderam ao número AS em qualquer um dos registros do roteamento. 

> [!IMPORTANT]
> Se o estado de prefixos públicos anunciados for *validação manual* , será necessário abrir um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer provas de que você possui os endereços IP anunciados juntamente com o número do Sistema Autônomo associado.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Configurar sua conexão do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurar o roteamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

