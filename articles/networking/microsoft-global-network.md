---
title: Rede global da Microsoft – Azure
description: Descreve como a Microsoft cria sua rede global rápida e confiável
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: c064a5afee8db0c747273ab1c38c901a6f10ef98
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876142"
---
# <a name="microsoft-global-network"></a>Rede global da Microsoft

A Microsoft possui e opera uma das maiores redes de backbone do mundo. Essa arquitetura global e sofisticada, abrangendo mais de 100.000 milhas, conecta nossos data centers e clientes. 
 
Todos os dias, os clientes em todo o mundo se conectam e passam trilhões de solicitações para Microsoft Azure, Bing, Dynamics 365, Office 365, XBox e muitos outros. Independentemente do tipo, os clientes esperam a confiabilidade e a capacidade de resposta instantâneas de nossos serviços. 
 
A WAN ( [rede global da Microsoft](https://azure.microsoft.com/global-infrastructure/global-network/) ) é uma parte central do fornecimento de uma ótima experiência em nuvem. Conectando nossos [data centers](https://azure.microsoft.com/global-infrastructure/) da Microsoft em 54 regiões do Azure e grande malha de nós de borda, posicionados estrategicamente em todo o mundo, nossa rede global oferece a disponibilidade, a capacidade e a flexibilidade para atender a qualquer demanda.

![Rede global da Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Obter a rede de nuvem Premium
 
Optar pela [melhor experiência possível](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) é fácil quando você usa o Microsoft Cloud. A partir do momento em que o tráfego do cliente entra em nossa rede global por meio de nossos nós de borda estrategicamente posicionados, seus dados viajam por rotas otimizadas perto da velocidade da luz. Isso garante a latência ideal para o melhor desempenho. Esses nós de borda, todos interconectados a mais de 3500 parceiros de Internet exclusivos (colegas) por meio de milhares de conexões em mais de 145 locais, fornecem a base de nossa estratégia de interconexão. 
 
Se estiver se conectando de Londres a Tóquio ou de Washington DC a Los Angeles, o desempenho da rede será quantificado e afetado por coisas como latência, tremulação, perda de pacotes e taxa de transferência.  Na Microsoft, preferimos e usamos interconexões diretas em oposição aos links de trânsito, isso mantém o tráfego de resposta simétrico e ajuda a manter saltos, partes emparelhadas e caminhos o mais curto e simples possível. 

Por exemplo, se um usuário em Londres tentar acessar um serviço em Tokyo, o tráfego da Internet entrará em uma de nossas bordas em Londres, passará pela WAN da Microsoft pela França, nossos caminhos de trans-Saudita entre a Europa e a Índia e, em seguida, para o Japão onde o serviço está hospedado. O tráfego de resposta é simétrico. Às vezes, isso é chamado de [Roteamento](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) de irfriar, o que significa que o tráfego permanece na rede da Microsoft o mais longo possível antes de sua entrega.  
  
Então, isso significa qualquer e todo o tráfego ao usar os serviços da Microsoft? Sim, qualquer tráfego entre data centers, dentro de Microsoft Azure ou entre serviços da Microsoft, como máquinas virtuais, Office 365, XBox, bancos de dados SQL, armazenamento e redes virtuais, é roteado em nossa rede global e nunca pela Internet pública, para garantir o ideal desempenho e integridade.  
 
Grandes investimentos em capacidade de fibra e diversidade em caminhos metro, terrestre e submarino são cruciais para manter o nível de serviço consistente e alto, ao mesmo tempo que incentiva o crescimento extremo de nossa nuvem e serviços online. Os acréscimos recentes à nossa rede global são o nosso cabo [Marea](https://www.submarinecablemap.com/#/submarine-cable/marea) submarino, o primeiro OLS (Open line System) do setor em Subsea, entre Bilbao, Espanha e Virgínia, Virgínia, EUA, bem como o [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) entre Nova York, EUA e Dublin, Irlanda e [novo Pacífico cruzado (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) entre Tóquio, Japão e Portland, Oregon, EUA. 
 

## <a name="our-network-is-your-network"></a>Nossa rede é a sua rede

Colocamos duas décadas de experiência, juntamente com grandes investimentos na rede, para garantir o desempenho ideal o tempo todo. As empresas podem aproveitar ao máximo nossos ativos de rede e criar arquiteturas de sobreposição avançadas na parte superior. 
 
O Microsoft Azure oferece o mais rico portfólio de serviços e recursos, permitindo que os clientes compilem, expandam e atendam aos requisitos de rede de maneira rápida e fácil em qualquer lugar. Nossa família de serviços de conectividade abrange o emparelhamento de rede virtual entre regiões, arquiteturas híbridas e de ponto a site e em nuvem, bem como cenários de trânsito de IP global.  Para empresas que procuram conectar seu próprio Datacenter ou rede ao Azure, ou clientes com grandes ingestão de dados ou necessidades de trânsito, o [expressroute](../expressroute/expressroute-introduction.md)e o [expressroute Direct](../expressroute/expressroute-erdirect-about.md) fornecem opções de até 100 Gbps de largura de banda, diretamente em A rede global da Microsoft nos locais de emparelhamento em todo o mundo.  
 
O [ExpressRoute alcance global](../expressroute/expressroute-global-reach.md) foi projetado para complementar a implementação de WAN do provedor de serviços e conectar seus sites locais em todo o mundo. Se você executar uma operação global, poderá usar o ExpressRoute Alcance Global em conjunto com seus provedores de serviços preferenciais e locais para conectar todos os seus sites globais usando a rede global da Microsoft. Expandir sua nova rede na nuvem (WAN) para abranger grandes números de sites de filiais pode ser realizado por meio da WAN virtual do Azure, que traz a capacidade de conectar diretamente suas ramificações à rede global da Microsoft com dispositivos VPN SDWAN & (ou seja, O equipamento local do cliente ou o CPE) com facilidade de uso interno e gerenciamento automatizado de conectividade e configuração. 
 
O [emparelhamento VNet global](../virtual-network/virtual-network-peering-overview.md) permite que os clientes conectem duas ou mais redes virtuais do Azure entre regiões diretamente. Uma vez emparelhadas, as redes virtuais aparecem como uma. O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas é roteado por meio da infraestrutura de backbone da Microsoft, assim como o tráfego é roteado entre máquinas virtuais na mesma rede virtual-somente por meio de endereços IP privados. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Bem gerenciado usando a inovação definida pelo software

Executando uma das nuvens líderes do mundo, a Microsoft obteve muita percepção e experiência na [criação e no gerenciamento](https://myignite.techcommunity.microsoft.com/sessions/66668) da infraestrutura global de alto desempenho.  
 
Seguimos um conjunto robusto de princípios operacionais: 
 
- Use o melhor hardware de troca entre as várias camadas da rede.  
- Implante novos recursos com impacto zero para os usuários finais.  
- Distribua atualizações de forma segura e confiável em toda a frota, o mais rápido possível. Horas em vez de semanas.  
- Utilize telemetria profunda em escala de nuvem e mitigação de falha totalmente automatizada.  
- Use a tecnologia de rede unificada e definida por software para controlar todos os elementos de hardware na rede.  Eliminação de duplicação e redução de falhas. 
 
Esses princípios se aplicam a todas as camadas da rede: da interface de rede do host, switching de plataforma, funções de rede no data center como balanceadores de carga, até a WAN com nossa plataforma de engenharia de tráfego e nossas redes ópticas.  
 
O crescimento exponencial do Azure e de sua rede atingiu um ponto em que eventualmente percebemos que a intuição humana não podia mais depender de gerenciar as operações de rede global. Para atender à necessidade de validar alterações longas, médias e de curto prazo na rede, desenvolvemos uma plataforma para espelhar e emular nossa rede de produção de forma sintética. A capacidade de criar ambientes espelhados e executar milhões de simulações permite testar alterações de software e hardware e seu impacto, antes de confirmá-las em nossa plataforma de produção e rede. 

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre os serviços de rede fornecidos no Azure](https://azure.microsoft.com/product-categories/networking/)
