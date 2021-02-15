---
title: Considerações de rede - HSM Dedicado do Azure | Microsoft Docs
description: Visão geral das considerações aplicáveis às implantações do HSM Dedicado do Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 3764b261b491c660da16d7989be20742fead1fbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91359147"
---
# <a name="azure-dedicated-hsm-networking"></a>Rede HSM Dedicado do Azure

O HSM Dedicado do Azure dedicado requer um ambiente de rede altamente seguro. Isso ocorre se for da nuvem de Azure de volta para ambiente de TI do cliente (local), usando aplicativos distribuídos ou para cenários de alta disponibilidade. A Rede do Azure fornece isso e há quatro áreas distintas que devem ser abordadas.

- Criar dispositivos HSM dentro de sua Rede Virtual (VNet) do Microsoft Azure
- Conexão local a recursos baseados em nuvem para a configuração e gerenciamento dos dispositivos HSM
- Criar e conectar redes virtuais para a conexão entre recursos de aplicativos e dispositivos HSM
- Como conectar redes virtuais entre regiões para comunicação interna e também para habilitar cenários de alta disponibilidade

## <a name="virtual-network-for-your-dedicated-hsms"></a>Rede virtual para seus HSMs Dedicados

HSMs Dedicados são integrados a uma Rede Virtual e colocados na própria rede privada dos clientes no Azure. Isso permite o acesso aos dispositivos de máquinas virtuais ou recursos de computação na rede virtual.  
Para obter mais informações sobre como integrar serviços do Azure com a rede virtual e os recursos que ele fornece, consulte a documentação dos serviços da [Rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md).

### <a name="virtual-networks"></a>Redes virtuais

Antes de provisionar um dispositivo HSM Dedicado, os clientes primeiro precisarão criar uma rede Virtual no Azure ou usar um que já existe na assinatura dos clientes. A rede virtual define o perímetro de segurança para o Dispositivo HSM Dedicado. Para obter mais informações sobre redes virtuais do Azure, consulte [Documentação da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Sub-redes

As sub-redes segmentam a rede virtual em espaços de endereço separados utilizáveis pelos recursos do Azure colocados neles. HSMs Dedicados são implantados em uma sub-rede em uma rede virtual. Cada dispositivo HSM Dedicado que é implantado na sub-rede do cliente receberá um endereço IP privado dessa sub-rede. A sub-rede na qual o dispositivo do HSM está implantado precisa ser delegada explicitamente para o serviço: Microsoft.HardwareSecurityModules/dedicatedHSMs. Isso concede determinadas permissões para o serviço HSM para implantação na sub-rede. A delegação para os HSMs Dedicados impõe determinadas restrições de política na sub-rede. Grupos de Segurança de Rede (NSGs) e rotas definidas pelo usuário (UDRs) atualmente não têm suporte em sub-redes delegadas. Como resultado, depois que uma sub-rede é delegada aos HSMs Dedicados, ela só pode ser usada para implantar recursos do HSM. Ocorrerá falha na implantação de todos os outros recursos de cliente para a sub-rede.


### <a name="expressroute-gateway"></a>Gateway de ExpressRoute

Um requisito da arquitetura atual é a configuração de um gateway de ER na sub-rede de clientes em que um dispositivo HSM precisa ser colocado para habilitar a integração do dispositivo HSM para o Microsoft Azure. Esse gateway de ER não pode ser utilizado para se conectar a fontes locais para os dispositivos HSM de clientes no Microsoft Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Conexão de sua TI local ao Microsoft Azure

Ao criar recursos baseados em nuvem, isso é um requisito típico para uma conexão privada de volta os recursos de IT locais. No caso do HSM Dedicado, será predominantemente para o software do cliente do HSM configurar os dispositivos HSM e também para atividades, como backups e extrair logs de HSMs para análise. Um ponto de decisão fundamental aqui é a natureza da conexão, pois há opções.  A opção mais flexível é VPN de Site a Site, pois é provável que haja vários recursos de locais que exigem uma comunicação segura com recursos (incluindo HSMs) na nuvem do Azure. Isso exigirá uma organização do cliente para ter um dispositivo VPN para facilitar a conexão. Uma conexão VPN de Site a Site pode ser usada se houver apenas um ponto de extremidade local como uma estação de trabalho de administração.
Para obter mais informações sobre as opções de conectividade, consulte [Opções de planejamento do Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Neste momento, o ExpressRoute não é uma opção de conexão para recursos locais. Também deve-se observar que o Gateway de ExpressRoute usado conforme descrito acima, não é para conexões com a infraestrutura local.

### <a name="point-to-site-vpn"></a>VPN ponto a site

Uma rede Virtual Privada de ponto a site é a forma mais simples de conexão segura para um ponto de extremidade local. Isso pode ser relevante se você pretende ter apenas uma estação de trabalho de administração para HSMs Dedicados com base no Azure.

### <a name="site-to-site-vpn"></a>VPN de site a site

Uma rede Virtual privada de site a site permite a comunicação segura entre os HSMs Dedicados baseados no Azure e sua TI no local. Um motivo para fazer isso é ter um recurso de backup para o HSM local e precisar de uma conexão entre os dois para executar o backup.

## <a name="connecting-virtual-networks"></a>Conexão de redes virtuais

Uma arquitetura de implantação típica para o HSM Dedicado começa com uma única rede virtual e uma sub-rede correspondente no qual os dispositivos HSM são criados e provisionados. Nessa mesma região, pode haver outras redes virtuais e sub-redes para componentes de aplicativos que fariam uso do HSM Dedicado. Para habilitar a comunicação entre essas redes, usamos o Emparelhamento da Rede Virtual do Microsoft Azure.

### <a name="virtual-network-peering"></a>Emparelhamento de rede virtual

Quando houver várias redes virtuais dentro de uma região que precisam acessar recursos uns dos outros, o Emparelhamento da Rede Virtual do Microsoft Azure pode ser usado para criar canais de comunicação seguros entre eles.  O Emparelhamento de rede virtual fornece não apenas proteção às comunicações, mas também garante uma conexão de baixa latência e largura de banda alta entre os recursos no Azure.

![emparelhamento de rede](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Conexões em Regiões do Azure

Os dispositivos HSM têm a capacidade de, por meio de bibliotecas de software, redirecionar o tráfego para um HSM alternativo. O redirecionamento do tráfego é útil se os dispositivos falharem ou ao acessar um dispositivo perdido. Cenários de falha de nível regional podem ser reduzidos pela implantação de HSMs em outras regiões e habilitar a comunicação entre redes virtuais através das regiões.

### <a name="cross-region-ha-using-vpn-gateway"></a>Entre a HA de região usando gateway de VPN

Para aplicativos distribuídos globalmente ou para cenários de alta disponibilidade de failover regional, é necessário conectar redes virtuais entre regiões. Com o HSM Dedicado do Azure, a alta disponibilidade pode ser obtida usando um Gateway de VPN que fornece um túnel seguro entre as duas redes virtuais. Para obter mais informações sobre conexões de Rede Virtual para Rede Virtual usando o Gateway de VPN, consulte o artigo intitulado [O que é o Gateway de VPN?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> O emparelhamento de Rede Virtual global não está disponível em cenários de conectividade entre regiões com os HSMs Dedicados neste momento e, em vez disso, o gateway de VPN deve ser usado. 

![O diagrama mostra duas regiões conectadas por dois gateways V P N. Cada região contém redes virtuais emparelhadas.](media/networking/global-vnet.png)

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes](faq.md)
- [Capacidade de suporte](supportability.md)
- [Alta disponibilidade](high-availability.md)
- [Segurança física](physical-security.md)
- [Monitoring](monitoring.md)
- [Arquitetura de implantação](deployment-architecture.md)