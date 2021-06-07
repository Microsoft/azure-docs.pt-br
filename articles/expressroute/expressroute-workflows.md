---
title: 'Azure ExpressRoute: fluxo de trabalho de configuração de circuito'
description: Esta página mostra o fluxo de trabalho para configurar circuitos e emparelhamentos do ExpressRoute
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 24ad325cae2ee71ad49ee8ee055a83ceb8fa7ef2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721728"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Fluxos de trabalho do ExpressRoute para provisionamento e estados do circuito

Este artigo explica o provisionamento de serviços e os fluxos de trabalho de configuração de roteamento em um alto nível. As seções a seguir descrevem as tarefas para provisionar um circuito de ExpressRoute de ponta a ponta.

## <a name="workflow-steps"></a>Etapas do fluxo de trabalho

### <a name="1-prerequisites"></a>1. Pré-requisitos

Verifique se os pré-requisitos foram atendidos. Para obter uma lista completa, consulte [Prerequisites and Checklist](expressroute-prerequisites.md).

* Uma assinatura do Azure foi criada.
* A conectividade física foi estabelecida com o parceiro ExpressRoute ou configurada por meio do ExpressRoute Direct. Examine o local, confira [locais e parceiros](expressroute-locations-providers.md#partners) para exibir o parceiro expressroute e a conectividade direta do expressroute entre locais de emparelhamento.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. solicite conectividade ou configure o ExpressRoute Direct

Solicite a conectividade do provedor de serviços ou configure o ExpressRoute Direct.

#### <a name="expressroute-partner-model"></a>Modelo de parceiro do ExpressRoute

Solicite conectividade do provedor de serviço. Esse processo varia. Entre em contato com o provedor de conectividade para obter mais detalhes sobre a solicitação de conectividade.

* Selecione o parceiro do ExpressRoute
* Selecione o local de emparelhamento
* Selecione a largura de banda
* Selecionar o modelo de cobrança
* Selecionar complemento Standard ou Premium

#### <a name="expressroute-direct-model"></a>Modelo direto do ExpressRoute

* Exibir a capacidade do ExpressRoute Direct disponível entre locais de emparelhamento.
* Reserve portas criando o recurso do ExpressRoute Direct em sua assinatura do Azure.
* Solicite e receba a letra de autorização e ordene as conexões físicas cruzadas do provedor de local de emparelhamento.
* Habilite o estado do administrador e exiba os níveis de luz e o link físico usando [Azure monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics).

### <a name="3-create-an-expressroute-circuit"></a>3. criar um circuito do ExpressRoute

#### <a name="expressroute-partner-model"></a>Modelo de parceiro do ExpressRoute

Verifique se o parceiro do ExpressRoute está pronto para provisionar a conectividade. O circuito do ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. Use as instruções em [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) para criar seu circuito.

#### <a name="expressroute-direct-model"></a>Modelo direto do ExpressRoute

Verifique se o link físico e o estado do administrador estão habilitados em ambos os links. Consulte [como configurar o ExpressRoute Direct](how-to-expressroute-direct-portal.md) para obter diretrizes. O circuito do ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. Use as instruções em [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) para criar seu circuito.

### <a name="4-service-provider-provisions-connectivity"></a>4. o provedor de serviços provisiona a conectividade

Esta seção pertence apenas ao modelo de conectividade do parceiro ExpressRoute:

* Forneça a chave de serviço (s-Key) ao provedor de conectividade.
* Forneça informações adicionais necessárias para o provedor de conectividade (por exemplo, ID de VPN).
* Se o provedor gerencia a configuração de roteamento, forneça os detalhes necessários.

Você pode garantir que o circuito foi provisionado com êxito verificando o estado de provisionamento do circuito do ExpressRoute usando o PowerShell, a portal do Azure ou a CLI.

### <a name="5-configure-routing-domains"></a>5. configurar domínios de roteamento

Configure os domínios de roteamento. Se seu provedor de conectividade gerencia a configuração de camada 3, ele configurará o roteamento para o circuito. Se seu provedor de conectividade oferece apenas serviços de camada 2 ou se você estiver usando o ExpressRoute Direct, você deve configurar o roteamento de acordo com as diretrizes descritas nos artigos sobre [requisitos de roteamento](expressroute-routing.md) e [configuração de roteamento](expressroute-howto-routing-classic.md) .

#### <a name="for-azure-private-peering"></a>Para emparelhamento privado do Azure

Habilite o emparelhamento privado para se conectar a VMs e serviços de nuvem implantados na rede virtual do Azure.

* Sub-redes IPv4:
    * Sub-rede de emparelhamento para o caminho 1 (/30)
    * Sub-rede de emparelhamento para o caminho 2 (/30)
* Sub-redes IPv6 (opcional):
    * Sub-rede de emparelhamento para o caminho 1 (/126)
    * Sub-rede de emparelhamento para o caminho 2 (/126)
* ID de VLAN para emparelhamento
* ASN para emparelhamento
* ExpressRoute ASN = 12076
* Hash MD5 (opcional)

#### <a name="for-microsoft-peering"></a>Para emparelhamento da Microsoft

Habilite isso para acessar o Microsoft serviços online, como Microsoft 365. Além disso, todos os serviços de PaaS do Azure são acessíveis através do emparelhamento da Microsoft. Você deve garantir que use um proxy/borda separado para se conectar à Microsoft do que o que você usa para a Internet. Usar a mesma borda para o ExpressRoute e para a Internet causará o roteamento assimétrico e falhas de conectividade em sua rede.

* Sub-redes IPv4:
    * Sub-rede de emparelhamento para o caminho 1 (/30)-deve ser o IP público
    * A sub-rede de emparelhamento para o caminho 2 (/30)-deve ser o IP público
* Sub-redes IPv6 (opcional):
    * Sub-rede de emparelhamento para o caminho 1 (/126)-deve ser um IP público
    * A sub-rede de emparelhamento para o caminho 2 (/126)-deve ser um IP público
* ID de VLAN para emparelhamento
* ASN para emparelhamento
* Prefixos anunciados-devem ser prefixos IP públicos
* ASN do cliente (opcional se for diferente do ASN de emparelhamento)
* RIR/TIR para validação de IP e ASN
* ExpressRoute ASN = 12076
* Hash MD5 (opcional)

### <a name="6-start-using-the-expressroute-circuit"></a>6. começar a usar o circuito do ExpressRoute

* Você pode vincular redes virtuais do Azure ao circuito do ExpressRoute para habilitar a conectividade do local para a rede virtual do Azure. Consulte o artigo [vincular um VNet a um circuito](expressroute-howto-linkvnet-arm.md) para obter orientação. Essas redes virtuais podem estar na mesma assinatura do Azure que o circuito do ExpressRoute ou podem estar em uma assinatura diferente.
* Conecte-se aos serviços do Azure e aos serviços de nuvem da Microsoft por meio do emparelhamento da Microsoft.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>Estados de provisionamento do circuito de parceiro do ExpressRoute

A seção a seguir descreve os diferentes Estados de circuito do ExpressRoute para o modelo de conectividade do parceiro ExpressRoute.
Cada circuito de parceiro do ExpressRoute tem dois Estados:

* **ServiceProviderProvisioningState** representa o estado no lado do provedor de conectividade. Ele pode ser *Não Provisionado*, *Provisionando* ou *Provisionado*. O circuito do ExpressRoute deve estar em um estado provisionado para configurar o emparelhamento. **Esse estado pertence apenas aos circuitos de parceiros do expressroute e não é exibido nas propriedades de um circuito direto do expressroute**.

* **Status** representa o estado de provisionamento da Microsoft. Essa propriedade é definida como habilitada quando você cria um circuito do ExpressRoute

### <a name="possible-states-of-an-expressroute-circuit"></a>Possíveis estados de um circuito do ExpressRoute

Esta seção descreve os possíveis estados de um circuito do ExpressRoute criado no modelo de conectividade do parceiro ExpressRoute.

**No momento da criação**

O circuito do ExpressRoute relatará os seguintes Estados na criação de recursos.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Quando o provedor de conectividade está no processo de provisionamento do circuito**

O circuito do ExpressRoute relatará os seguintes Estados enquanto o provedor de conectividade estiver trabalhando para provisionar o circuito.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Quando o provedor de conectividade tiver concluído o processo de provisionamento**

O circuito do ExpressRoute relatará os seguintes Estados assim que o provedor de conectividade tiver provisionado com êxito o circuito.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Quando o provedor de conectividade estiver Desprovisionando o circuito**

Se o circuito do ExpressRoute precisar ser desprovisionado, o circuito relatará os Estados a seguir quando o provedor de serviços tiver concluído o processo de desprovisionamento.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

Você pode optar por habilitá-lo novamente se for necessário, ou executar cmdlets do PowerShell para excluir o circuito.  

> [!IMPORTANT]
> Um circuito não pode ser excluído quando o ServiceProviderProvisioningState é provisionado ou provisionado. O provedor de conectividade precisa desprovisionar o circuito antes que ele possa ser excluído. A Microsoft continuará a cobrar o circuito até que o recurso de circuito do ExpressRoute seja excluído no Azure.
> 

## <a name="routing-session-configuration-state"></a>Estado de configuração da sessão de roteamento

O estado de provisionamento do BGP relata se a sessão BGP foi habilitada no Microsoft Edge. O estado deve ser habilitado para usar o emparelhamento privado ou da Microsoft.

É importante verificar o estado da sessão BGP, especialmente para o emparelhamento da Microsoft. Além do estado de provisionamento BGP, há outro estado chamado *estado de prefixos públicos anunciados*. O estado de prefixos públicos anunciados deve estar no estado *configurado* , tanto para a sessão BGP quanto para que o roteamento funcione de ponta a ponta. 

Se o estado de prefixo público anunciado for definido como *validação necessária* , a sessão BGP não estará habilitada, pois os prefixos anunciados não corresponderam ao número AS em qualquer um dos registros do roteamento.

> [!IMPORTANT]
> Se o estado de prefixos públicos anunciados estiver no estado de *validação manual* , você precisará abrir um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer evidências de que você possui os endereços IP anunciados junto com o número do sistema autônomo associado.
> 
> 

## <a name="next-steps"></a>Próximas etapas

* Configurar sua conexão do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurar o roteamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
