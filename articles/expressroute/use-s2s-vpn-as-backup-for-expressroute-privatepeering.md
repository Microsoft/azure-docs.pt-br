---
title: Usando a VPN S2S como um backup para o emparelhamento privado do Azure ExpressRoute | Microsoft Docs
description: Esta página fornece recomendações de arquitetura para fazer backup do emparelhamento privado do Azure ExpressRoute com VPN S2S.
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92207913"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Usando a VPN S2S como um backup para o emparelhamento privado do ExpressRoute

No artigo intitulado [criando para recuperação de desastre com o emparelhamento privado do expressroute][DR-PP], discutimos a necessidade de solução de conectividade de backup para uma conectividade de emparelhamento privado do expressroute e como usar circuitos do expressroute com redundância geográfica para fins de uso. Neste artigo, vamos considerar como aproveitar e manter a VPN S2S (site a site) como um backup para o emparelhamento privado do ExpressRoute. 

Diferentemente dos circuitos de ExpressRoute com redundância geográfica, você pode usar a combinação de recuperação de desastres de ExpressRoute-VPN somente no modo ativo-passivo. Um grande desafio de usar qualquer conectividade de rede de backup no modo passivo é que a conexão passiva geralmente falhará ao lado da conexão primária. O motivo comum para as falhas da conexão passiva é a falta de manutenção ativa. Portanto, neste artigo, vamos nos concentrar em como verificar e manter ativamente a conectividade VPN S2S que está fazendo backup de um emparelhamento privado do ExpressRoute.

>[!NOTE] 
>Quando uma determinada rota é anunciada via ExpressRoute e VPN, o Azure prefere o roteamento pelo ExpressRoute.  
>

Neste artigo, vamos ver como verificar a conectividade tanto da perspectiva do Azure quanto da perspectiva de borda de rede do lado do cliente. A capacidade de validar de qualquer uma das extremidades ajudará independentemente de você gerenciar os dispositivos de rede do lado do cliente que emparelham com as entidades de rede da Microsoft. 

## <a name="example-topology"></a>Exemplo de topologia

Em nossa configuração, temos uma rede local conectada a uma VNet do Hub do Azure por meio de um circuito do ExpressRoute e uma conexão VPN S2S. A VNet do Hub do Azure está, por sua vez, emparelhada com uma VNet spoke, conforme mostrado no diagrama abaixo:

![1][1]

Na configuração, o circuito do ExpressRoute é encerrado em um par de roteadores de "borda do cliente" (CE) no local. A LAN local é conectada aos roteadores do CE por meio de um par de firewalls que operam no modo de acompanhamento líder. A VPN S2S é encerrada diretamente nos firewalls.

A tabela a seguir lista os prefixos IP de chave da topologia:

| **Entidade** | **Prefix** |
| --- | --- |
| LAN local | 10.1.11.0/25 |
| VNet do Hub do Azure | 10.17.11.0/25 |
| VNet do Azure spoke | 10.17.11.128/26 |
| Servidor de teste local | 10.1.11.10 |
| VM de teste de VNet do spoke | 10.17.11.132 |
| Sub-rede P2P de conexão primária do ExpressRoute | 192.168.11.16/30 |
| Sub-rede P2P de conexão secundária do ExpressRoute | 192.168.11.20/30 |
| IP do par de BGP primário do gateway de VPN | 10.17.11.76 |
| IP do par BGP secundário do gateway de VPN | 10.17.11.77 |
| IP de pares BGP VPN de firewall local | 192.168.11.88 |
| E/f do roteador CE primário em relação ao IP do firewall | 192.168.11.0/31 |
| E/f de firewall em relação ao IP do roteador CE primário | 192.168.11.1/31 |
| E/f do roteador CE secundário em relação ao IP do firewall | 192.168.11.2/31 |
| E/f de firewall em relação ao IP do roteador CE secundário | 192.168.11.3/31 |


A tabela a seguir lista o ASNs da topologia:

| **Sistema autônomo** | **ASN** |
| --- | --- |
| Local | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Rede virtual GW (ExR) | 65515 |
| Rede virtual GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Alta disponibilidade sem assimétrica

### <a name="configuring-for-high-availability"></a>Configurando para alta disponibilidade

[Configurar as conexões coexistentes do expressroute e site a site][Conf-CoExist] discute como configurar o circuito de expressroute e as conexões VPN S2S coexistentes. Como discutimos na [criação de alta disponibilidade com o expressroute][HA], para melhorar a alta disponibilidade do expressroute, nossa configuração mantém a redundância de rede (evita o ponto único de falha) até os pontos de extremidade. Além disso, as conexões primárias e secundárias dos circuitos do ExpressRoute são configuradas para operar no modo ativo-ativo anunciando os prefixos locais da mesma forma pelas duas conexões. 

O anúncio de rota local do roteador CE primário por meio da conexão primária do circuito do ExpressRoute é mostrado abaixo (comandos Junos):

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

O anúncio de rota local do roteador CE secundário por meio da conexão secundária do circuito do ExpressRoute é mostrado abaixo (comandos Junos):

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Para melhorar a alta disponibilidade da conexão de backup, a VPN S2S também é configurada no modo ativo-ativo. A configuração do gateway de VPN do Azure é mostrada abaixo. Observação como parte da VPN de configuração de VPN, os endereços IP do par de BGP do gateway--10.17.11.76 e 10.17.11.77--também estão listados.

![2][2]

A rota local é anunciada pelos firewalls para os pares de BGP primários e secundários do gateway de VPN. Os anúncios de rota são mostrados abaixo (Junos):

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>Configurar a VPN S2S no modo ativo-ativo não apenas fornece alta disponibilidade para a conectividade de rede de backup de recuperação de desastre, mas também fornece maior taxa de transferência para a conectividade de backup. Em outras palavras, é recomendável configurar a VPN S2S no modo ativo-ativo, pois ela força a criação de vários túneis subjacentes.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Configurando para fluxo de tráfego simétrico

Observamos que, quando uma determinada rota local é anunciada por meio de ExpressRoute e VPN S2S, o Azure prefere o caminho do ExpressRoute. Para forçar o Azure a preferir o caminho VPN S2S sobre o ExpressRoute coexistente, você precisa anunciar rotas mais específicas (prefixo mais longo com maior máscara de sub-rede) por meio da conexão VPN. Nosso objetivo aqui é usar as conexões VPN como somente backup. Portanto, o comportamento de seleção de caminho padrão do Azure está em linha com nosso objetivo. 

É nossa responsabilidade garantir que o tráfego destinado ao Azure do local também prefira o caminho do ExpressRoute em relação à VPN S2S. A preferência local padrão dos roteadores e firewalls CE em nossa configuração local é 100. Portanto, ao configurar a preferência local das rotas recebidas por meio dos emparelhamentos privados do ExpressRoute maiores que 100 (digamos 150), podemos fazer com que o tráfego destinado ao Azure prefira o circuito do ExpressRoute no estado estacionário.

A configuração de BGP do roteador CE primário que encerra a conexão primária do circuito do ExpressRoute é mostrada abaixo. Observe que o valor da preferência local das rotas anunciadas pela sessão iBGP está configurado para ser 150. Da mesma forma, precisamos garantir que a preferência local do roteador secundário do CE que termina a conexão secundária do circuito de ExpressRoute também esteja configurada para ser 150.

```console
user@SEA-MX03-01> show configuration routing-instances Cust11
description "Customer 11 VRF";
instance-type virtual-router;
interface xe-0/0/0:0.110;
interface ae0.11;
protocols {
  bgp {
    group ibgp {
        type internal;
        local-preference 150;
        neighbor 192.168.11.1;
    }
    group ebgp {
        peer-as 12076;
        bfd-liveness-detection {
            minimum-interval 300;
            multiplier 3;
        }
        neighbor 192.168.11.18;
    }
  }
}
```

A tabela de roteamento dos firewalls locais confirma (mostrado abaixo) que, para o tráfego local destinado ao Azure, o caminho preferencial está acima do ExpressRoute no estado Steady.

```console
user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                      AS path: 12076 I, validation-state: unverified
                    > to 192.168.11.0 via reth1.11
                      to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                    [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
10.17.11.76/32     *[Static/5] 2d 21:12:16
                     > via st0.118
10.17.11.77/32     *[Static/5] 2d 00:41:56
                    > via st0.119
10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                       AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.0 via reth1.11
                       to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                     [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
```

Na tabela de rotas acima, para as rotas de VNet Hub e spoke--10.17.11.0/25 e 10.17.11.128/26--vemos que o circuito do ExpressRoute é preferencial em conexões VPN. O 192.168.11.0 e o 192.168.11.2 são IPs na interface de firewall em direção aos roteadores CE.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Validação da troca de rota pela VPN S2S

Anteriormente neste artigo, verificamos o anúncio de rota local dos firewalls para os pares de BGP primários e secundários do gateway de VPN. Além disso, vamos confirmar as rotas do Azure recebidas pelos firewalls dos pares de BGP primários e secundários do gateway de VPN.

```console
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.76                             65515 I
  10.17.11.128/26         10.17.11.76                             65515 I

{primary:node0}
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.77                             65515 I
  10.17.11.128/26         10.17.11.77                             65515 I
```

Da mesma forma, vamos verificar prefixos de rota de rede local recebidos pelo gateway de VPN do Azure. 

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

Network      NextHop       AsPath      Weight
-------      -------       ------      ------
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.76   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.77   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
```

Como visto acima, o gateway de VPN tem rotas recebidas pelos pares de BGP primário e secundário do gateway de VPN. Ele também tem visibilidade sobre as rotas recebidas por meio de conexões primárias e secundárias do ExpressRoute (aquelas com AS-Path precedidas com 12076). Para confirmar as rotas recebidas por meio de conexões VPN, precisamos saber o IP do par de BGP local das conexões. Em nossa configuração em questão, é 192.168.11.88 e vemos as rotas recebidas dele.

Em seguida, vamos verificar as rotas anunciadas pelo gateway de VPN do Azure para o ponto de BGP do firewall local (192.168.11.88).

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

Falha ao ver as trocas de rota indicam falha de conexão. Consulte [solução de problemas: uma conexão VPN site a site do Azure não pode se conectar e parar de funcionar][VPN Troubleshoot] para obter ajuda com a solução de problemas de conexão VPN.

## <a name="testing-failover"></a>Testando o failover

Agora que confirmamos as trocas de rota bem-sucedidas na conexão VPN (plano de controle), estamos definidos para mudar o tráfego (plano de dados) da conectividade do ExpressRoute para a conectividade VPN. 

>[!NOTE] 
>Em ambientes de produção, o teste de failover deve ser feito durante a janela de trabalho de manutenção de rede agendada, pois ele pode causar interrupções no serviço.
>

Antes de fazer a alternância de tráfego, vamos rastrear a rota do caminho atual em nossa configuração do servidor de teste local para a VM de teste na VNet do spoke.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

As sub-redes de conexão ponto a ponto primária e secundária do ExpressRoute de nossa configuração são, respectivamente, 192.168.11.16/30 e 192.168.11.20/30. Na rota de rastreamento acima, na etapa 3 vemos que estamos atingindo 192.168.11.18, que é o IP da interface do MSEE primário. A presença da interface MSEE confirma que, conforme o esperado, o caminho atual está acima do ExpressRoute.

Conforme relatado nos [emparelhamentos de circuito do expressroute][RST], vamos usar os seguintes comandos do PowerShell para desabilitar o emparelhamento primário e o secundário do circuito do ExpressRoute.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

O tempo de alternância de failover depende do tempo de convergência de BGP. Em nossa configuração, a opção de failover leva alguns segundos (menos de 10). Após a mudança, a repetição dos traceroute mostra o seguinte caminho:

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

O resultado de traceroute confirma que a conexão de backup via VPN S2S está ativa e pode fornecer continuidade de serviço se as conexões primárias e secundárias do ExpressRoute falharem. Para concluir o teste de failover, vamos habilitar as conexões de ExpressRoute de volta e normalizar o fluxo de tráfego usando o seguinte conjunto de comandos.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Para confirmar se o tráfego é revertido para o ExpressRoute, repita os traceroute e verifique se ele está passando pelo emparelhamento privado do ExpressRoute.

## <a name="next-steps"></a>Próximas etapas

O ExpressRoute foi projetado para alta disponibilidade sem ponto único de falha na rede da Microsoft. Ainda assim, um circuito do ExpressRoute é restrito a uma única região geográfica e a um provedor de serviços. A VPN S2S pode ser uma boa solução de backup passivo de recuperação de desastre para um circuito do ExpressRoute. Para uma solução de conexão de backup passiva confiável, a manutenção regular da configuração passiva e a validação periódica a conexão é importante. É essencial não permitir que a configuração de VPN se torne obsoleta e, periodicamente (digamos, a cada trimestre) Repita as etapas de teste de validação e failover descritas neste artigo durante a janela de manutenção.

Para habilitar o monitoramento e alertas com base em métricas de gateway de VPN, consulte [configurar alertas em métricas de gateway de VPN][VPN-alerts].

Para agilizar a convergência de BGP após uma falha de ExpressRoute, [Configure o BFD no expressroute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologia em questão"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Configuração de GW VPN"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md