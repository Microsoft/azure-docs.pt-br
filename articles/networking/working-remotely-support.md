---
title: Trabalhando remotamente usando os serviços de rede do Azure
description: Esta página descreve como você pode usar os serviços de rede do Azure que estão disponíveis para habilitar o trabalho remotamente e como reduzir os problemas de tráfego resultantes do aumento do número de pessoas trabalhando remotamente.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 835be5b867826d75732c9482743ad6a4ca2900e7
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231835"
---
# <a name="working-remotely-using-azure-networking-services"></a>Trabalhando remotamente usando os serviços de rede do Azure

>[!NOTE]
> Este artigo descreve como você pode aproveitar os serviços de rede do Azure, a rede da Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e atenuar os problemas de rede que você pode estar enfrentando devido à crise COVID-19.

Este artigo descreve as opções disponíveis para que as organizações configurem o acesso remoto para seus usuários ou complementem suas soluções existentes com capacidade adicional durante períodos de pico de utilização. Os arquitetos de rede enfrentam os seguintes desafios:

- Resolva um aumento na utilização da rede.
- Forneça conectividade segura e confiável para mais funcionários de sua empresa e clientes.
- Forneça conectividade para locais remotos em todo o mundo.

Nem todas as redes (por exemplo, WAN privada e redes de núcleo corporativo) enfrentam o congestionamento do pico de carga de trabalho remoto. Os afunilamentos são normalmente relatados apenas em redes de banda larga doméstica e gateways de VPN de redes locais de corporações.

Os planejadores de rede podem ajudar a facilitar os gargalos e aliviar o congestionamento da rede, tendo em mente que diferentes tipos de tráfego precisam de prioridades de tratamento de rede diferentes e por um redirecionamento/distribuição de carga inteligente. Por exemplo, o tráfego de tele-medecine em tempo real da interação do doutor-paciente é de alta importância e sensível a atraso/variação. Enquanto que a replicação do mesmo tráfego entre os armazenamentos não é sensível ao atraso. O tráfego anterior deve ser roteado por meio do caminho de rede ideal com maior qualidade de serviço; enquanto é aceitável rotear o tráfego mais tarde por meio da rota abaixo do ideal.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Compartilhando nossas práticas recomendadas – a rede do Azure foi projetada para elasticidade e alta disponibilidade

O Azure foi projetado para resistir a alterações repentinas na utilização dos recursos e pode ajudar muito durante períodos de pico de utilização. Além disso, a Microsoft mantém e opera uma das maiores redes de mundos. A rede da Microsoft foi projetada para alta disponibilidade que pode resistir a diferentes tipos de falha: de um único elemento de rede falha na falha de uma região inteira.

A rede da Microsoft foi projetada para atender aos requisitos e fornecer um desempenho ideal para diferentes tipos de tráfego de rede, incluindo o atraso do tráfego de multimídia sensível ao Skype e às equipes, CDN, análise de Big Data em tempo real, armazenamento do Azure, Bing e Xbox. Para fornecer um desempenho ideal para diferentes tipos de tráfego, a rede da Microsoft atrai todo o tráfego que está destinado a-ou aguardando trânsito por meio de seus recursos o mais próximo possível da origem do tráfego.

>[!NOTE] 
>Usar os recursos de rede do Azure descritos abaixo utiliza o comportamento de atração de tráfego da rede global da Microsoft para fornecer uma melhor experiência de rede do cliente. O comportamento de atração de tráfego da rede da Microsoft ajuda a carregar o tráfego assim que possível nas redes da primeira/última quilometragem que podem sofrer congestionamentos durante períodos de pico de utilização.
>

## <a name="enable-employees-to-work-remotely"></a>Permitir que os funcionários trabalhem remotamente

O gateway de VPN do Azure dá suporte a conexões VPN de P2S (ponto a site) e de site a site (S2S). Usando o gateway de VPN do Azure, você pode dimensionar as conexões do seu funcionário para acessar com segurança os recursos implantados do Azure e os recursos locais. Para obter mais informações, consulte [como permitir que os usuários trabalhem remotamente](../vpn-gateway/work-remotely-support.md). 

Se você estiver usando SSTP (Secure Sockets Tunneling Protocol), o número de conexões simultâneas será limitado a 128. Para obter um número maior de conexões, sugerimos a transição para OpenVPN ou IKEv2. Para obter mais informações, consulte [transição para o protocolo OpenVPN ou IKEv2 do SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Para acessar seus recursos implantados no Azure, os desenvolvedores remotos poderiam usar a solução de bastiões do Azure, em vez da conexão VPN, para obter acesso seguro ao shell (RDP ou SSH) sem a necessidade de IPs públicos nas VMs que estão sendo acessadas. Para obter mais informações, consulte [trabalhar remotamente usando a bastiões do Azure](../bastion/work-remotely-support.md).

Para agregar conexão VPN de grande escala, para dar suporte a conexões qualquer para qualquer entre recursos em locais globais diferentes locais, em diferentes redes virtuais de Hub regional e spoke e para otimizar a utilização de várias redes de banda larga doméstica, você pode usar a WAN virtual do Azure. Para obter mais informações, consulte [lutando para atender às necessidades de trabalho de casa? Aqui está o local em que a WAN virtual do Azure pode ajudar](../virtual-wan/work-remotely-support.md).

Outra maneira de dar suporte a uma força de trabalho remota é implantar uma VDI (Virtual Desktop Infrastructure) hospedada em sua rede virtual do Azure, protegida com um firewall do Azure. Por exemplo, a área de trabalho virtual do Windows (WVD) é um serviço de desktop e de virtualização de aplicativo que é executado no Azure. Com a área de trabalho virtual do Windows, você pode configurar um ambiente escalonável e flexível na sua assinatura do Azure sem a necessidade de executar servidores de gateway adicionais. Você é responsável somente pelas máquinas virtuais WVD em sua rede virtual. Para obter mais informações, consulte [suporte ao trabalho remoto do firewall do Azure](../firewall/remote-work-support.md). 

O Azure também tem um conjunto avançado de parceiros de sistema de eco. Nossos parceiros da rede virtual appliances no Azure também podem ajudar a dimensionar a conectividade VPN. Para obter mais informações, consulte [Considerações sobre NVA (solução de virtualização de rede) para trabalho remoto](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Estender a conexão dos funcionários para acessar recursos distribuídos globalmente

Os serviços do Azure a seguir podem ajudar a permitir que os funcionários acessem seus recursos distribuídos globalmente. Seus recursos podem estar em qualquer uma das regiões do Azure, redes locais ou até mesmo em outras nuvens públicas ou privadas. 

- **Emparelhamento de rede virtual do Azure**: se você implantar seus recursos em mais de uma região do Azure e/ou se você agregar a conectividade de funcionários de trabalho remotamente usando várias redes virtuais, você poderá estabelecer a conectividade entre as várias redes virtuais do Azure usando o emparelhamento de rede virtual. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure][VNet-peer].

- **Solução baseada em VPN do Azure**: para seus funcionários remotos conectados ao Azure por meio de P2S ou VPN S2S, você pode habilitar o acesso a redes locais Configurando a VPN S2S entre suas redes locais e o gateway de VPN do Azure. Para obter mais informações, consulte [criar uma conexão site a site][S2S].

- **ExpressRoute**: usando o emparelhamento privado do expressroute, você pode habilitar a conectividade privada entre as implantações do Azure e a infraestrutura local ou sua infraestrutura em uma instalação de colocalização. O ExpressRoute, por meio do emparelhamento da Microsoft, também permite acessar pontos de extremidade públicos na Microsoft a partir de sua rede local. As conexões de ExpressRoute não passam pela Internet pública. Eles oferecem conectividade segura, confiabilidade, maior taxa de transferência, com latências menores e consistentes do que conexões típicas pela Internet. Para obter mais informações, confira [Visão geral do ExpressRoute][ExR]. Aproveitar seu provedor de rede existente que já faz parte do nosso [ecossistema de parceiros do ExpressRoute][ExR-eco] pode ajudar a reduzir o tempo de obtenção de conexões de largura de banda grande para a Microsoft.  Usando o [ExpressRoute Direct][ExR-D] , você pode conectar diretamente sua rede local ao backbone da Microsoft. O ExpressRoute Direct oferece duas opções de taxa de linha diferentes de 10 Gbps ou 100 Gbps duplos. 

- **Wan virtual do Azure**: a WAN virtual do Azure permite a interoperabilidade direta entre as conexões VPN e os circuitos do ExpressRoute. Como mencionado anteriormente, a WAN virtual do Azure também oferece suporte a conexões qualquer para qualquer entre recursos em locais globais diferentes no local, em diferentes redes virtuais de Hub regional e spoke

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Dimensionar a conectividade do cliente para recursos front-end

Durante os momentos em que mais pessoas ficarem online, muitos sites corporativos terão o maior tráfego de clientes. Aplicativo Azure gateway pode ajudar a gerenciar essa carga de trabalho de front-end aumentada. Para obter mais informações, consulte [suporte a alto tráfego do gateway de aplicativo](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Suporte da Microsoft para tráfego de várias nuvens

Para suas implantações em outras nuvens públicas, a Microsoft pode fornecer conectividade global. A WAN virtual do Azure, VPN ou ExpressRoute pode ajudar nesse sentido. Para estender a conectividade do Azure para outras nuvens, você pode configurar a VPN S2S entre as duas nuvens. Você também pode estabelecer a conectividade do Azure com outras nuvens públicas usando o ExpressRoute. O Oracle Cloud faz parte do ecossistema de parceiros do ExpressRoute. Você pode [Configurar uma interconexão direta entre o Azure e a infraestrutura de nuvem do Oracle][Az-OCI]. A maioria dos provedores de serviços que fazem parte do ecossistema de parceiros do ExpressRoute também oferecem conectividade privada com outras nuvens públicas. Aproveitando esses provedores de serviços, você pode estabelecer conectividade privada entre suas implantações no Azure e em outras nuvens por meio do ExpressRoute.

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir discutem como diferentes recursos de rede do Azure podem ser usados para dimensionar os usuários para trabalhar remotamente:

| **Artigo** | **Descrição** |
| --- | --- |
| [Como permitir que os usuários trabalhem remotamente](../vpn-gateway/work-remotely-support.md) | Examine as opções disponíveis para configurar o acesso remoto para usuários ou para complementar suas soluções existentes com capacidade adicional para sua organização.|
| [Dificuldade para atender às necessidades de residências? Aqui está onde a WAN virtual do Azure pode ajudar](../virtual-wan/work-remotely-support.md) | Use a WAN virtual do Azure para atender às necessidades de conectividade remota da sua organização.|
| [Suporte a alto tráfego do Gateway de Aplicativo](../application-gateway/high-traffic-support.md) | Use o gateway de aplicativo com o WAF (firewall do aplicativo Web) para uma maneira escalonável e segura de gerenciar o tráfego para seus aplicativos Web. |
| [Considerações de NVA (solução de virtualização de rede) para trabalho remoto](../vpn-gateway/nva-work-remotely-support.md)|Examine as diretrizes sobre como aproveitar o NVAs no Azure para fornecer soluções de acesso remoto. |
| [Transição para o protocolo OpenVPN ou IKEv2 do SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md) | Supere o limite de conexão simultânea de 128 do SSTP fazendo a transição para o protocolo OpenVPN ou IKEv2.|
| [Trabalhando remotamente usando a bastiões do Azure](../bastion/work-remotely-support.md) | Fornecer conectividade RDP/SSH segura e direta para máquinas virtuais na rede virtual do Azure, diretamente no portal do Azure, sem o uso de um endereço IP público. |
| [Usando o Azure ExpressRoute para criar conectividade híbrida para dar suporte a usuários remotos](../expressroute/work-remotely-support.md) | Use o ExpressRoute para conectividade híbrida para permitir que os usuários em sua organização trabalhem remotamente.|
| [Suporte ao trabalho remoto do firewall do Azure](../firewall/remote-work-support.md)|Proteja seus recursos de rede virtual do Azure usando o Firewall do Azure. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md