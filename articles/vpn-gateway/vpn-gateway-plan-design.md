---
title: "Planejamento e design de conexões entre locais: Gateway de VPN do Azure | Microsoft Docs"
description: "Saiba mais sobre o planejamento e design de Gateway de VPN para conexões entre locais, híbridas e VNet a VNet"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: d5aaab83-4e74-4484-8bf0-cc465811e757
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2017
ms.author: cherylmc
ms.openlocfilehash: 0ebc3ef4a64432e993dd6ed69766bb64544fe433
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="planning-and-design-for-vpn-gateway"></a>Planejamento e design para o Gateway de VPN

Planejar e projetar suas conexões entre redes virtuais e entre locais pode ser simples ou complicado, dependendo das suas necessidades de rede. Este artigo traz considerações básicas de design e planejamento.

## <a name="planning"></a>Planejamento

### <a name="compare"></a>Opções de conectividade entre locais

Caso você queira conectar seus sites locais com segurança a uma rede virtual, há três maneiras diferentes de fazer isso: Site a Site, Ponto a Site e o ExpressRoute. Compare as diferentes conexões entre locais que estão disponíveis. A opção escolhida pode depender de várias considerações, como:

* Que tipo de taxa de transferência sua solução exige?
* Você deseja se comunicar através da Internet pública por meio de VPN segura ou através de uma conexão privada?
* Você tem um endereço IP público disponível para uso?
* Você planeja usar um dispositivo VPN? Nesse caso, é compatível?
* Você está conectando apenas alguns computadores ou deseja ter uma conexão persistente para o seu site?
* Qual tipo de gateway de VPN é necessário para a solução que você deseja criar?
* Qual SKU de gateway você deve usar?

### <a name="planningtable"></a>Tabela de planejamento

A tabela a seguir pode ajudá-lo a decidir a melhor opção de conectividade para sua solução.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>SKUs do Gateway

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Fluxo de trabalho

A lista a seguir descreve o fluxo de trabalho comum para conectividade de nuvem:

1. Projete e planeje sua topologia de conectividade e liste os espaços de endereço para todas as redes às quais você deseja se conectar.
2. Crie uma rede virtual do Azure. 
3. Criar um gateway de VPN para a rede virtual.
4. Crie e configure as conexões com redes locais ou outras redes virtuais (conforme necessário).
5. Crie e configure uma conexão Ponto a Site para o gateway de VPN do Azure (conforme necessário).

## <a name="design"></a>Design
### <a name="topologies"></a>Topologias de conexão

Comece examinando os diagramas no artigo [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) . O artigo contém diagramas básicos, os modelos de implantação para cada topologia e as ferramentas de implantação disponíveis que você pode usar para implantar sua configuração.

### <a name="designbasics"></a>Noções básicas sobre design

As seções a seguir tratam dos aspectos básicos do gateway de VPN. 

#### <a name="servicelimits"></a>Limites dos serviços de rede

Percorra as tabelas para exibir [limites de serviços de rede](../azure-subscription-service-limits.md#networking-limits). Os limites listados podem afetar o design.

#### <a name="subnets"></a>Sobre sub-redes

Quando estiver criando conexões, você precisa considerar os intervalos de sub-rede. Não pode haver sobreposição dos intervalos de endereço de sub-rede. Uma sub-rede sobreposta ocorre quando uma rede virtual ou local contém o mesmo espaço de endereço que outro local. Isso significa que seus engenheiros de rede para redes locais deverão definir um intervalo para usar o espaço/sub-redes de endereçamento IP do Azure. Você precisa de um espaço de endereço que não esteja sendo usado na rede local.

Também é importante evitar a sobreposição de sub-redes quando você estiver trabalhando com conexões VNet a VNet. Se houver sobreposição das suas sub-redes e um endereço IP existir em ambas as VNets de envio e de destino, as conexões VNet a VNet falharão. O Azure não poderá encaminhar os dados para a outra VNet porque o endereço de destino faz parte da VNet de envio.

Os Gateways de VPN precisam de uma sub-rede específica, chamada sub-rede de gateway. Todas as sub-redes de gateway devem ser nomeadas como GatewaySubnet para funcionar adequadamente. Lembre-se de não pode nomear sua sub-rede de gateway com um nome diferente e não implantar VMs ou qualquer outra coisa para a sub-rede de gateway. Consulte [Sub-redes de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Sobre gateways de rede local

O gateway de rede local geralmente se refere ao seu local. No modelo de implantação clássico, o gateway de rede local era conhecido como um Site da Rede Local. Quando configura um gateway de rede local, você lhe dá um nome, especifica o endereço IP público do dispositivo VPN local e especifica os prefixos de endereço que estão no caminho local. O Azure examina os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que você especificou para o gateway de rede local e encaminha os pacotes adequadamente. Você pode modificar os prefixos de endereço conforme necessário. Para saber mais, confira [Gateways de rede local](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>Sobre tipos de gateway

É crucial selecionar o tipo de gateway correto para sua topologia. Se você selecionar o tipo incorreto, seu gateway não funcionará corretamente. O tipo de gateway especifica como o próprio gateway se conecta e é uma definição de configuração necessária para o modelo de implantação do Gerenciador de Recursos.

Os tipos de gateway são:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>Sobre tipos de conexão

Cada configuração exige um tipo específico de conexão. Os tipos de conexão são:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Sobre tipos de VPN

Cada configuração requer um tipo específico de VPN. Se estiver combinando duas configurações, como a criação de uma conexão Site a Site e uma conexão Ponto a Site com a mesma rede virtual, você deverá usar um tipo VPN que atenda a ambos os requisitos de conexão.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

As tabelas a seguir mostram o tipo VPN conforme mapeado para cada configuração de conexão. Verifique se o tipo de VPN para o gateway corresponde à configuração que você deseja criar. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Dispositivos de VPN e conexões Site a Site

Para configurar uma conexão Site a Site, independentemente do modelo de implantação, você precisará os seguintes itens:

* Um dispositivo VPN que é compatível com os gateways de VPN do Azure
* Um endereço IP IPv4 público que não está protegido por um NAT

Você precisa ter experiência para configurar seu dispositivo VPN ou pedir que alguém o configure para você.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Considere a possibilidade de roteamento de túnel forçado

Para a maioria das configurações, é possível configurar o túnel forçado. O túnel forçado permite redirecionar ou "forçar" todo o tráfego direcionado para a Internet de volta para seu local por meio de um túnel VPN de Site a Site para inspeção e auditoria. Esse é um requisito crítico de segurança para a maioria das políticas de TI empresariais. 

Sem o túnel forçado, o tráfego direcionado para Internet de suas VMs no Azure sempre percorrerão da infraestrutura de rede do Azure diretamente para a Internet, sem a opção para permitir que você inspecione ou audite o tráfego. O acesso não autorizado à Internet pode levar à divulgação de informações ou outros tipos de violações de segurança.

Uma conexão de túnel forçado pode ser configurada em ambos os modelos de implantação e usando ferramentas diferentes. Para obter mais informações, consulte [Configurar o túnel forçado](vpn-gateway-forced-tunneling-rm.md).

**Diagrama do túnel forçado**

![Diagrama de túnel forçado do Gateway de VPN do Azure](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos [Perguntas frequentes sobre o gateway de VPN](vpn-gateway-vpn-faq.md) e [Sobre gateways de VPN](vpn-gateway-about-vpngateways.md) para obter mais informações para ajudá-lo com seu design.

Para obter mais informações sobre configurações de gateway específicas, consulte [Sobre as configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).