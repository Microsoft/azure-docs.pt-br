---
title: Projetando para alta disponibilidade com o Azure ExpressRoute | Microsoft Docs
description: Esta página fornece recomendações de arquitetura para alta disponibilidade ao usar o Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466642"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Projetando para alta disponibilidade com o ExpressRoute

ExpressRoute foi projetado para alta disponibilidade fornecer conectividade de rede privada de nível empresarial para recursos da Microsoft a portadora. Em outras palavras, não há nenhum ponto único de falha no caminho do ExpressRoute na rede Microsoft. Para maximizar a disponibilidade, o segmento de provedor de serviço do seu circuito do ExpressRoute e o cliente também devem ser projetados para alta disponibilidade. Neste artigo, vamos primeiro, examine as considerações de arquitetura de rede para a criação de conectividade de rede robusta usando um ExpressRoute, em seguida, vamos examinar os recursos de ajuste fino que ajudam a melhorar a alta disponibilidade do seu circuito do ExpressRoute.


## <a name="architecture-considerations"></a>Considerações de arquitetura

A figura a seguir ilustra a maneira recomendada para se conectar usando um circuito do ExpressRoute para maximizar a disponibilidade de um circuito do ExpressRoute.

 [![1]][1]

Para alta disponibilidade, é essencial para manter a redundância do circuito de ExpressRoute em toda a rede de ponta a ponta. Em outras palavras, você precisa manter a redundância em sua rede local e não deve comprometer redundância em sua rede de provedor de serviço. Manutenção da redundância no mínimo implica evitar ponto único de falhas de rede. Necessidade de alimentação redundantes e resfriamento para a rede de dispositivos serão ainda mais melhoram a alta disponibilidade.

### <a name="first-mile-physical-layer-design-considerations"></a>Considerações de design do primeiro milha camada física

 Se você encerrar as conexões de primário e secundárias de um circuitos do ExpressRoute no mesmo equipamento de local (CPE) clientes, você está comprometendo a alta disponibilidade dentro de sua rede local. Além disso, se você configurar as duas primárias e secundárias conexões via a mesma porta de um CPE (ou pelo encerramento duas conexões sob diferentes sub-interfaces ou mesclando duas conexões dentro da rede de parceiros), você está forçando o parceiro comprometa a alta disponibilidade em seu segmento da rede. Esse comprometimento é ilustrado na figura a seguir.

[![2]][2]

Por outro lado, se você encerrar o primário e conexões secundárias de um circuitos do ExpressRoute em diferentes locais geográficos, você poderia comprometer o desempenho da conectividade da rede. Se o tráfego está ativamente a carga balanceada entre o primário e conexões secundárias que são finalizados em diferentes localizações geográficas, potencial diferença substancial na latência de rede entre os dois caminhos resultaria em rede abaixo do ideal desempenho. 

Para considerações de design com redundância geográfica, consulte [projetar para recuperação de desastres com o ExpressRoute][DR].

### <a name="active-active-connections"></a>Conexões de ativo-ativo

Rede da Microsoft é configurada para operar as conexões primárias e secundárias de circuitos do ExpressRoute no modo ativo-ativo. No entanto, por meio de seus anúncios de rota, você pode forçar as conexões redundantes de um circuito de ExpressRoute para operar no modo ativo-passivo. Anunciando o BGP e rotas mais específicos como prefixação das path é as técnicas comuns usadas para fazer um caminho preferencial em detrimento do outro.

Para melhorar a alta disponibilidade, é recomendável para operar as duas conexões de um circuito de ExpressRoute no modo ativo-ativo. Se você permitir que as conexões de operar em modo ativo-ativo, o Microsoft network carregará balancear o tráfego entre as conexões de base por fluxo.

As conexões primárias e secundárias de um circuito de ExpressRoute na face do modo ativo-passivo de correr o risco as duas conexões com falha após uma falha no caminho do Active Directory. As causas comuns de falha em Alternar são a falta de gerenciamento ativo de conexão passivo e conexão passivo anunciar rotas obsoletas.

Como alternativa, executando as conexões primárias e secundárias de um circuito de ExpressRoute no modo ativo-ativo, resulta em apenas cerca de metade os fluxos com falha e Obtendo redirecionado, após uma falha de conexão do ExpressRoute. Portanto, o modo ativo-ativo significativamente ajudará a melhorar o tempo médio para recuperação (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT para emparelhamento da Microsoft 

Emparelhamento da Microsoft foi projetado para a comunicação entre pontos de extremidade públicos. Portanto, normalmente, pontos de extremidade privados no local são endereço de rede convertido (com NAT) com IP público no cliente ou na rede de parceiros antes que eles se comunicam por meio do emparelhamento da Microsoft. Supondo que você usar conexões primárias e secundárias no modo ativo-ativo, onde e como você NAT tem impacto sobre como você se recuperar rapidamente após uma falha em uma das conexões de ExpressRoute. Duas opções diferentes de NAT são ilustradas na figura a seguir:

[![3]][3]

A opção 1, NAT é aplicado depois de dividir o tráfego entre as conexões primárias e secundárias do ExpressRoute. Para atender aos requisitos de NAT com monitoração de estado, pools de NAT independentes são usados entre o primário e secundários dispositivos para que o tráfego de retorno chegará ao mesmo dispositivo de borda por meio do qual o fluxo egressed.

A opção 2, um pool NAT comum é usado antes de dividir o tráfego entre as conexões primárias e secundárias do ExpressRoute. É importante fazer a distinção que não significa que o pool NAT comum antes de dividir o tráfego de introduzir um ponto único de falha, assim, comprometer a alta disponibilidade.

Com a opção 1, após uma falha de conexão do ExpressRoute, a capacidade de alcançar o pool de NAT correspondente for interrompida. Portanto, todos os fluxos quebrados precisam ser restabelecida por TCP ou camada de aplicativo após o tempo limite da janela correspondente. Se qualquer um dos pools de NAT são usados para front-end que qualquer um dos servidores no local e se a conectividade correspondente falharem, os servidores de local não podem ser acessados do Azure até que a conectividade seja corrigida.

Enquanto com a opção 2, o NAT é acessível mesmo após uma falha de conexão primária ou secundária. Portanto, a camada de rede pode redirecionar ajuda e pacotes de recuperação mais rápida após a falha. 

> [!NOTE]
> Se você usa a opção 1 (independentes pools NAT para conexões de ExpressRoute primários e secundários) de NAT e mapear uma porta de um endereço IP de um do pool de NAT para um servidor local, o servidor não estará acessível por meio do ExpressRoute do circuito quando correspondente Falha na conexão.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Ajuste fino de recursos para o emparelhamento privado

Nesta seção, vamos revisão opcional (dependendo da sua implantação do Azure e como confidenciais que você está a MTTR) recursos que ajudam a melhorar a alta disponibilidade do seu circuito do ExpressRoute. Especificamente, vamos examinar o reconhecimento de fuso implantação de gateways de rede virtual ExpressRoute e detecção de encaminhamento bidirecional (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Gateways de rede virtual ExpressRoute com suporte a zona de disponibilidade

Uma Zona de Disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Se você optar por implantação de IaaS do Azure com redundância de zona, você talvez queira configurar gateways de rede virtual com redundância de zona que encerram o emparelhamento privado do ExpressRoute. Para saber mais, consulte [sobre os gateways de rede virtual com redundância de zona em zonas de disponibilidade do Azure][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Melhorando o tempo de detecção de falha

ExpressRoute dá suporte a BFD sobre o emparelhamento privado. BFD reduz o tempo de detecção de falha na rede de camada 2 entre a borda do Microsoft Enterprise (MSEEs) e seus vizinhos BGP no lado local de cerca de 3 minutos (padrão) para menos de um segundo. Tempo de detecção de falha rápida ajuda a hastening recuperação de falha. Para saber mais, consulte [BFD configurar por meio do ExpressRoute][BFD].

## <a name="next-steps"></a>Próximas etapas

Neste artigo, discutimos como projetar para alta disponibilidade de uma conectividade de circuito de ExpressRoute. Um ponto de emparelhamento de circuito de ExpressRoute é fixado a um local geográfico e, portanto, pode ser afetado por uma falha catastrófica que afeta o local de inteiro. 

Para considerações de design criar a conectividade de rede com redundância geográfica para o backbone da Microsoft que pode suportar falhas catastróficas, que afetam toda uma região, consulte [projetar para recuperação de desastresemparelhamentoprivadodoExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "recomendado a maneira de se conectar usando o ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal conectividade de última milha"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "opções de NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




