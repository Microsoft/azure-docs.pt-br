---
title: Sobre o Azure ExpressRoute Direct
description: Saiba mais sobre os principais recursos do Azure ExpressRoute Direct e as informações necessárias para carregar o ExpressRoute Direct, como SKUs disponíveis e requisitos técnicos.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: duau
ms.openlocfilehash: 56d6a76991c4386be45b2c18f4edb3d363e58fa5
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027135"
---
# <a name="about-expressroute-direct"></a>Sobre o ExpressRoute Direct

O ExpressRoute Direct oferece a capacidade de se conectar diretamente à rede global da Microsoft em locais de emparelhamento distribuídos estrategicamente em todo o mundo. O ExpressRoute Direct fornece conectividade dual de 100 Gbps ou 10 Gbps, que dá suporte à conectividade ativa/ativa em escala. Você pode trabalhar com qualquer provedor de serviços para ER Direct.

Os principais recursos que o ExpressRoute Direct fornece incluem, mas não estão limitados a:

* Ingestão de dados em massa em serviços como Armazenamento e o Cosmos DB
* Isolamento físico para indústrias que são regulamentadas e exigem conectividade dedicada e isolada, como: bancos, governo e varejo
* Controle granular de distribuição de circuito com base em unidade de negócios

## <a name="onboard-to-expressroute-direct"></a>Integração ao ExpressRoute Direct

Antes de usar o ExpressRoute Direct, você deve primeiro registrar sua assinatura. Para registrar, execute os seguintes comandos usando Azure PowerShell:

1.  Entre no Azure e selecione a assinatura que você deseja registrar.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Registre sua assinatura para visualização pública usando o seguinte comando:
1. 
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Depois de registrado, verifique se o provedor de recursos **Microsoft. Network** está registrado em sua assinatura. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos.

1. Acesse suas configurações de assinatura, conforme descrito em [provedores de recursos e tipos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).

1. Em sua assinatura, para **provedores de recursos**, verifique se o provedor **Microsoft. Network** mostra um status **registrado** . Se o provedor de recursos Microsoft. Network não estiver presente na lista de provedores registrados, adicione-o.

Se você começar a usar o ExpressRoute Direct e observar que não há portas disponíveis no local de emparelhamento escolhido, envie um email ExpressRouteDirect@microsoft.com para solicitar mais inventário.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute usando um provedor de serviços e ExpressRoute Direct

| **ExpressRoute usando um provedor de serviços** | **ExpressRoute Direct** | 
| --- | --- |
| Usa provedores de serviços para habilitar a integração rápida e a conectividade com a infraestrutura existente | Requer infraestrutura de 100 Gbps/10 Gbps e gerenciamento completo de todas as camadas
| Integra-se a centenas de fornecedores, inclusive Ethernet e MPLS | Capacidade direta/dedicada para setores regulamentados e ingestão de dados em massa |
| SKUs de circuitos de 50 Mbps a 10 Gbps | O cliente pode selecionar uma combinação das seguintes SKUs de circuito no ExpressRoute Direct de 100-Gbps: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> O cliente pode selecionar uma combinação dos seguintes SKUs de circuito no ExpressRoute direto de 10 Gbps:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Otimizado para um único locatário | Otimizado para locatário único com várias unidades de negócios e vários ambientes de trabalho

## <a name="expressroute-direct-circuits"></a>Circuitos do ExpressRoute Direct

Microsoft Azure ExpressRoute permite que você estenda sua rede local para a nuvem da Microsoft por uma conexão privada facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como Microsoft Azure e Microsoft 365.

Cada local de emparelhamento tem acesso à rede global da Microsoft e pode acessar qualquer região em uma zona geopolítica por padrão. Você pode acessar todas as regiões globais com um circuito Premium.  

A funcionalidade na maioria dos cenários é equivalente aos circuitos que usam um provedor de serviços do ExpressRoute para operar. Para dar suporte a ainda mais granularidade e às novas funcionalidades oferecidas usando o ExpressRoute Direct, há determinadas funcionalidades principais que existem nos Circuitos do ExpressRoute Direct.

## <a name="circuit-skus"></a>SKUs de circuito

O ExpressRoute Direct dá suporte a cenários de ingestão de dados em massa no Armazenamento do Azure e em outros serviços de Big Data. Os circuitos do ExpressRoute em 100-Gbps ExpressRoute Direct agora também dão suporte a SKUs de circuito de **40 Gbps** e * * 100-Gbps. Os pares de porta física têm apenas **100 Gbps ou 10 Gbps** e podem ter vários circuitos virtuais. Tamanhos de circuito:

| **100-Gbps ExpressRoute direto** | **ExpressRoute direto de 10 Gbps** | 
| --- | --- |
| **Largura de banda assinada**: 200 Gbps | **Largura de banda assinada**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisitos técnicos

* Interfaces de roteador do Microsoft Enterprise Edge (MSEE):
    * Duas portas Ethernet de 10 Gigabit ou 100-Gigabit somente no par de roteador
    * Conectividade de fibra de modo único LR
    * IPv4 e IPv6
    * IP MTU 1500 bytes

* Conectividade de comutador/roteador 2/camada 3:
    * Deve oferecer suporte à marca 1 802.1 Q (Dot1Q) ou a um encapsulamento de marca 802.1 Q (QinQ) de duas marcas
    * EtherType = 0x8100
    * É necessário adicionar a tag VLAN externa (STAG) com base na ID de VLAN especificada pela Microsoft- *aplicável somente em QinQ*
    * Deve oferecer suporte a várias sessões BGP (VLANs) por porta e dispositivo
    * Conectividade IPv4 e IPv6. *Para IPv6, nenhuma subinterface extra será criada. O endereço IPv6 será adicionado à subinterface existente*. 
    * Opcional: suporte a [BFD (detecção de encaminhamento bidirecional)](./expressroute-bfd.md) , que é configurado por padrão em todos os emparelhamentos privados em circuitos do ExpressRoute

## <a name="vlan-tagging"></a>Marcação de VLAN

O ExpressRoute Direct dá suporte à marcação de VLAN QinQ e Dot1Q.

* **Marcação de VLAN QinQ** permite domínios de roteamento isolados com base no circuito do ExpressRoute. O Azure fornece dinamicamente uma marca S na criação do circuito e não pode ser alterado. Cada emparelhamento no circuito (privado e Microsoft) usará uma marca C exclusiva como a VLAN. A marca C não precisa ser exclusiva entre circuitos nas portas diretas do ExpressRoute.

* **Marcação de VLAN Dot1Q** permite a marcação de uma única VLAN com base no par de portas do ExpressRoute Direct. Uma marca C usada em um emparelhamento precisa ser exclusiva em todos os circuitos e emparelhamentos do par de portas do ExpressRoute Direct.

## <a name="workflow"></a>Fluxo de trabalho

[![modelo](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Contrato de Nível de Serviço

O ExpressRoute Direct fornece o mesmo SLA de nível empresarial com conexões redundantes ativas/ativas à rede global da Microsoft. A infraestrutura de ExpressRoute é redundante e a conectividade com a rede global da Microsoft é redundante e diversificada e dimensionada corretamente com os requisitos do cliente. 

## <a name="next-steps"></a>Próximas etapas

[Configurar o ExpressRoute Direct](expressroute-howto-erdirect.md)
