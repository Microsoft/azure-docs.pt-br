---
title: Sobre o Gateway de VPN do Azure
description: Saiba o que é um Gateway de VPN e como usá-lo para se conectar às redes virtuais VPN Ponto a Site, VNet a VNet e IPsec/IKE Site a Site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom:
- contperf-fy21q1
- e2e-hybrid
ms.openlocfilehash: b4a2e1b80fe62c5e8f74e35a1a7f61bdddf89c53
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066527"
---
# <a name="what-is-vpn-gateway"></a>O que é o Gateway de VPN?

Um gateway de VPN é um tipo específico de gateway de rede virtual que é usado para enviar tráfego criptografado entre uma rede virtual do Azure e um local na Internet pública. Você também pode usar um gateway de VPN para enviar tráfego criptografado entre as redes virtuais do Azure pela rede da Microsoft. Uma rede virtual pode ter apenas um gateway de VPN. No entanto, você pode criar várias conexões com o mesmo gateway de VPN. Quando você cria várias conexões com o mesmo gateway de VPN, todos os túneis de VPN compartilham a largura de banda de gateway disponível.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>O que é um gateway da rede virtual?

Um gateway de rede virtual é composto por duas ou mais máquinas virtuais implantadas em uma sub-rede específica chamada de *sub-rede de gateway*. As VMs de gateway de rede virtual são configuradas para conter tabelas de roteamento e executar serviços de gateway específicos. Essas VMs são criadas quando você cria o gateway de rede virtual. Não é possível configurar diretamente as VMs que fazem parte do gateway de rede virtual.

Ao configurar um gateway de rede virtual, você define uma configuração que especifica o tipo de gateway. O tipo de gateway determina como o gateway de rede virtual será usado e as ações que o gateway realiza. O tipo de gateway 'VPN' especifica que o tipo de gateway de rede virtual criado é o 'gateway de VPN'. Isso o distingue de um gateway de ExpressRoute, que usa um tipo de gateway diferente. Uma rede virtual pode ter dois gateways de rede virtual: um gateway de VPN e um gateway de ExpressRoute. Para obter mais informações, consulte [Tipos de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwtype).

A criação de um gateway de rede virtual pode levar até 45 minutos para ser concluída. Quando você cria um gateway de rede virtual, as VMs de gateway são implantadas na sub-rede de gateway e definidas com as configurações especificadas. Depois de criar um gateway de VPN, você pode criar uma conexão de túnel de VPN IPsec/IKE entre o gateway de VPN e o outro gateway de VPN (rede virtual a rede virtual), ou criar uma conexão de túnel de VPN IPsec/IKE entre locais, entre o gateway de VPN e um dispositivo VPN local (Site a Site). Você também pode criar uma conexão VPN Ponto a Site (VPN sobre OpenVPN, IKEv2 ou SSTP), que permite a você se conectar à sua rede virtual de um local remoto, como de uma conferência ou em casa.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>Configurando um Gateway de VPN

Uma conexão de gateway VPN conta com vários recursos que são configurados com definições específicas. A maioria dos recursos pode ser configurada separadamente, embora alguns deles devam ser configurados em determinada ordem.

### <a name="design"></a><a name="diagrams"></a>Design

É importante saber que há diferentes configurações disponíveis para conexões de gateway de VPN. Você precisa determinar qual configuração melhor atende às suas necessidades. Por exemplo, as conexões ponto a site, site a site e de ExpressRoute/site a site coexistentes têm diferentes instruções e requisitos de configuração. Para obter informações sobre design e para exibir diagramas de topologia de conexão, confira [Design](design.md).

### <a name="planning-table"></a><a name="planningtable"></a>Tabela de planejamento

A tabela a seguir pode ajudá-lo a decidir a melhor opção de conectividade para sua solução.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Configurações

As configurações que você escolheu para cada recurso são essenciais para a criação de uma conexão bem-sucedida. Para obter informações sobre os recursos individuais e as configurações do Gateway de VPN, consulte [Sobre as configurações do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md). Este artigo contém informações para ajudar você a entender tipos de gateway, SKUs de gateway, tipos de VPN, tipos de conexão, sub-redes de gateway, gateways de rede local e várias outras configurações de recursos que você possa considerar.

### <a name="deployment-tools"></a><a name="tools"></a>Ferramentas de implantação

Você pode começar criando e configurando os recursos usando uma ferramenta de configuração, como o portal do Azure. Você pode decidir trocar para outra ferramenta, como o PowerShell, para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não é possível configurar cada recurso e definição de recursos no portal do Azure. As instruções nos artigos para cada topologia de conexão especificam quando uma ferramenta de configuração específica é necessária.

## <a name="gateway-skus"></a><a name="gwsku"></a>SKUs do Gateway

Ao criar um gateway de rede virtual, especifique a SKU do gateway que você deseja usar. Selecione as SKUs que atendem às suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, recursos e SLAs.

* Para obter mais informações sobre as SKUs do gateway, incluindo recursos compatíveis, produção e teste de desenvolvimento e etapas de configuração, confira o artigo [Configurações do Gateway de VPN – SKUs do Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
* Para obter informações sobre a SKU herdada, confira [Como trabalhar com SKUs herdadas](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>SKUs de gateway pelo túnel, a conexão e a taxa de transferência

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Zonas de Disponibilidade

É possível implantar gateways de VPN em Zonas de Disponibilidade do Azure. Isso traz resiliência, escalabilidade e maior disponibilidade para gateways de rede virtual. A implantação de gateways em Zonas de Disponibilidade do Azure separa de forma física e lógica os gateways em uma região, enquanto protege a conectividade de rede local com o Azure de falhas no nível na zona. Confira [Sobre os gateways de rede virtual com redundância de zona nas Zonas de Disponibilidade do Azure](about-zone-redundant-vnet-gateways.md).

## <a name="pricing"></a><a name="pricing"></a>Preços

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Para saber mais sobre as SKUs de gateway para Gateway de VPN, veja [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a><a name="faq"></a>Perguntas frequentes

Para perguntas frequentes sobre o gateway de VPN, consulte [Perguntas frequentes sobre o gateway de VPN](vpn-gateway-vpn-faq.md).

## <a name="whats-new"></a><a name="new"></a>Novidades

Assine o RSS feed e veja as atualizações mais recentes dos recursos do Gateway de VPN na página [Atualizações do Azure](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway).

## <a name="next-steps"></a>Próximas etapas

- Exiba as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md) para saber mais.
- Exiba os [Limites de serviço e assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.
