---
title: 'Azure ExpressRoute: configurar o BFD'
description: Este artigo fornece instruções sobre como configurar a BFD (Detecção de Encaminhamento Bidirecional) no emparelhamento privado de um circuito do ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511256"
---
# <a name="configure-bfd-over-expressroute"></a>Configurar BFD no ExpressRoute

O ExpressRoute dá suporte à detecção de encaminhamento bidirecional (BFD) em emparelhamento privado e da Microsoft. Ao habilitar o BFD sobre o ExpressRoute, você pode acelerar a detecção de falhas de link entre os dispositivos do Microsoft Enterprise Edge (MSEE) e os roteadores que o circuito do ExpressRoute é configurado (CE/PE). Você pode configurar o ExpressRoute em seus dispositivos de roteamento de borda ou seus dispositivos de roteamento de borda de parceiro (se você tiver feito com o serviço de conexão de camada 3 gerenciado). Este documento explica a necessidade de BFD e de como habilitar a BFD no ExpressRoute.

## <a name="need-for-bfd"></a>Necessidade de BFD

O diagrama a seguir mostra o benefício de habilitar BFD no circuito do ExpressRoute: [![1]][1]

É possível habilitar o circuito do ExpressRoute por meio de conexões da Camada 2 ou por conexões gerenciadas da Camada 3. Em ambos os casos, se houver mais de um dispositivo de camada 2 no caminho de conexão do ExpressRoute, a responsabilidade de detectar quaisquer falhas de link no caminho está na sessão BGP subjacente.

Nos dispositivos MSEE, o BGP Keep-Alive e o tempo de espera geralmente são configurados como 60 e 180 segundos, respectivamente. Por esse motivo, quando ocorre uma falha de link, pode levar até três minutos para detectar qualquer falha de link e alternar o tráfego para conexão alternativa.

Você pode controlar os temporizadores BGP Configurando um BGP Keep-Alive e um tempo de espera inferiores em seu dispositivo de emparelhamento de borda. Se os temporizadores BGP não forem os mesmos entre os dois dispositivos de emparelhamento, a sessão BGP será estabelecida usando o valor de tempo menor. O BGP Keep-Alive pode ser definido como baixo como três segundos e o tempo de espera como baixo de 10 segundos. No entanto, a definição de um temporizador BGP muito agressivo não é recomendada porque o protocolo tem uso intensivo de processo.

Nesse cenário, a BFD pode ajudar. A BFD fornece detecção de falha de vínculo de baixa sobrecarga em um intervalo de tempo em fração de segundos. 


## <a name="enabling-bfd"></a>Habilitar BFD

A BFD é configurada por padrão em todas as interfaces de emparelhamento privado do ExpressRoute criadas recentemente nos MSEEs. Dessa forma, para habilitar o BFD, você só precisa configurar o BFD em seus dispositivos primários e secundários. Configurar o BFD é um processo de duas etapas. Configure o BFD na interface e, em seguida, vincule-o à sessão BGP.

Um exemplo de configuração de CE/PE (usando o Cisco IOS XE) é mostrado abaixo. 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>Para habilitar a BFD em um emparelhamento privado já existente, será necessário redefinir o emparelhamento. Consulte [Redefinir emparelhamentos do ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negociação de temporizador de BFD

Entre pares de BFD, o mais lento dos dois pares determina a taxa de transmissão. Os intervalos de transmissão/recepção de BFD dos MSEEs estão definidos para 300 milissegundos. Em determinados cenários, o intervalo pode ser definido em um valor mais alto de 750 milissegundos. Ao configurar um valor mais alto, você pode forçar esses intervalos a serem mais longos, mas não é possível torná-los mais curtos.

>[!NOTE]
>Se você tiver configurado circuitos do ExpressRoute com redundância geográfica ou usar a conectividade VPN IPSec de site a site como backup. A habilitação de BFD ajudará o failover mais rápido após uma falha de conectividade do ExpressRoute. 
>

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações ou ajuda, confira os seguintes links:

- [Criar e modificar um circuito do ExpressRoute][CreateCircuit]
- [Criar e modificar o roteamento de um circuito do ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "A BFD acelera o tempo de dedução de falha de vínculo"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md