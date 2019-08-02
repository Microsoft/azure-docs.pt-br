---
title: Projetando para recuperação de desastres com o Azure ExpressRoute | Microsoft Docs
description: Esta página fornece recomendações de arquitetura para recuperação de desastres ao usar o Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466062"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Projetando para recuperação de desastres com o emparelhamento privado do ExpressRoute

ExpressRoute foi projetado para alta disponibilidade fornecer conectividade de rede privada de nível empresarial para recursos da Microsoft a portadora. Em outras palavras, não há nenhum ponto único de falha no caminho do ExpressRoute na rede Microsoft. Para considerações de design maximizar a disponibilidade de um circuito do ExpressRoute, consulte [Projetando para alta disponibilidade com o ExpressRoute][HA].

No entanto, levando populares adágio – da Murphy*se algo pode dar errado, ele será*– em consideração, neste artigo vamos nos concentrar em soluções que vão além das falhas que podem ser endereçadas usando um único circuito do ExpressRoute. Em outras palavras, neste artigo vamos dar uma olhada em considerações de arquitetura de rede para a criação de conectividade de rede robusta de back-end para recuperação de desastres usando replicação georredundante circuitos do ExpressRoute.

## <a name="need-for-redundant-connectivity-solution"></a>Necessidade de solução de conectividade redundante

Há possibilidades e instâncias onde um serviço regional inteiro (seja que a Microsoft, outros provedores de serviços de nuvem, cliente ou provedores de serviços de rede) obtém degradado. A causa raiz de tal impacto ampla de serviços regionais incluem calamidade natural. Portanto, para aplicativos críticos missão e de continuidade de negócios é importante planejar a recuperação de desastres.   

Independentemente de você executar seus aplicativos de missão crítica em uma região do Azure ou no local ou em qualquer lugar, você pode usar outra região do Azure como seu site de failover. A seguintes artigos endereços recuperação de desastres de aplicativos e perspectivas de acesso de front-end:

- [Recuperação de desastre de escala empresarial][Enterprise DR]
- [Recuperação de desastre para SMB com o Azure Site Recovery][SMB DR]

Se você depender da conectividade de ExpressRoute entre sua rede local e a Microsoft para operações críticas, seu plano de recuperação de desastres também deve incluir a conectividade de rede com redundância geográfica. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Desafios de usar vários circuitos do ExpressRoute

Quando você interconectar o mesmo conjunto de redes usando mais de uma conexão, você pode apresentar caminhos paralelos entre as redes. Caminhos, quando projetadas corretamente, pode levar a roteamento assimétrica em paralelo. Se você tiver entidades com monitoração de estado (por exemplo, NAT, firewall) no caminho, o roteamento assimétrica poderia bloquear o fluxo de tráfego.  Normalmente, ao longo do caminho de emparelhamento privado do ExpressRoute não chegar em entidades com monitoração de estado, como o NAT ou de Firewalls. Portanto, roteamento assimétrica por meio de emparelhamento privado do ExpressRoute não necessariamente bloqueia o fluxo de tráfego.
 
No entanto, se você carregar balancear o tráfego entre os caminhos paralelos com redundância geográfica, independentemente se você tiver entidades com monitoração de estado ou não, você experimentará desempenho inconsistente de rede. Neste artigo, vamos falar sobre como resolver esses desafios.

## <a name="small-to-medium-on-premises-network-considerations"></a>Pequenas e considerações de rede de médio porte no local

Vamos considerar a rede de exemplo ilustrada no diagrama a seguir. No exemplo, a conectividade de ExpressRoute com redundância geográfica é estabelecida entre local da Contoso para um e a rede virtual da Contoso em uma região do Azure. No diagrama, a linha verde estável indica o caminho preferido (por meio do ExpressRoute 1) e aquele pontilhada representa o caminho de espera (por meio do ExpressRoute 2).

[![1]][1]

Quando você está criando a conectividade do ExpressRoute para recuperação de desastres, você precisa considerar:

- usando com redundância geográfica circuitos do ExpressRoute
- usando redes de provedor de serviço diferentes para diferente circuito do ExpressRoute
- criação de cada um circuito de ExpressRoute para [alta disponibilidade][HA]
- encerrando o circuito do ExpressRoute diferente em um local diferente na rede do cliente

Por padrão, se você anunciar rotas de forma idêntica em todos os caminhos de ExpressRoute, o Azure será o balanceamento de carga associado tráfego local por todos os caminhos de ExpressRoute usando o roteamento de múltiplos caminhos (ECMP) custo igual.

No entanto, com os circuitos do ExpressRoute com redundância geográfica é necessário levar em desempenho de rede diferente de consideração com caminhos de rede diferente (principalmente para latência de rede). Para obter o desempenho de rede mais consistente durante a operação normal, você talvez queira preferir circuito do ExpressRoute que oferece a latência mínima.

Você pode influenciar o Azure para preferir um circuito de ExpressRoute em vez de outra usando uma das seguintes técnicas (listadas em ordem de eficiência):

- anunciando a rota mais específica no circuito de ExpressRoute preferencial em comparação comparado outro circuito de ExpressRoute
- Configurando o peso mais alto de Conexão sobre a conexão que vincula-se a rede virtual ao circuito de ExpressRoute preferencial
- anunciando as rotas no circuito do ExpressRoute menos preferencial com mais do caminho (como caminho preceder)

### <a name="more-specific-route"></a>Rota mais específica

O diagrama a seguir ilustra a seleção de caminho influencia ExpressRoute usando o anúncio de rota mais específico. No exemplo ilustrado, Contoso local/24 intervalo IP é anunciado como intervalos de endereços de dois /25 via o caminho preferido (ExpressRoute 1) e como /24 por meio do caminho espera (ExpressRoute 2).

[![2]][2]

Como /25 é mais específico, em comparação com /24, Azure enviará o tráfego destinado a 10.1.11.0/24 por meio do ExpressRoute 1 no estado normal. Se as duas conexões do ExpressRoute 1 ficam inativos, a rede virtual veria o anúncio de rota 10.1.11.0/24 somente por meio do ExpressRoute 2; e, portanto, o circuito em espera é usado nesse estado de falha.

### <a name="connection-weight"></a>Peso de Conexão

Captura de tela a seguir ilustra como configurar o peso de uma conexão de ExpressRoute por meio do portal do Azure.

[![3]][3]

O diagrama a seguir ilustra a seleção de caminho influencia ExpressRoute usando o peso de conexão. O peso de conexão padrão é 0. No exemplo a seguir, o peso da conexão do ExpressRoute 1 é configurado como 100. Quando uma rede virtual recebe um prefixo de rota anunciado por meio de mais de um circuito de ExpressRoute, a rede virtual preferirá a conexão com o peso mais alto.

[![4]][4]

Se as duas conexões do ExpressRoute 1 ficam inativos, a rede virtual veria o anúncio de rota 10.1.11.0/24 somente por meio do ExpressRoute 2; e, portanto, o circuito em espera é usado nesse estado de falha.

### <a name="as-path-prepend"></a>COMO caminho preceda

O diagrama a seguir ilustra influencia seleção de caminho de ExpressRoute usando o caminho preceder. No diagrama, o anúncio de rota ao longo do ExpressRoute 1 indica o comportamento padrão de eBGP. Sobre o anúncio de rota ao longo de 2 de ExpressRoute, ASN da rede local é anexado adicionalmente na rota de como caminho. Quando a mesma rota é recebida por meio de vários circuitos do ExpressRoute, por que o processo de seleção de rota de eBGP, a rede virtual prefere a rota mais curta como caminho. 

[![5]][5]

Se as duas conexões do ExpressRoute 1 ficam inativos, a rede virtual veria o anúncio de rota 10.1.11.0/24 somente por meio do ExpressRoute 2. Consequentemente, quanto mais longa como caminho seria irrelevante. Portanto, o circuito em espera seria usado nesse estado de falha.

Usando qualquer uma das técnicas, se você influenciar do Azure para dar preferência a um dos seu ExpressRoute sobre outras, você também precisa garantir que a rede local também prefiro o mesmo caminho de ExpressRoute para o tráfego para evitar assimétricos fluxos de saída do Azure. Normalmente, o valor de preferência local é usado para influenciar a rede local para dar preferência a um circuito de ExpressRoute sobre outras. Preferência de local é uma métrica BGP (iBGP) interna. A rota BGP com o maior valor de preferência local é preferencial.

> [!IMPORTANT]
> Quando você usa determinados circuitos de ExpressRoute como autônomo, você precisará ativamente gerenciá-los e testar periodicamente a operação de failover. 
> 

## <a name="large-distributed-enterprise-network"></a>Rede de grande empresa distribuída

Quando você tiver uma rede grande empresa distribuída, você provavelmente tem vários circuitos do ExpressRoute. Nesta seção, vamos ver como recuperação de desastres usando os circuitos do ExpressRoute ativo-ativo, sem a necessidade de circuitos pelo suporte adicional de design. 

Vamos considerar o exemplo ilustrado no diagrama a seguir. No exemplo, a Contoso tem dois pontos locais conectados a dois de implantação de IaaS de Contoso em dois diferentes regiões do Azure por meio de circuitos do ExpressRoute em dois locais diferentes de emparelhamento. 

[![6]][6]

Como projetamos a recuperação de desastres tem um impacto sobre como cruzada regionais para atravessar o tráfego local (Região1/region2 para Local2/location1) é roteado. Vamos considerar duas arquiteturas diferentes de desastres que roteia o tráfego de local de região cruzada de forma diferente.

### <a name="scenario-1"></a>Cenário 1

No primeiro cenário, vamos projetar a recuperação de desastres, de modo que o fluxo de todo o tráfego entre uma rede local e de região do Azure pelo circuito do ExpressRoute local no estado estável. Se o circuito de ExpressRoute local falhar, em seguida, o circuito do ExpressRoute remoto é usado para todos os fluxos de tráfego entre o Azure e rede local.

Cenário 1 é ilustrado no diagrama a seguir. No diagrama, linhas verdes indicam caminhos para o fluxo de tráfego entre as redes VNet1 e locais. As linhas azuis indicam caminhos para o fluxo de tráfego entre redes locais e VNet2. Linhas sólidas indicam o caminho desejado no estado estável e as linhas tracejadas indicam o caminho do tráfego na falha de circuito de ExpressRoute correspondente que executa o fluxo de tráfego de estado estável. 

[![7]][7]

Você pode arquitetar o cenário usando o peso de conexão para influenciar a redes virtuais para preferir o tráfego de saída de conexão para localização local emparelhamento ExpressRoute para a rede local. Para concluir a solução, você precisará garantir que o fluxo de tráfego reverso simétricos. Você pode usar preferência de local na sessão iBGP entre seus roteadores BGP (no qual os circuitos do ExpressRoute são encerrados no lado local) para dar preferência a um circuito de ExpressRoute. A solução é ilustrada no diagrama a seguir. 

[![8]][8]

### <a name="scenario-2"></a>Cenário 2

Cenário 2 é ilustrado no diagrama a seguir. No diagrama, linhas verdes indicam caminhos para o fluxo de tráfego entre as redes VNet1 e locais. As linhas azuis indicam caminhos para o fluxo de tráfego entre redes locais e VNet2. No estado estável (linhas sólidas no diagrama), todos o tráfego entre os locais de redes virtuais e locais fluem por meio do backbone da Microsoft para a maior parte e flui a interconexão entre locais somente no estado de falha (linhas pontilhadas na o diagrama) de um ExpressRoute.

[![9]][9]

A solução é ilustrada no diagrama a seguir. Conforme ilustrado, você também pode projetar o cenário usando a mais específica de rota (opção 1) ou AS path preceda (opção 2) para influenciar a seleção de caminho de rede virtual. Para influenciar a seleção de rotas de rede local para o tráfego de saída do Azure, você precisa configurar a interconexão entre o local como menos preferível. Howe, você configura o link de interconexão como preferível depende do protocolo de roteamento na rede local. Você pode usar a preferência de local com iBGP ou métrica com IGP (OSPF ou IS-IS).

[![10]][10]


## <a name="next-steps"></a>Próximas etapas

Neste artigo, discutimos como projetar para recuperação de desastre de uma conectividade de emparelhamento privada do circuito ExpressRoute. A seguintes artigos endereços recuperação de desastres de aplicativos e perspectivas de acesso de front-end:

- [Recuperação de desastre de escala empresarial][Enterprise DR]
- [Recuperação de desastre para SMB com o Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "pequeno para considerações de tamanho médio de rede local"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "influenciar a seleção de caminho usando rotas mais específicas"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Configurando o peso de conexão por meio do portal do Azure"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "influenciar a seleção de caminho usando o peso de conexão"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "preceda influenciar a seleção de caminho usando o caminho"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "considerações de rede de grande distribuída no local"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "cenário 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "solução 1 de circuitos do ExpressRoute ativo-ativo"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "cenário 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "solução 2 de circuitos do ExpressRoute ativo-ativo"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





