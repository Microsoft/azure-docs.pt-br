---
title: 'Azure ExpressRoute: projetando para alta disponibilidade'
description: Esta página fornece recomendações de arquitetura para alta disponibilidade ao usar o Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: 3602c3944e8731263fbb55f024c276783950329f
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "92202354"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Projetando para alta disponibilidade com o ExpressRoute

O ExpressRoute foi projetado para alta disponibilidade a fim de fornecer conectividade de rede privada de nível Carrier para recursos da Microsoft. Em outras palavras, não há nenhum ponto único de falha no caminho do ExpressRoute dentro da rede da Microsoft. Para maximizar a disponibilidade, o cliente e o segmento do provedor de serviços do circuito do ExpressRoute também devem ser arquitetados para alta disponibilidade. Neste artigo, primeiro vamos examinar as considerações de arquitetura de rede para a criação de uma conectividade de rede robusta usando um ExpressRoute. em seguida, vamos examinar os recursos de ajuste que o ajudarão a melhorar a alta disponibilidade do seu circuito do ExpressRoute.

>[!NOTE]
>Os conceitos descritos neste artigo se aplicam igualmente quando um circuito do ExpressRoute é criado sob a WAN virtual ou fora dele.
>

## <a name="architecture-considerations"></a>Considerações sobre arquitetura

A figura a seguir ilustra a maneira recomendada de se conectar usando um circuito do ExpressRoute para maximizar a disponibilidade de um circuito do ExpressRoute.

 [![1]][1]

Para alta disponibilidade, é essencial manter a redundância do circuito do ExpressRoute em toda a rede de ponta a ponta. Em outras palavras, você precisa manter a redundância dentro de sua rede local e não deve comprometer a redundância na rede do provedor de serviços. Manter a redundância no mínimo implica em evitar o ponto único de falhas de rede. Ter energia e resfriamento redundantes para os dispositivos de rede melhorará ainda mais a alta disponibilidade.

### <a name="first-mile-physical-layer-design-considerations"></a>Considerações de design da camada física da primeira quilometragem

 Se você encerrar as conexões primária e secundária de um circuito do ExpressRoute no mesmo CPE (equipamento de local do cliente), você estará comprometendo a alta disponibilidade em sua rede local. Além disso, se você configurar as conexões primária e secundária por meio da mesma porta de um CPE (terminando as duas conexões em diferentes subinterfaces ou mesclando as duas conexões na rede de parceiros), você está forçando o parceiro a comprometer a alta disponibilidade em seu segmento de rede também. Esse comprometimento é ilustrado na figura a seguir.

[![2]][2]

Por outro lado, se você encerrar as conexões primária e secundária de um circuito do ExpressRoute em diferentes localizações geográficas, você poderá comprometer o desempenho da rede da conectividade. Se o tráfego estiver ativamente balanceado entre as conexões primária e secundária terminadas em diferentes localizações geográficas, uma possível diferença significativa na latência de rede entre os dois caminhos resultaria em um desempenho de rede inferior. 

Para considerações de design com redundância geográfica, consulte [projetando para recuperação de desastre com o ExpressRoute][DR].

### <a name="active-active-connections"></a>Conexões ativas-ativas

A rede da Microsoft está configurada para operar as conexões primárias e secundárias de circuitos do ExpressRoute no modo ativo-ativo. No entanto, por meio de seus anúncios de rota, você pode forçar as conexões redundantes de um circuito do ExpressRoute para operar no modo ativo-passivo. Anunciar rotas mais específicas e BGP como caminho preferível são as técnicas comuns usadas para tornar um caminho preferencial sobre o outro.

Para melhorar a alta disponibilidade, é recomendável operar ambas as conexões de um circuito do ExpressRoute no modo ativo-ativo. Se você permitir que as conexões operem no modo ativo-ativo, a rede da Microsoft balanceará o tráfego entre as conexões em cada fluxo.

A execução das conexões primária e secundária de um circuito do ExpressRoute no modo ativo-passivo enfrenta o risco de ambas as conexões falharem após uma falha no caminho ativo. As causas comuns de falha na mudança são a falta de gerenciamento ativo da conexão passiva e as rotas obsoletas de publicidade de conexão passiva.

Como alternativa, executar as conexões primária e secundária de um circuito de ExpressRoute no modo ativo-ativo, resulta em apenas cerca de metade dos fluxos com falha e sendo redirecionado, seguindo uma falha de conexão do ExpressRoute. Portanto, o modo ativo-ativo ajudará significativamente a melhorar o tempo médio de recuperação (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT para emparelhamento da Microsoft 

O emparelhamento da Microsoft foi projetado para comunicação entre pontos de extremidade públicos. Normalmente, os pontos de extremidade privados locais são endereços de rede traduzidos (NATed) com IP público no cliente ou na rede de parceiros antes de se comunicarem por emparelhamento da Microsoft. Supondo que você use as conexões primária e secundária no modo ativo-ativo, onde e como o NAT tem um impacto sobre a rapidez de recuperação após uma falha em uma das conexões do ExpressRoute. Duas opções de NAT diferentes são ilustradas na figura a seguir:

[![Beta]][3]

Na opção 1, o NAT é aplicado após a divisão do tráfego entre as conexões primárias e secundárias do ExpressRoute. Para atender aos requisitos de estado do NAT, os pools de NAT independentes são usados entre os dispositivos primário e secundário para que o tráfego de retorno chegue ao mesmo dispositivo de borda por meio do qual o fluxo foi enviado.

Na opção 2, um pool de NAT comum é usado antes de dividir o tráfego entre as conexões primárias e secundárias do ExpressRoute. É importante fazer a distinção de que o pool de NAT comum antes de dividir o tráfego não significa introduzir um ponto único de falha, comprometendo a alta disponibilidade.

Com a opção 1, após uma falha de conexão do ExpressRoute, a capacidade de alcançar o pool de NAT correspondente é interrompida. Portanto, todos os fluxos quebrados precisam ser restabelecidos pela camada TCP ou de aplicativo após o tempo limite da janela correspondente. Se qualquer um dos pools de NAT for usado para front-end de qualquer um dos servidores locais e se a conectividade correspondente falhar, os servidores locais não poderão ser acessados do Azure até que a conectividade seja corrigida.

Ao passo que com a opção 2, o NAT pode ser acessado mesmo após uma falha de conexão primária ou secundária. Portanto, a camada de rede em si pode redirecionar os pacotes e ajudar a recuperação mais rápida após a falha. 

> [!NOTE]
> Se você usar a opção de NAT 1 (pools de NAT independentes para conexões primárias e secundárias do ExpressRoute) e mapear uma porta de um endereço IP de um dos pools NAT para um servidor local, o servidor não poderá ser acessado por meio do circuito do ExpressRoute quando a conexão correspondente falhar.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Recursos de ajuste fino para emparelhamento privado

Nesta seção, vamos examinar opcionalmente (dependendo de sua implantação do Azure e de quão confidencial você é MTTR) recursos que ajudam a melhorar a alta disponibilidade do circuito do ExpressRoute. Especificamente, vamos examinar a implantação com reconhecimento de zona dos gateways de rede virtual ExpressRoute e a detecção de encaminhamento bidirecional (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Gateways de rede virtual do ExpressRoute com reconhecimento de zona de disponibilidade

Uma Zona de Disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Se você optar pela implantação de IaaS do Azure com redundância de zona, talvez também queira configurar gateways de rede virtual com redundância de zona que terminem o emparelhamento privado do ExpressRoute. Para saber mais, confira [sobre gateways de rede virtual com redundância de zona no zonas de disponibilidade do Azure][zone redundant vgw]. Para configurar o gateway de rede virtual com redundância de zona, consulte [criar um gateway de rede virtual com redundância de zona no zonas de disponibilidade do Azure][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Melhorando o tempo de detecção de falhas

O ExpressRoute dá suporte a BFD sobre emparelhamento privado. O BFD reduz o tempo de detecção de falha na rede de camada 2 entre o Microsoft Enterprise Edge (MSEEs) e seus vizinhos de BGP no lado local de cerca de 3 minutos (padrão) a menos de um segundo. O tempo de detecção de falha rápida ajuda a hastening a recuperação de falhas. Para saber mais, confira [Configurar o BFD no ExpressRoute][BFD].

## <a name="next-steps"></a>Próximas etapas

Neste artigo, discutimos como projetar para alta disponibilidade de uma conectividade de circuito do ExpressRoute. Um ponto de emparelhamento de circuito do ExpressRoute é fixado em uma localização geográfica e, portanto, pode ser afetado por uma falha catastrófica que afete todo o local. 

Para considerações de design para criar conectividade de rede com redundância geográfica para o backbone da Microsoft que pode suportar falhas catastróficas, que afetam toda a região, consulte [projetando para recuperação de desastres com o emparelhamento privado do ExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "maneira recomendada de se conectar usando o ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "conectividade de última quilometragem de nível inferior"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "Opções de NAT"


<!--Link References-->
[zone redundant vgw]: ../vpn-gateway/about-zone-redundant-vnet-gateways.md
[conf zone redundant vgw]: ../vpn-gateway/create-zone-redundant-vnet-gateway.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[BFD]: ./expressroute-bfd.md
[DR]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md