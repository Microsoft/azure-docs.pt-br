---
title: Gateway de VPN clássico para migração do Gerenciador de Recursos | Microsoft Docs
description: Esta página fornece uma visão geral do gateway de VPN clássico para migração do Gerenciador de Recursos.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: b65b47389611bcc0e5acb3c7ebff672f72a87581
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761551"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Gateway de VPN clássico para migração do Gerenciador de Recursos
Gateways de VPN agora podem ser migrados do modelo de implantação clássico para o Gerenciador de Recursos. É possível ler mais sobre os [Recursos e benefícios](../azure-resource-manager/resource-group-overview.md) do Azure Resource Manager. Neste artigo, detalhamos como migrar de implantações clássicas para o modelo com base no Gerenciador de Recursos mais recente. 

Gateways de VPN são migrados como parte da migração de VNet do clássico para o Gerenciador de Recursos. Essa migração é feita com uma VNet por vez. Não há nenhum requisito adicional em termos de ferramentas ou pré-requisitos para migração. As etapas de migração são idênticas à migração de VNet existente e são documentadas na [página de migração de recursos IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Não há nenhum tempo de inatividade do roteiro de dados durante a migração e, portanto, cargas de trabalho existentes continuariam a funcionar sem perda de conectividade local durante a migração. O endereço IP público associado ao gateway de VPN não é alterado durante o processo de migração. Isso significa que você não precisará reconfigurar seu roteador local quando a migração for concluída.  

O modelo no Gerenciador de Recursos é diferente do modelo clássico e é composto de gateways de rede virtual, gateways de rede local e recursos de conexão. Eles representam o gateway de VPN, o site local representando o espaço de endereço local e a conectividade entre os dois, respectivamente. Quando a migração for concluída, seus gateways não estarão disponíveis no modelo clássico e todas as operações de gerenciamento em gateways de rede virtual, gateways de rede local e objetos de conexão deverão ser executadas usando o modelo do Gerenciador de Recursos.

## <a name="supported-scenarios"></a>Cenários com suporte
Os cenários mais comuns de conectividade VPN são cobertos pela migração do clássico para o Gerenciador de Recursos. Os cenários compatíveis incluem -

* Conectividade ponto a site
* Conectividade site a site com o gateway de VPN conectado à localização local
* Conectividade VNet a VNet entre duas redes VNets usando gateways de VPN
* Várias redes virtuais conectadas à mesma localização local
* Conectividade de vários sites
* VNets com diagrama do túnel forçado

Os cenários não compatíveis incluem -  

* VNet com ExpressRoute Gateway e VPN Gateway não é compatível atualmente.
* Cenários de tráfego em que as extensões de VM estiverem conectadas a servidores locais. As limitações de conectividade VPN de tráfego são detalhadas abaixo.

> [!NOTE]
> A validação CIDR no modelo do Gerenciador de Recursos é mais restrita do que no modelo clássico. Antes de migrar, verifique se os intervalos de endereços do clássico dados seguem um formato CIDR válido antes de iniciar a migração. A CIDR pode ser validada usando qualquer validador de CIDR comum. VNet ou sites locais com intervalos CIDR inválidos resultariam em estado de falha quando migrados.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migração de conectividade VNet a VNet
Conectividade VNet a VNet no clássico foi obtida ao criar uma representação de site local da VNet conectada. Os clientes precisavam criar dois sites locais que representassem as duas VNets que precisavam ser conectadas. Em seguida, elas eram conectadas às VNets correspondentes usando túneis IPsec para estabelecer a conectividade entre as duas VNets. Esse modelo tem desafios de gerenciamento, já que as alterações de intervalo de endereço em uma VNet também devem ser mantidas na representação do site local correspondente. No modelo do Gerenciador de Recursos, essa solução alternativa não é mais necessária. A conexão entre as duas VNets pode ser obtida diretamente usando o tipo de conexão 'Vnet2Vnet' no recurso de conexão. 

![Captura de tela da migração VNet a VNet.](./media/vpn-gateway-migration/migration1.png)

Durante a migração de VNet, detectamos que a entidade conectada ao gateway de VPN da VNet atual é outra VNet e verificamos que, depois de concluída a migração de ambas as VNets, não se vê mais os dois sites locais que representam a outra VNet. O modelo clássico de dois gateways de VPN, dois sites locais e duas conexões entre eles é transformado no modelo do Gerenciador de Recursos com dois gateways de VPN e duas conexões do tipo Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Conectividade VPN de tráfego
É possível configurar gateways de VPN em uma topologia de tal forma que a conectividade local para uma VNet seja obtida pela conexão a outra VNet que esteja conectada diretamente ao local. Trata-se da conectividade VPN de tráfego em que as instâncias na primeira VNet são conectadas aos recursos locais por meio do trânsito para o gateway de VPN na VNet conectada que está conectada diretamente ao local. Para atingir essa configuração no modelo de implantação clássico, você precisaria criar um site local que tivesse agregado prefixos representando a VNet conectada e o espaço de endereço local. Esse site local representacional seria, então, conectado à VNet para obter conectividade de tráfego. Esse modelo clássico também traz desafios de gerenciamento semelhantes, pois qualquer alteração no intervalo de endereço local também deve ser mantida no site local que representa a agregação de VNet e local. A introdução ao suporte BGP em gateways compatíveis do Gerenciador de Recursos com suporte simplifica a gerenciabilidade, pois os gateways conectados aprendem rotas do local sem modificações manuais de prefixos.

![Captura de tela do cenário de roteamento do tráfego.](./media/vpn-gateway-migration/migration2.png)

Como é possível transformar a conectividade VNet a VNet sem a necessidade de sites locais, o cenário de tráfego perde conectividade local para a VNet que estiver indiretamente conectada ao local. A perda de conectividade pode ser reduzida nas duas maneiras a seguir, após a migração ser concluída - 

* Com a habilitação do BGP em gateways de VPN conectados juntos e ao local. A habilitação do BGP restaura a conectividade sem qualquer outra alteração de configuração, pois as rotas são aprendidas e anunciadas entre os gateways de VNet. Observe que a opção de BGP só está disponível em SKUs standard ou superiores.
* O estabelecimento de uma conexão explícita da VNet afetada ao gateway de rede local que representa a localização local. Isso também exigiria a alteração da configuração do roteador local para criar e configurar o túnel IPsec.

## <a name="next-steps"></a>Próximas etapas
Depois de aprender sobre o suporte à migração de gateway de VPN, acesse [migração com suporte de plataforma de recursos de IaaS do clássico para o Gerenciador de Recursos](../virtual-machines/windows/migration-classic-resource-manager-ps.md) para começar.

