---
title: Gateway de VPN do Azure | Microsoft Docs
description: Saiba mais sobre o que é um gateway de VPN e as maneiras de usar um gateway de VPN para se conectar a redes virtuais do Azure. Incluindo soluções IPsec/IKE Site a Site entre locais e de rede virtual a rede virtual, bem como VPN Ponto a Site.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 05/22/2019
ms.author: cherylmc
ms.openlocfilehash: b4ad8697997a8c90a6548c66819bfe790c8235e3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798986"
---
# <a name="what-is-vpn-gateway"></a>O que é o Gateway de VPN?

Um gateway de VPN é um tipo específico de gateway de rede virtual que é usado para enviar tráfego criptografado entre uma rede virtual do Azure e um local na Internet pública. Você também pode usar um gateway de VPN para enviar tráfego criptografado entre as redes virtuais do Azure pela rede da Microsoft. Uma rede virtual pode ter apenas um gateway de VPN. No entanto, você pode criar várias conexões com o mesmo gateway de VPN. Quando você cria várias conexões com o mesmo gateway de VPN, todos os túneis de VPN compartilham a largura de banda de gateway disponível.

## <a name="whatis"></a>O que é um gateway da rede virtual?

Um gateway de rede virtual é composto por duas ou mais máquinas virtuais implantadas em uma sub-rede específica criada, que é chamada de *sub-rede de gateway*. As VMs localizadas na sub-rede de gateway são criadas quando você cria o gateway de rede virtual. As VMs de gateway de rede virtual são configuradas para conter as tabelas de roteamento e os serviços de gateway específicos do gateway. Não é possível configurar diretamente as VMs que fazem parte do gateway de rede virtual, e você nunca deve implantar recursos adicionais para a sub-rede de gateway.

É possível implantar gateways de VPN em Zonas de Disponibilidade do Azure. Isso traz resiliência, escalabilidade e maior disponibilidade para gateways de rede virtual. A implantação de gateways em Zonas de Disponibilidade do Azure separa de forma física e lógica os gateways em uma região, enquanto protege a conectividade de rede local com o Azure de falhas no nível na zona. Confira [Sobre os gateways de rede virtual com redundância de zona em Zonas de Disponibilidade do Azure](about-zone-redundant-vnet-gateways.md)

A criação de um gateway de rede virtual pode levar até 45 minutos para ser concluída. Quando você cria um gateway de rede virtual, as VMs de gateway são implantadas na sub-rede de gateway e definidas com as configurações especificadas. Uma das configurações que você define é o tipo de gateway. O tipo de gateway 'vpn' especifica que o tipo de gateway de rede virtual criado é um gateway de VPN. Depois de criar um gateway de VPN, você pode criar uma conexão de túnel de VPN IPsec/IKE entre o gateway de VPN e o outro gateway de VPN (rede virtual a rede virtual), ou criar uma conexão de túnel de VPN IPsec/IKE entre locais, entre o gateway de VPN e um dispositivo VPN local (Site a Site). Você também pode criar uma conexão VPN Ponto a Site (VPN sobre OpenVPN, IKEv2 ou SSTP), que permite a você se conectar à sua rede virtual de um local remoto, como de uma conferência ou em casa.

## <a name="configuring"></a>Configurando um Gateway de VPN

Uma conexão de gateway VPN conta com vários recursos que são configurados com definições específicas. A maioria dos recursos pode ser configurada separadamente, embora alguns deles devam ser configurados em determinada ordem.

### <a name="settings"></a>Configurações

As configurações que você escolheu para cada recurso são essenciais para a criação de uma conexão bem-sucedida. Para obter informações sobre os recursos individuais e as configurações do Gateway de VPN, consulte [Sobre as configurações do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md). Este artigo contém informações para ajudar você a entender tipos de gateway, SKUs de gateway, tipos de VPN, tipos de conexão, sub-redes de gateway, gateways de rede local e várias outras configurações de recursos que você possa considerar.

### <a name="tools"></a>Ferramentas de implantação

Você pode começar criando e configurando os recursos usando uma ferramenta de configuração, como o portal do Azure. Você pode decidir trocar para outra ferramenta, como o PowerShell, para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não é possível configurar cada recurso e definição de recursos no portal do Azure. As instruções nos artigos para cada topologia de conexão especificam quando uma ferramenta de configuração específica é necessária. 

### <a name="models"></a>Modelo de implantação

Atualmente, há dois modelos de implantação do Azure. Quando você configura um gateway de VPN, as etapas efetuadas dependem do modelo de implantação utilizado para criar sua rede virtual. Por exemplo, se tiver criado a rede virtual usando o modelo de implantação clássico, você usará as diretrizes e instruções do modelo de implantação clássico para criar e configurar o gateway de VPN. Para obter mais informações sobre os modelos de implantação, consulte [Noções básicas sobre o Resource Manager e os modelos de implantação clássicos](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="planningtable"></a>Tabela de planejamento

A tabela a seguir pode ajudá-lo a decidir a melhor opção de conectividade para sua solução.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gwsku"></a>SKUs do Gateway

Ao criar um gateway de rede virtual, especifique a SKU do gateway que você deseja usar. Selecione as SKUs que atendem às suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, recursos e SLAs. Para obter mais informações sobre as SKUs do gateway, incluindo recursos compatíveis, produção e teste de desenvolvimento e etapas de configuração, confira o artigo [Configurações do Gateway de VPN – SKUs do Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para obter informações sobre a SKU herdada, confira [Como trabalhar com SKUs herdadas](vpn-gateway-about-skus-legacy.md).

### <a name="benchmark"></a>SKUs de gateway pelo túnel, a conexão e a taxa de transferência

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="diagrams"></a>Diagramas de topologia de conexão

É importante saber que há diferentes configurações disponíveis para conexões de gateway de VPN. Você precisa determinar qual configuração melhor atende às suas necessidades. Nas seções a seguir, você pode exibir informações e diagramas de topologia sobre as seguintes conexões de gateway VPN: As seções abaixo contêm tabelas que listam:

* Modelo de implantação disponível
* Ferramentas de configuração disponíveis
* Links que levam você diretamente a um artigo, se disponível

Use os diagramas e as descrições para ajudar a selecionar a topologia de conexão adequada a seus requisitos. Os diagramas mostram as principais topologias de linha de base, mas é possível criar topologias mais complexas usando os diagramas como uma diretriz.

## <a name="s2smulti"></a>Site a Site e Vários Sites (túnel VPN IPsec/IKE)

### <a name="S2S"></a>Site a site

Uma conexão de gateway VPN Site a Site (S2S) é uma conexão por túnel VPN IPsec/IKE (IKEv1 ou IKEv2). As conexões S2S podem ser usadas para configurações entre instalações e híbridas. Uma conexão S2S exige um dispositivo VPN local com um endereço IP público atribuído a ele e não por uma NAT. Para saber mais sobre como selecionar um dispositivo VPN, confira as [Perguntas Frequentes de Gateway de VPN – dispositivos VPN](vpn-gateway-vpn-faq.md#s2s).

![Exemplo de conexão Site a Site do Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Vários Sites

Esse tipo de conexão é uma variação da conexão Site a Site. Você pode criar mais de uma conexão de VPN em seu gateway de rede virtual, normalmente se conectando a vários sites locais. Ao trabalhar com várias conexões, você deve usar um tipo de VPN baseado em rota (conhecido como gateway dinâmico ao trabalhar com redes virtuais clássicas). Como cada rede virtual pode ter apenas um gateway de VPN, todas as conexões por meio do gateway compartilham a largura de banda disponível. Esse tipo de conexão é frequentemente chamado de conexão de "vários sites".

![Exemplo de conexão Multissite do Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modelos de implantação e métodos para Site a Site e Vários Sites

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>VPN ponto a site

Uma conexão de gateway de VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. Uma conexão P2S é estabelecida iniciando-a do computador cliente. Essa solução é útil para pessoas que trabalham remotamente que querem se conectar às VNets do Azure de um local remoto, como de casa ou de uma conferência. A VPN P2S também é uma solução útil para usar em vez de uma VPN S2S, quando você tiver apenas alguns clientes que precisam se conectar a uma rede virtual.

Ao contrário das conexões S2S, as conexões P2S não exigem um endereço IP público local ou um dispositivo VPN. As conexões P2S podem ser usadas com as conexões S2S pelo mesmo gateway de VPN, desde que todos os requisitos de configuração de ambas as conexões sejam compatíveis. Para saber mais sobre conexões Ponto a Site, confira [Sobre VPN Ponto a Site](point-to-site-about.md).


![Exemplo de conexão Ponto de Site do Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Modelos e métodos de implantação para P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>Conexões de VNet para VNet (túnel VPN IPsec/IKE)

Conectar uma rede virtual a outra rede virtual é semelhante a conectar uma rede virtual (Rede Virtual para Rede Virtual) a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. Você pode até combinar a comunicação VNet a VNet com as configurações de conexão de vários sites. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.

As redes virtuais às quais você se conecta podem estar:

* na mesma região ou em regiões diferentes
* na mesma assinatura ou em assinaturas diferentes 
* nos mesmos modelos de implantação ou em modelos diferentes

![Exemplo de conexão VNet a VNet do Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Conexões entre os modelos de implantação

Atualmente, o Azure tem dois modelos de implantação: o clássico e o Resource Manager. Se você já usa o Azure há algum tempo, provavelmente terá as VMs do Azure e as funções de instância em execução em uma Rede Virtual clássica. Suas VMs e instâncias de função mais recentes podem estar em execução em uma Rede Virtual criada no Resource Manager. Você pode criar uma conexão entre as Redes Virtuais para permitir que os recursos em uma rede virtual se comuniquem diretamente com os recursos em outra.

### <a name="vnet-peering"></a>Emparelhamento VNet

Talvez você possa usar o emparelhamento VNet para criar sua conexão, desde que a rede virtual atenda a certos requisitos. O emparelhamento de Rede Virtual não usa um gateway de rede virtual. Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implantação e métodos para VNet a VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (conexão privada)

O ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de conexão privada facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o CRM Online. A conectividade pode ocorrer de uma rede “qualquer para qualquer” (VPN IP), uma rede Ethernet ponto a ponto ou uma conexão cruzada virtual por meio de um provedor de conectividade em uma colocalização.

As conexões de ExpressRoute não passam pela Internet pública. Isso permite que as conexões de ExpressRoute ofereçam mais confiabilidade, mais velocidade, latências menores e muito mais segurança do que as conexões típicas pela Internet.

Uma conexão ExpressRoute usa um gateway de rede virtual como parte da configuração necessária. Em uma conexão ExpressRoute, um gateway de rede virtual é configurado com o tipo de gateway 'ExpressRoute', em vez de 'Vpn'. Embora o tráfego que passa por um circuito do ExpressRoute não seja criptografado por padrão, é possível criar uma solução que permite enviar tráfego criptografado em um circuito ExpressRoute. Para obter mais informações sobre o ExpressRoute, consulte a [Visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Conexões coexistentes Site a Site e de ExpressRoute

O ExpressRoute é uma conexão direta e privada da sua WAN (não pela Internet pública) para Serviços Microsoft, incluindo o Azure. O tráfego da VPN Site a Site é criptografado ao percorer a Internet pública. Poder configurar conexões VPN Site a Site e o ExpressRoute para a mesma rede virtual oferece várias vantagens.

Você pode configurar uma VPN Site a Site como um caminho de failover seguro para o ExpressRoute ou usar VPNs Site a Site para se conectar a sites que não fazem parte de sua rede, mas estão conectados por meio do ExpressRoute. Observe que essa configuração exige dois gateways de rede virtual para a mesma rede virtual, um usando o tipo de gateway 'Vpn' e o outro usando o tipo de gateway 'ExpressRoute'.

![Exemplos de conexões coexistentes do ExpressRoute e do Gateway de VPN](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Coexistência de modelos e métodos de implantação para S2S e ExpressRoute

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Preços

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Para saber mais sobre as SKUs de gateway para Gateway de VPN, veja [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a>Perguntas frequentes

Para perguntas frequentes sobre o gateway de VPN, consulte [Perguntas frequentes sobre o gateway de VPN](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>Próximas etapas

- Exiba as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md) para saber mais.
- Exiba os [Limites de serviço e assinatura](../azure-subscription-service-limits.md#networking-limits).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.
