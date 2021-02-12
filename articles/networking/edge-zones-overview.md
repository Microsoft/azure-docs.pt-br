---
title: Sobre a visualização da zona do Azure Edge
description: 'Saiba mais sobre as ofertas de computação de borda da Microsoft: zona de borda do Azure.'
services: vnf-manager
author: cherylmc
ms.service: vnf-manager
ms.topic: article
ms.date: 01/13/2021
ms.author: cherylmc
ms.openlocfilehash: 04555303d5128db6c183d27a0c5fcb69063fdc28
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185403"
---
# <a name="about-azure-edge-zone-preview"></a>Sobre a visualização da zona do Azure Edge

A zona de borda do Azure é uma família de ofertas de Microsoft Azure que permite o processamento de dados próximo ao usuário. Você pode implantar VMs, contêineres e outros serviços do Azure selecionados em zonas de borda para atender aos requisitos de baixa latência e alta taxa de transferência dos aplicativos.

Os cenários de caso de uso típicos para zonas de borda incluem:

- Comando e controle em tempo real em robótica.
- Análise em tempo real e inferência por meio de inteligência artificial e aprendizado de máquina.
- Visão da máquina.
- Renderização remota para cenários mistos de realidade e VDI.
- Jogos de vários participantes.
- Streaming de mídia e distribuição de conteúdo.
- Vigilância e segurança.

Há três tipos de zonas de borda do Azure:

- Azure Edge Zone
- Zonas do Azure Edge com a transportadora
- Zonas de borda privada do Azure

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

![Azure Edge Zone](./media/edge-zones-overview/edge-zones.png "Azure Edge Zone")

As zonas do Azure Edge são extensões de pequena superfície do Azure colocadas em centros de população que estão longe das regiões do Azure. As zonas do Azure Edge dão suporte a VMs, contêineres e um conjunto selecionado de serviços do Azure que permitem que você execute aplicativos sensíveis à latência e com uso intensivo de taxa de transferência próximos aos usuários finais. As zonas do Azure Edge fazem parte da rede global da Microsoft. Eles fornecem conectividade segura, confiável e de alta largura de banda entre aplicativos executados na zona de borda perto do usuário. As zonas do Azure Edge são de propriedade e operadas pela Microsoft. Você pode usar o mesmo conjunto de ferramentas do Azure e o mesmo portal para gerenciar e implantar serviços em zonas de borda.

Os casos de uso típicos incluem:

- Jogos e streaming de jogos.
- Streaming de mídia e distribuição de conteúdo.
- Análise em tempo real e inferência por meio de inteligência artificial e aprendizado de máquina.
- Renderização para realidade misturada.

As zonas do Azure Edge estarão disponíveis nas seguintes áreas de metrô:

- Nova Iorque, NY
- Los Angeles, CA
- Miami, FL

[Contate a equipe da zona de borda](https://aka.ms/EdgeZones) para obter mais informações.

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Zonas do Azure Edge com a transportadora

![Zonas de borda com a transportadora](./media/edge-zones-overview/edge-carrier.png "Zonas de borda com a transportadora")

As zonas do Azure Edge com a transportadora são extensões de pequena superfície do Azure que são colocadas nos datacenters dos operadores móveis em centros de população. A zona de borda do Azure com a infraestrutura da transportadora é colocada um salto para fora da rede 5G da operadora móvel. Esse posicionamento oferece latência de menos de 10 milissegundos para aplicativos de dispositivos móveis.

As zonas de borda do Azure com a transportadora são implantadas nos data centers dos operadores móveis e conectadas à rede global da Microsoft. Eles fornecem conectividade segura, confiável e de alta largura de banda entre aplicativos que são executados perto do usuário. Os desenvolvedores podem usar o mesmo conjunto de ferramentas familiares para criar e implantar serviços nas zonas de borda.

Os casos de uso típicos incluem:

- Jogos e streaming de jogos.
- Streaming de mídia e distribuição de conteúdo.
- Análise em tempo real e inferência por meio de inteligência artificial e aprendizado de máquina.
- Renderização para realidade misturada.
- Dispositivos móveis conectados.
- Tele-remédio.

As zonas de borda serão oferecidas em parceria com os seguintes operadores:

- ÀS&T (Atlanta, Dallas e Los Angeles)

Os ISVs que trabalham em aplicativos otimizados e escalonáveis conectados a redes 5G agora podem usar o novo local de visualização de Los Angeles de zonas de borda do Azure com no&T ao criar e experimentar plataformas de latência extremamente baixa, cenários móveis e conectados. Registre-se no programa pioneiro para tirar proveito da conectividade segura e de alta largura de banda.

[Contate a equipe da zona de borda](https://aka.ms/EdgeZones) para obter mais informações.

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Zonas de borda privada do Azure

![Zonas de borda privada](./media/edge-zones-overview/private-edge.png "Zonas de borda privada")

As zonas de borda privada do Azure são extensões de pequena superfície do Azure que são colocadas no local. A zona de borda privada do Azure é baseada na plataforma de [borda Azure Stack](https://azure.microsoft.com/products/azure-stack/edge/) . Ele permite acesso de baixa latência aos serviços de computação e armazenamento implantados localmente. A zona de borda privada também permite que você implante aplicativos de ISVs e VNFs (funções de rede virtualizadas) como [aplicativos gerenciados do Azure](https://azure.microsoft.com/services/managed-applications/) , juntamente com máquinas virtuais e contêineres locais. Esses VNFs podem incluir núcleos de pacotes móveis, roteadores, firewalls e dispositivos SD-WAN. A zona de borda privada do Azure vem com uma solução de orquestração nativa de nuvem que permite gerenciar os ciclos de vida de VNFs e aplicativos do portal do Azure.

A zona de borda privada do Azure permite desenvolver e implantar aplicativos locais usando as mesmas ferramentas conhecidas que você usa para criar e implantar aplicativos no Azure.

Ele também permite que você:

- Execute redes móveis privadas (LTE particular, 5G privado).
- Implemente funções de segurança como firewalls.
- Estenda suas redes locais entre várias ramificações e o Azure usando dispositivos SD-WAN nos mesmos dispositivos de zona de borda privada e gerencie-os do Azure.

Os casos de uso típicos incluem:

- Comando e controle em tempo real em robótica.
- Análise em tempo real e inferência com inteligência artificial e aprendizado de máquina.
- Visão da máquina.
- Renderização remota para cenários mistos de realidade e VDI.
- Vigilância e segurança.

Temos um rico ecossistema de fornecedores de VNF, ISVs e parceiros MSP para permitir soluções de ponta a ponta que usam zonas de borda privadas. [Contate a equipe da zona de borda privada](https://aka.ms/EdgeZonesPartner) para obter mais informações.

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>Parceiros de zona de borda privada

![Parceiros de zona de borda privada](./media/edge-zones-overview/partners.png "Parceiros de zonas de borda privada")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>VNFs (funções de rede virtualizadas)

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Núcleo de pacotes evoluídos virtualizados (vEPC) para redes móveis

- [Redes afirmadas](https://www.affirmednetworks.com/)
- [Celona](https://www.celona.io/azure-edge)
- [Druid software](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nuvem de automação digital Nokia](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Parceiros de rádio móvel

- [Celona](https://www.celona.io/azure-edge)
- [Commscope Ruckus](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-fornecedores de WAN

- [128 Technology](https://www.128technology.com/)
- [](https://netfoundry.io/)
- [Redes Nuage da Nokia](https://www.nuagenetworks.net/)
- [Redes versas](https://www.versa-networks.com/)
- [VMware SD – WAN por Velocloud](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>Fornecedores de roteador

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Fornecedores de firewall

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>Provedores de soluções gerenciadas: operadores móveis e integradores de sistema global (GSIs)

| GSIs e operadores | Operadoras móveis |
| --- | --- |
| Amdocs                       | Etisalat             |
| Torre americana               | Comunicações NTT   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| Sem fio federado           | SK Telecom           |
| Infosys                      | Telefonica           |
| Mahindra de tecnologia                | Telstra              |
|                              | Vodafone             |

[Entre em contato com a equipe da zona de borda privada](https://aka.ms/EdgeZonesPartner) para obter informações sobre como se tornar um parceiro.

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>Soluções de zona de borda privada

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Rede móvel privada em zonas de borda privada

![Rede móvel privada em zonas de borda privada](./media/edge-zones-overview/mobile-networks.png "Rede móvel privada em zonas de borda privada")

Agora você pode implantar uma rede móvel privada em zonas de borda privadas. As redes móveis privadas habilitam a latência ultra baixa, alta capacidade e a rede sem fio confiável e segura necessária para aplicativos críticos para os negócios.

As redes móveis privadas podem habilitar cenários como:
- Comando e controle de veículos guiados automatizados (AGVs) em depósitos.
- Comunicação em tempo real entre os robôs no Smart factories.
- Realidade aumentada e aplicativos de borda de realidade virtual.

A função de rede vEPC (núcleo de pacotes evoluídos) virtualizada é o cérebro de uma rede móvel privada. Agora você pode implantar um vEPC em zonas de borda privadas. Para obter uma lista de parceiros do vEPC que estão disponíveis em zonas de borda privada, consulte [ISVs do vEPC](#vEPC).

A implantação de uma solução de rede móvel privada em zonas de borda privada requer outros componentes, como pontos de acesso móvel, cartões SIM e outros VNFs como roteadores. O acesso a um espectro licenciado ou não licenciado é essencial para configurar uma rede móvel privada. E você pode precisar de ajuda com o planejamento de RF, layout físico, instalação e suporte. Para obter uma lista de parceiros, consulte [parceiros de rádio móvel](#mobile-radio).

A Microsoft fornece um ecossistema de parceiros que pode ajudar com todos os aspectos desse processo. Os parceiros podem ajudar no planejamento da rede, na compra de dispositivos necessários, na configuração de hardware e no gerenciamento da configuração do Azure. Um conjunto de parceiros validados totalmente integrados à Microsoft garantem que sua solução será confiável e fácil de usar. Você pode se concentrar nos cenários principais e contar com a Microsoft e seus parceiros para ajudar no restante.

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-WAN em zonas de borda privada

![SD-WAN em zonas de borda privada](./media/edge-zones-overview/sd-wan.png "SD-WAN em zonas de borda privada")

O SD-WAN permite que você crie WANs (redes de longa distância) de nível empresarial que têm estes benefícios:

- Maior largura de banda
- Acesso de alto desempenho à nuvem
- Inserção de serviço
- Confiabilidade
- Gerenciamento de política
- Ampla visibilidade da rede

O SD-WAN fornece conectividade de filial sem interrupções, que é orquestrada de controladores centrais redundantes a um custo menor de propriedade.
O SD-WAN em zonas de borda privada permite que você passe de um modelo centrado em Capex para um modelo de software como serviço (SaaS) para reduzir os orçamentos de ti. Você pode usar sua escolha de parceiros SD-WAN, Orchestrator ou Controller para habilitar novos serviços e propagá-los em toda a rede imediatamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, entre em contato com as seguintes equipes:

* [Equipe da zona de borda](https://aka.ms/EdgeZones)
* [Equipe de zona de borda privada, para se tornar um parceiro](https://aka.ms/EdgeZonesPartner)
