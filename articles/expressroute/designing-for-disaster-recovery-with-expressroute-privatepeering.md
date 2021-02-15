---
title: 'Azure ExpressRoute: projetando para recuperação de desastres'
description: Esta página fornece recomendações de arquitetura para a recuperação de desastres durante o uso do Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: duau
ms.openlocfilehash: 2a5730cd75ccb76d25897e9109555113f7355c2f
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "92202406"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Criando para recuperação de desastre com o emparelhamento privado do ExpressRoute

O ExpressRoute foi projetado para alta disponibilidade a fim de fornecer conectividade de rede privada de nível Carrier para recursos da Microsoft. Em outras palavras, não há nenhum ponto único de falha no caminho do ExpressRoute dentro da rede da Microsoft. Para obter considerações de design para maximizar a disponibilidade de um circuito do ExpressRoute, consulte [projetando para alta disponibilidade com o ExpressRoute][HA].

No entanto, tomando o adágio popular da Murphy – *se algo puder dar errado, ele entrará* em consideração. neste artigo, vamos nos concentrar em soluções que vão além das falhas que podem ser resolvidas usando um único circuito do ExpressRoute. Em outras palavras, neste artigo, vamos examinar as considerações de arquitetura de rede para criar uma conectividade de rede de back-end robusta para recuperação de desastres usando circuitos de ExpressRoute com redundância geográfica.

>[!NOTE]
>Os conceitos descritos neste artigo se aplicam igualmente quando um circuito do ExpressRoute é criado sob a WAN virtual ou fora dele.
>

## <a name="need-for-redundant-connectivity-solution"></a>Necessidade de solução de conectividade redundante

Há possibilidades e instâncias em que um serviço regional inteiro (seja o que a Microsoft, os provedores de serviços de rede, o cliente ou outros provedores de serviços de nuvem) é degradado. A causa raiz para esse impacto regional de serviços variados incluem calamidade natural. Portanto, para aplicativos de continuidade de negócios e de missão crítica, é importante planejar a recuperação de desastres.   

Independentemente de você executar seus aplicativos de missão crítica em uma região do Azure ou local ou em qualquer outro lugar, você pode usar outra região do Azure como seu site de failover. Os artigos a seguir abordam a recuperação de desastre de aplicativos e perspectivas de acesso de front-end:

- [Recuperação de desastre de escala empresarial][Enterprise DR]
- [Recuperação de desastre para SMB com o Azure Site Recovery][SMB DR]

Se você depender da conectividade do ExpressRoute entre sua rede local e a Microsoft para operações críticas, seu plano de recuperação de desastres também deverá incluir conectividade de rede com redundância geográfica. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Desafios de usar vários circuitos do ExpressRoute

Ao interconectar o mesmo conjunto de redes usando mais de uma conexão, você introduz caminhos paralelos entre as redes. Os caminhos paralelos, quando não arquitetaram corretamente, podem levar ao roteamento assimétrico. Se você tiver entidades com estado (por exemplo, NAT, firewall) no caminho, o roteamento assimétrico poderá bloquear o fluxo de tráfego.  Normalmente, no caminho de emparelhamento privado do ExpressRoute, você não entrará em entidades com estado, como NAT ou firewalls. Portanto, o roteamento assimétrico pelo emparelhamento privado do ExpressRoute não bloqueia necessariamente o fluxo de tráfego.
 
No entanto, se você balancear a carga do tráfego entre caminhos paralelos com redundância geográfica, independentemente de você ter entidades com estado ou não, você terá um desempenho de rede inconsistente. Neste artigo, vamos discutir como abordar esses desafios.

## <a name="small-to-medium-on-premises-network-considerations"></a>Considerações de rede local de pequeno a médio porte

Vamos considerar a rede de exemplo ilustrada no diagrama a seguir. No exemplo, a conectividade de ExpressRoute com redundância geográfica é estabelecida entre a localização local da Contoso e a VNet da Contoso em uma região do Azure. No diagrama, uma linha verde sólida indica o caminho preferencial (via ExpressRoute 1) e o pontilhado representa o caminho de espera (via ExpressRoute 2).

[![1]][1]

Ao criar a conectividade de ExpressRoute para recuperação de desastres, você precisa considerar:

- usando circuitos do ExpressRoute com redundância geográfica
- usando diferentes redes de provedor de serviços para um circuito de ExpressRoute diferente
- projetando cada um dos circuitos de ExpressRoute para [alta disponibilidade][HA]
- encerrando o circuito do ExpressRoute diferente em um local diferente na rede do cliente

Por padrão, se você anunciar rotas de forma idêntica em todos os caminhos do ExpressRoute, o Azure balanceará a carga do tráfego de entrada local em todos os caminhos do ExpressRoute usando o roteamento de ECMP (vários caminhos de custo igual).

No entanto, com os circuitos de ExpressRoute com redundância geográfica, precisamos levar em consideração diferentes desempenhos de rede com diferentes caminhos de rede (especialmente para latência de rede). Para obter um desempenho de rede mais consistente durante a operação normal, convém preferir o circuito do ExpressRoute que oferece a latência mínima.

Você pode influenciar o Azure a preferir um circuito de ExpressRoute sobre outro usando uma das seguintes técnicas (listadas na ordem de eficácia):

- anunciando uma rota mais específica sobre o circuito de ExpressRoute preferencial em comparação com outros circuitos do ExpressRoute
- Configurando um peso de conexão maior na conexão que vincula a rede virtual ao circuito de ExpressRoute preferencial
- anunciando as rotas em um circuito de ExpressRoute menos preferencial com caminho mais longo do que (como caminho do demarcador)

### <a name="more-specific-route"></a>Rota mais específica

O diagrama a seguir ilustra a influência da seleção de caminho do ExpressRoute usando um anúncio de rota mais específico. No exemplo ilustrado, o intervalo de IP local/24 da Contoso é anunciado como dois/25 intervalos de endereços por meio do caminho preferencial (ExpressRoute 1) e como/24 por meio do caminho de espera (ExpressRoute 2).

[![2]][2]

Como/25 é mais específico, comparado a/24, o Azure enviaria o tráfego destinado a 10.1.11.0/24 por meio do ExpressRoute 1 no estado normal. Se ambas as conexões do ExpressRoute 1 ficarem inativas, a VNet veria o anúncio de rota 10.1.11.0/24 somente por meio do ExpressRoute 2; e, portanto, o circuito em espera é usado nesse estado de falha.

### <a name="connection-weight"></a>Peso da conexão

A captura de tela a seguir ilustra a configuração do peso de uma conexão do ExpressRoute por meio de portal do Azure.

[![Beta]][3]

O diagrama a seguir ilustra a influência da seleção de caminho do ExpressRoute usando o peso da conexão. O peso de conexão padrão é 0. No exemplo a seguir, o peso da conexão para o ExpressRoute 1 é configurado como 100. Quando uma VNet recebe um prefixo de rota anunciado por mais de um circuito de ExpressRoute, a VNet prefere a conexão com o peso mais alto.

[![4]][4]

Se ambas as conexões do ExpressRoute 1 ficarem inativas, a VNet veria o anúncio de rota 10.1.11.0/24 somente por meio do ExpressRoute 2; e, portanto, o circuito em espera é usado nesse estado de falha.

### <a name="as-path-prepend"></a>Como caminho de preceder

O diagrama a seguir ilustra influência na seleção de caminho do ExpressRoute usando AS precedes de caminho. No diagrama, o anúncio de rota no ExpressRoute 1 indica o comportamento padrão do eBGP. No anúncio de rota no ExpressRoute 2, o ASN da rede local é anexado adicionalmente na rota como caminho. Quando a mesma rota é recebida por meio de vários circuitos de ExpressRoute, de acordo com o processo de seleção de rota eBGP, a VNet prefere a rota com o caminho mais curto. 

[![05]][5]

Se ambas as conexões do ExpressRoute 1 ficarem inativas, a VNet veria o anúncio de rota 10.1.11.0/24 somente por meio do ExpressRoute 2. De modo seqüencial, o caminho mais longo como se tornaria irrelevante. Portanto, o circuito em espera seria usado nesse estado de falha.

Usando qualquer uma das técnicas, se você influenciar o Azure a preferir um dos ExpressRoute sobre outros, também precisará garantir que a rede local também prefira o mesmo caminho do ExpressRoute para o tráfego associado do Azure para evitar fluxos assimétricos. Normalmente, o valor de preferência local é usado para influenciar a rede local para preferir um circuito de ExpressRoute sobre outros. A preferência local é uma métrica BGP interna (iBGP). A rota BGP com o maior valor de preferência local é preferencial.

> [!IMPORTANT]
> Ao usar determinados circuitos do ExpressRoute como em espera, você precisa gerenciá-los ativamente e testar periodicamente a operação de failover. 
> 

## <a name="large-distributed-enterprise-network"></a>Grande rede corporativa distribuída

Quando você tem uma grande rede corporativa distribuída, provavelmente terá vários circuitos de ExpressRoute. Nesta seção, vamos ver como projetar a recuperação de desastres usando os circuitos do ExpressRoute ativo-ativo, sem a necessidade de circuitos de espera adicionais. 

Vamos considerar o exemplo ilustrado no diagrama a seguir. No exemplo, a contoso tem dois locais no local conectados a duas implantações de IaaS da Contoso em duas regiões diferentes do Azure por meio de circuitos de ExpressRoute em dois locais de emparelhamento diferentes. 

[![6]][6]

A maneira como arquitetamos a recuperação de desastres tem um impacto sobre como o tráfego entre as opções regionais e entre locais (region1/region2 para location2/location1) é roteado. Vamos considerar duas arquiteturas de desastre diferentes que roteiam o tráfego de localização entre regiões de forma diferente.

### <a name="scenario-1"></a>Cenário 1

No primeiro cenário, vamos projetar a recuperação de desastres de forma que todo o tráfego entre uma região do Azure e um fluxo de rede local por meio do circuito do ExpressRoute no estado estacionário. Se o circuito do ExpressRoute local falhar, o circuito do ExpressRoute remoto será usado para todos os fluxos de tráfego entre o Azure e a rede local.

O cenário 1 é ilustrado no diagrama a seguir. No diagrama, as linhas verdes indicam caminhos para o fluxo de tráfego entre VNet1 e redes locais. As linhas azuis indicam caminhos para o fluxo de tráfego entre VNet2 e redes locais. As linhas sólidas indicam o caminho desejado no estado estacionário e as linhas tracejadas indicam o caminho de tráfego na falha do circuito de ExpressRoute correspondente que transporta o fluxo de tráfego de estado estacionário. 

[![7]][7]

Você pode arquitetar o cenário usando o peso de conexão para influenciar o VNets para preferir a conexão com o local de emparelhamento local ExpressRoute para tráfego de rede local. Para concluir a solução, você precisa garantir o fluxo de tráfego inverso simétrico. Você pode usar a preferência local na sessão de iBGP entre os Roteadores BGP (nos quais os circuitos de ExpressRoute são encerrados no lado local) para preferir um circuito de ExpressRoute. A solução é ilustrada no diagrama a seguir. 

[![8]][8]

### <a name="scenario-2"></a>Cenário 2

O cenário 2 é ilustrado no diagrama a seguir. No diagrama, as linhas verdes indicam caminhos para o fluxo de tráfego entre VNet1 e redes locais. As linhas azuis indicam caminhos para o fluxo de tráfego entre VNet2 e redes locais. No estado estacionário (linhas sólidas no diagrama), todo o tráfego entre os locais VNets e local fluem por meio do backbone da Microsoft para a maior parte e flui através da interconexão entre locais no local apenas no estado de falha (linhas pontilhadas no diagrama) de um ExpressRoute.

[![9]][9]

A solução é ilustrada no diagrama a seguir. Conforme ilustrado, você pode arquitetar o cenário usando uma rota mais específica (opção 1) ou um prepath (opção 2) para influenciar a seleção do caminho da VNet. Para influenciar a seleção de rota de rede local para o tráfego associado do Azure, você precisa configurar a interconexão entre a localização local como menos preferível. Howe você configura o link de interconexão como preferível depende do protocolo de roteamento usado na rede local. Você pode usar a preferência local com iBGP ou métrica com o IGP (OSPF ou IS-IS).

[![10]][10]


## <a name="next-steps"></a>Próximas etapas

Neste artigo, discutimos como projetar para a recuperação de desastre de uma conectividade de emparelhamento privado do circuito do ExpressRoute. Os artigos a seguir abordam a recuperação de desastre de aplicativos e perspectivas de acesso de front-end:

- [Recuperação de desastre de escala empresarial][Enterprise DR]
- [Recuperação de desastre para SMB com o Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "considerações de rede local de tamanho pequeno para médio"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "influenciando a seleção de caminho usando rotas mais específicas"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Configurando o peso de conexão via portal do Azure"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "influenciando a seleção de caminho usando o peso da conexão"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "influenciando a seleção de caminho usando as precedes de caminho"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "grandes considerações de rede distribuída local"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "cenário 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "a solução de circuitos do ExpressRoute ativo-ativo 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "cenário 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "de 2 circuitos de ExpressRoute ativos-ativos"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending