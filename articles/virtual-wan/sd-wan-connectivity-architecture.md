---
title: Arquiteturas de conectividade WAN virtual e SD-WAN
titleSuffix: Azure Virtual WAN
description: Saiba mais sobre a interconexão de um SD-WAN privado com a WAN Virtual do Azure
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sukishen
ms.openlocfilehash: ea9ddd05fe6fc745a3eefc29ab4f1d6aababc936
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94564694"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Arquitetura de conectividade SD-WAN com WAN Virtual do Azure

A WAN Virtual do Azure é um serviço de rede que reúne muitos serviços de segurança e conectividade em nuvem com uma única interface operacional. Esses serviços incluem branch (VPN site a site), usuário remoto (VPN ponto a site), conectividade privada (ExpressRoute), conectividade transitiva dentro da nuvem para Vnets, VPN e interconectividade ExpressRoute, roteamento, Firewall do Azure e criptografia para conectividade privada.

Embora a própria WAN Virtual do Azure seja uma SD-WAN (WAN definida pelo software), ela também foi projetada para permitir a interconexão direta com as tecnologias e serviços de SD-WAN baseados no local. Muitos desses serviços são oferecidos por nosso ecossistema de [WAN Virtual](virtual-wan-locations-partners.md) e parceiros de Serviços Gerenciados de Rede do Azure [(MSPs)](../networking/networking-partners-msp.md). As empresas que estão transformando suas WAN privadas em SD-WAN têm opções ao interconectar a SD-WAN privada com a WAN Virtual do Azure. As empresas podem escolher entre estas opções:

* Modelo de interconexão direta
* Modelo de interconexão direta com NVA-in-VWAN-Hub
* Modelo de interconexão indireta
* Modelo de WAN Híbrida gerenciada usando o provedor de serviço gerenciado favorito [MSP](../networking/networking-partners-msp.md)

Em todos esses casos, a interconexão de WAN Virtual com a SD-WAN é semelhante da perspectiva da conectividade, mas pode variar na orquestração e no lado operacional.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Modelo de interconexão direta

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct.png" alt-text="Modelo de interconexão direta":::

Nesse modelo de arquitetura, o CPE (equipamento local do cliente) do branch de WAN é conectado diretamente a hubs de WAN Virtuais por meio de conexões IPsec. O CPE do branch também pode estar conectado a outros branches por meio do SD-WAN privado ou aproveitar a WAN Virtual para conectividade de branch com branch. Os branches que precisam acessar suas cargas de trabalho no Azure poderão acessar de forma direta e segura o Azure por meio de túneis IPsec que terminam nos hubs de WAN Virtuais.

Os parceiros de CPE do SD-WAN podem habilitar a automação para automatizar a conectividade IPsec, normalmente entediante e propensa a erros, de seus respectivos dispositivos CPE. A automação permite que o controlador SD-WAN se comunique com o Azure por meio da API WAN Virtual para configurar os sites de WAN Virtual, bem como enviar por push a configuração de túnel IPsec necessária para CPEs de branch. Consulte [Diretrizes de automação](virtual-wan-configure-automation-providers.md) para obter a descrição da automação de interconexão de WAN Virtual por vários parceiros de SD-WAN.

O CPE de SD-WAN continua sendo o local onde a otimização de tráfego, bem como a seleção de caminho, é implementada e imposta. 

Nesse modelo, uma otimização de tráfego proprietário do fornecedor com base em características de tráfego em tempo real pode não ter suporte, pois a conectividade com a WAN Virtual ocorre por IPsec, e a VPN IPsec termina no gateway de VPN de WAN Virtual. Por exemplo, a seleção de caminho dinâmico no CPE de branch é viável porque o dispositivo de branch troca várias informações de pacote de rede com outro nó de SD-WAN, identificando, assim, o melhor link a ser usado para vários tráfegos priorizados dinamicamente no branch. Esse recurso pode ser útil em áreas em que a otimização de última quilometragem (branch para o POP da Microsoft mais próximo) é necessária.

Com a WAN Virtual, os usuários podem obter a Seleção de Caminho do Azure, que é a seleção de caminho baseada em política entre vários links de ISP do CPE de branch para gateways VPN da WAN Virtual. A WAN Virtual permite a configuração de vários links (caminhos) do mesmo CPE de branch de SD-WAN; cada link representa uma conexão de túnel duplo de um IP público exclusivo do CPE de SD-WAN para duas instâncias diferentes do gateway VPN de WAN Virtual do Azure. Os fornecedores de SD-WAN podem implementar o caminho ideal para o Azure, com base nas políticas de tráfego definidas pelo mecanismo de política nos links de CPE. Na extremidade do Azure, todas as conexões recebidas são tratadas igualmente.

## <a name="direct-interconnect-model-with-nva-in-vwan-hub"></a><a name="direct"></a>Modelo de interconexão direta com NVA-in-VWAN-Hub

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct-nva.png" alt-text="Modelo de interconexão direta com NVA-in-VWAN-Hub":::

Esse modelo de arquitetura dá suporte à implantação de uma [solução de virtualização de rede de terceiros (NVA) diretamente no Hub virtual](./about-nva-hub.md). Isso permite que os clientes que desejam conectar seu CPE de ramificação à mesma marca NVA no Hub virtual, para que eles possam aproveitar os recursos de SD-WAN de ponta a ponta ao se conectarem às cargas de trabalho do Azure. 

Vários parceiros de WAN virtuais trabalharam para fornecer uma experiência que configura o NVA automaticamente como parte do processo de implantação. Depois que o NVA tiver sido provisionado no Hub virtual, qualquer configuração adicional que possa ser necessária para o NVA deverá ser feita por meio do portal do NVA Partners ou do aplicativo de gerenciamento. O acesso direto ao NVA não está disponível. Os NVAs que estão disponíveis para serem implantados diretamente no Hub de WAN virtual do Azure são projetados especificamente para serem usados no Hub virtual. Para parceiros que dão suporte a NVA no Hub do VWAN, bem como seus guias de implantação, consulte o artigo [parceiros de WAN virtual](virtual-wan-locations-partners.md#partners-with-integrated-virtual-hub-offerings) .

O CPE de SD-WAN continua sendo o local onde a otimização de tráfego, bem como a seleção de caminho, é implementada e imposta.
Nesse modelo, a otimização do tráfego proprietário do fornecedor com base em características de tráfego em tempo real é suportada porque a conectividade com a WAN virtual é por meio do SD-WAN NVA no Hub.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Modelo de interconexão indireta

:::image type="content" source="./media/sd-wan-connectivity-architecture/indirect.png" alt-text="Modelo de interconexão indireta":::

Nesse modelo de arquitetura, os CPEs de branch de SD-WAN estão indiretamente conectados a hubs de WAN Virtual. Como mostra a figura, um CPE de SD-WAN virtual é implantado em uma VNet corporativa. Esse CPE virtual é, por sua vez, conectada aos hubs de WAN Virtual usando IPsec. O CPE virtual serve como um gateway SD-WAN no Azure. Os branches que precisam acessar suas cargas de trabalho no Azure poderão acessá-los por meio do gateway v-CPE.

Como a conectividade com o Azure ocorre por meio do gateway de v-CPE (NVA), todo o tráfego de e para VNets de carga de trabalho do Azure para outros branches de SD-WAN passa pelo NVA. Nesse modelo, o usuário é responsável por gerenciar e operar o NVA de SD-WAN, incluindo alta disponibilidade, escalabilidade e roteamento.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Modelo de WAN Híbrido gerenciado

:::image type="content" source="./media/sd-wan-connectivity-architecture/hybrid.png" alt-text="Modelo de WAN Híbrido gerenciado":::

Nesse modelo de arquitetura, as empresas podem aproveitar um serviço SD-WAN gerenciado oferecido por um parceiro de MSP (provedor de serviços gerenciados). Esse modelo é semelhante aos modelos diretos ou indiretos descritos acima. No entanto, nesse modelo, o design, a orquestração e as operações de SD-WAN são entregues pelo provedor de SD-WAN.

[Os parceiros de MSP de Rede do Azure](../networking/networking-partners-msp.md) podem usar o [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) para implementar o SD-WAN e o serviço de WAN Virtual na assinatura do Azure do cliente empresarial, bem como operar a WAN híbrida de ponta a ponta em nome do cliente. Talvez esses MSPs também possam implementar o Azure ExpressRoute na WAN Virtual e operá-lo como um serviço gerenciado de ponta a ponta.

## <a name="additional-information"></a>Informações adicionais

* [Perguntas frequentes sobre inclusão](virtual-wan-faq.md)
* [Solução de Conectividade Remota](work-remotely-support.md)