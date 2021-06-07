---
title: 'Interoperabilidade no Azure: análise do plano de dados'
description: Este artigo fornece a análise do plano de dados da configuração do teste que é possível usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e o emparelhamento de rede virtual no Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4797fca5b4a756ca502147172e6f1590b9eacbe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234164"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperabilidade no Azure: análise do plano de dados

Este artigo descreve a análise do plano de dados da [configuração do teste][Setup]. Também é possível examinar a [configuração do teste][Configuration] e a [análise do plano de controle][Control-Analysis] da configuração do teste.

A análise do plano de dados examina o caminho percorrido pelos pacotes que atravessam de uma rede local (LAN ou rede virtual) para outra dentro de uma topologia. O caminho de dados entre duas redes locais não é necessariamente simétrico. Portanto, neste artigo, analisamos um caminho de encaminhamento de uma rede local para outra separada do caminho inverso.

## <a name="data-path-from-the-hub-vnet"></a>Caminho de dados da VNet do Hub

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

O emparelhamento da VNet (rede virtual) emula uma funcionalidade de ponte de rede entre as duas VNets emparelhadas. Mostramos a saída do rastreamento de rotas de uma VNet do hub para uma VM na VNet do spoke aqui:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.11.30.4

Trace complete.
```

A figura a seguir mostra a exibição da conexão gráfica da VNet do hub e da VNet do spoke da perspectiva do Observador de Rede do Azure:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Caminho para a VNet do branch

Mostramos a saída do rastreamento de rotas de uma VNet do hub para uma VM na VNet do branch aqui:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms     1 ms     1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     2 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

Neste rastreamento de rotas, o primeiro salto é o gateway VPN no Gateway de VPN do Azure da VNet do hub. O segundo salto é o gateway VPN da VNet do branch. O endereço IP do gateway VPN da VNet do branch não é anunciado na VNet do hub. O terceiro salto é a VM na VNet do branch.

A figura a seguir mostra a exibição da conexão gráfica da VNet do hub e da VNet do branch da perspectiva do Observador de Rede:

![2][2]

Para a mesma conexão, a seguinte figura mostra a exibição de grade no Observador de Rede:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Caminho para a Localização local 1

Mostramos a saída do rastreamento de rotas de uma VNet do hub para uma VM na Localização local 1 aqui:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     2 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

Neste rastreamento de rotas, o primeiro salto é o ponto de extremidade do túnel do gateway do Azure ExpressRoute para um MSEE (Roteador do Microsoft Enterprise Edge). O segundo e o terceiro saltos são o roteador de CE (borda do cliente) e os IPs de LAN da Localização local 1. Esses endereços IP não são anunciados na VNet do hub. O quarto salto é a VM na Localização local 1.


### <a name="path-to-on-premises-location-2"></a>Caminho para a Localização local 2

Mostramos a saída do rastreamento de rotas de uma VNet do hub para uma VM na Localização local 2 aqui:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    75 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

Neste rastreamento de rotas, o primeiro salto é o ponto de extremidade do túnel de gateway do ExpressRoute para um MSEE. O segundo e o terceiro saltos são o roteador de CE e os IPs de LAN da Localização local 2. Esses endereços IP não são anunciados na VNet do hub. O quarto salto é a VM na Localização local 2.

### <a name="path-to-the-remote-vnet"></a>Caminho para a VNet remota

Mostramos a saída do rastreamento de rotas de uma VNet do hub para uma VM na VNet remoto aqui:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    69 ms  10.17.30.4

Trace complete.
```

Neste rastreamento de rotas, o primeiro salto é o ponto de extremidade do túnel de gateway do ExpressRoute para um MSEE. O segundo salto é o IP do gateway da VNet remota. O intervalo de IP do segundo salto não é anunciado na VNet do hub. O terceiro salto é a VM na VNet remota.

## <a name="data-path-from-the-spoke-vnet"></a>Caminho de dados da VNet do spoke

O VNet do spoke compartilha a exibição de rede da VNet do hub. Por meio do Emparelhamento VNET, a VNet do spoke usa a conectividade de gateway remoto da VNet do hub como se ela estivesse conectada diretamente à VNet do spoke.

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Mostramos a saída do rastreamento de rotas de uma VNet do spoke para uma VM na VNet do hub aqui:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet"></a>Caminho para a VNet do branch

Mostramos a saída do rastreamento de rotas de uma VNet do spoke para uma VM na VNet do branch aqui:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

Neste rastreamento de rotas, o primeiro salto é o gateway VPN da VNet do hub. O segundo salto é o gateway VPN da VNet do branch. O endereço IP do gateway VPN da VNet do branch não é anunciado dentro da VNet do hub/spoke. O terceiro salto é a VM na VNet do branch.

### <a name="path-to-on-premises-location-1"></a>Caminho para a Localização local 1

Mostramos a saída do rastreamento de rotas da VNet do spoke para uma VM na Localização local 1 aqui:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    24 ms     2 ms     3 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     3 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

Nessas rotas, o primeiro salto é o ponto de extremidade de túnel de gateway do ExpressRoute da VNet do hub para um MSEE. O segundo e o terceiro saltos são o roteador de CE e os IPs de LAN da Localização local 1. Esses endereços IP não são anunciados na VNet do hub/spoke. O quarto salto é a VM na Localização local 1.

### <a name="path-to-on-premises-location-2"></a>Caminho para a Localização local 2

Mostramos a saída do rastreamento de rotas da VNet do spoke para uma VM na Localização local 2 aqui:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    76 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

Nessas rotas, o primeiro salto é o ponto de extremidade de túnel de gateway do ExpressRoute da VNet do hub para um MSEE. O segundo e o terceiro saltos são o roteador de CE e os IPs de LAN da Localização local 2. Esses endereços IP não são anunciados na VNets do hub/spoke. O quarto salto é a VM na Localização local 2.

### <a name="path-to-the-remote-vnet"></a>Caminho para a VNet remota

Mostramos a saída do rastreamento de rotas de uma VNet do spoke para uma VM na VNet remota aqui:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.10.30.133
  2     *        *        *     Request timed out.
  3    71 ms    70 ms    70 ms  10.17.30.4

Trace complete.
```

Nessas rotas, o primeiro salto é o ponto de extremidade de túnel de gateway do ExpressRoute da VNet do hub para um MSEE. O segundo salto é o IP do gateway da VNet remota. O intervalo de IP do segundo salto não é anunciado na VNet do hub/spoke. O terceiro salto é a VM na VNet remota.

## <a name="data-path-from-the-branch-vnet"></a>Caminho de dados da VNet do branch

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Mostramos a saída do rastreamento de rotas de uma VNet do branch para uma VM na VNet do hub aqui:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     3 ms  10.10.30.4

Trace complete.
```

Neste rastreamento de rotas, o primeiro salto é o gateway VPN da VNet do branch. O segundo salto é o gateway VPN da VNet do hub. O endereço IP do gateway VPN da VNet do hub não é anunciado na VNet remota. O terceiro salto é a VM na VNet do hub.

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

Mostramos a saída do rastreamento de rotas de uma VNet do branch para uma VM na VNet do spoke aqui:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     1 ms    <1 ms     1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     2 ms  10.11.30.4

Trace complete.
```

Neste rastreamento de rotas, o primeiro salto é o gateway VPN da VNet do branch. O segundo salto é o gateway VPN da VNet do hub. O endereço IP do gateway VPN da VNet do hub não é anunciado na VNet remota. O terceiro salto é a VM na VNet do spoke.

### <a name="path-to-on-premises-location-1"></a>Caminho para a Localização local 1

Mostramos a saída do rastreamento de rotas da VNet do branch para uma VM na Localização local 1 aqui:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.2.30.125
  4     *        *        *     Request timed out.
  5     3 ms     3 ms     3 ms  10.2.30.10

Trace complete.
```

Neste rastreamento de rotas, o primeiro salto é o gateway VPN da VNet do branch. O segundo salto é o gateway VPN da VNet do hub. O endereço IP do gateway VPN da VNet do hub não é anunciado na VNet remota. O terceiro salto é o ponto de terminação do túnel VPN no roteador CE principal. O quarto salto é um endereço IP interno da Localização local 1. Esse endereço IP de LAN não é anunciado fora do roteador de CE. O quinto salto é a VM de destino na Localização local 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Caminho para a Localização local 2 e para a VNet remota

Como discutimos na análise do plano de controle, a ramificação de rede virtual não tem nenhuma visibilidade para a Localização local 2 ou para a rede virtual remota acordo com a configuração de rede. Os seguintes resultados ping confirmam: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

C:\Users\rb>ping 10.17.30.4

Pinging 10.17.30.4 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.17.30.4:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

## <a name="data-path-from-on-premises-location-1"></a>Caminho de dados da Localização local 1

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Mostramos a saída do rastreamento de rotas da Localização local 1 para uma VM na Vnet do hub aqui:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     2 ms     2 ms     2 ms  10.10.30.4

Trace complete.
```

Neste rastreamento de rotas, os dois primeiros saltos fazem parte da rede local. O terceiro salto é a interface principal do MSEE voltada para o roteador de CE. O quarto salto é o gateway do ExpressRoute da VNet do hub. O intervalo de IP do gateway do ExpressRoute da VNet do hub não é anunciado para a rede local. O quinto salto é o VM de destino.

O Observador de Rede fornece apenas uma exibição centrada no Azure. Para uma perspectiva local, usamos o Monitor de Desempenho de Rede do Azure. O Monitor de Desempenho de Rede oferece agentes que podem ser instalados em servidores em redes fora do Azure para análise do caminho de dados.

A figura a seguir mostra a exibição de topologia da conectividade da VM de Localização local 1 para a VM na VNet do hub via ExpressRoute:

![4][4]

Conforme discutido anteriormente, a configuração do teste usa uma VPN site a site como conectividade de backup para o ExpressRoute entre a Localização local 1 e a VNet do hub. Para testar o caminho de dados de backup, vamos induzir uma falha de link do ExpressRoute entre o roteador CE local 1 primário e o MSEE correspondente. Para induzir uma falha de link do ExpressRoute, desligue a interface de CE voltada para o MSEE:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     3 ms  10.10.30.4

Trace complete.
```

A figura a seguir é a exibição de topologia da conectividade da VM da Localização local 1 para a VM na VNet do hub por meio da conectividade VPN de site a site quando a conectividade do ExpressRoute está inativa:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

Mostramos a saída do rastreamento de rotas da Localização local 1 para uma VM na Vnet do spoke aqui:

Vamos trazer de volta a conectividade primária do ExpressRoute para fazer a análise do caminho de dados em direção à VNet do spoke:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     3 ms     2 ms     2 ms  10.11.30.4

Trace complete.
```

Traga a conectividade primária do ExpressRoute 1 para o restante da análise do caminho de dados.

### <a name="path-to-the-branch-vnet"></a>Caminho para a VNet do branch

Mostramos a saída do rastreamento de rotas da Localização local 1 para uma VM na Vnet do branch aqui:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

### <a name="path-to-on-premises-location-2"></a>Caminho para a Localização local 2

Conforme discutimos na [análise do plano de controle][Control-Analysis], a Localização local 1 não tem visibilidade para a Localização local 2 de acordo com a configuração de rede. Os seguintes resultados ping confirmam: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

### <a name="path-to-the-remote-vnet"></a>Caminho para a VNet remota

Mostramos a saída do rastreamento de rotas da Localização local 1 para uma VM na VNet remota aqui:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2     2 ms     5 ms     7 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5    69 ms    70 ms    69 ms  10.17.30.4

Trace complete.
```

## <a name="data-path-from-on-premises-location-2"></a>Caminho de dados da Localização local 2

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Mostramos a saída do rastreamento de rotas da Localização local 2 para uma VM na Vnet do hub aqui:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.4
  3    <1 ms    <1 ms    <1 ms  192.168.31.22
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

Mostramos a saída do rastreamento de rotas da Localização local 2 para uma VM na Vnet do spoke aqui:

```console
C:\Windows\system32>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops
  1    <1 ms    <1 ms     1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.0
  3    <1 ms    <1 ms    <1 ms  192.168.31.18
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Caminho para a VNet do branch, para a Localização local 1 e para a VNet remota

Como discutimos na [análise do plano de controle][Control-Analysis], a Localização local 1 não tem visibilidade para a VNet do branch, para a Localização local 1 ou para a VNet remota de acordo com a configuração de rede. 

## <a name="data-path-from-the-remote-vnet"></a>Caminho de dados da VNet remota

### <a name="path-to-the-hub-vnet"></a>Caminho para a VNet do hub

Mostramos a saída do rastreamento de rotas de uma VNet remota para uma VM na VNet do hub aqui:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    65 ms    65 ms    65 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    68 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Caminho para a VNet do spoke

Mostramos a saída do rastreamento de rotas da VNet remota para uma VM na VNet do spoke aqui:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    71 ms    69 ms    69 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Caminho para a VNet do branch e para a Localização local 2

Como discutimos na [análise do plano de controle][Control-Analysis], a VNet remota não tem visibilidade para a VNet do branch ou para a Localização local 2 de acordo com a configuração de rede. 

### <a name="path-to-on-premises-location-1"></a>Caminho para a Localização local 1

Mostramos a saída do rastreamento de rotas da VNet remota para uma VM na Localização local 1 aqui:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    69 ms    69 ms    69 ms  10.2.30.10

Trace complete.
```

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Conectividade do ExpressRoute e VPN site a site em conjunto

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN site a site pelo ExpressRoute

É possível configurar uma VPN site a site, usando emparelhamento do Microsoft ExpressRoute para trocar dados em modo privado entre a rede local e as VNets do Azure. Com essa configuração, é possível trocar dados com confidencialidade, autenticidade e integridade. A troca de dados também é antirreprodução. Para obter mais informações sobre como configurar uma VPN IPsec site a site no modo de túnel usando emparelhamento do ExpressRoute da Microsoft, consulte [VPN site a site sobre emparelhamento do Microsoft Azure ExpressRoute][S2S-Over-ExR]. 

A principal limitação da configuração de uma VPN site a site que usa o emparelhamento da Microsoft é a taxa de transferência. A taxa de transferência no túnel IPsec é limitada pela capacidade do gateway de VPN. A taxa de transferência do gateway de VPN é menor que a taxa de transferência do ExpressRoute. Nesse cenário, o uso do túnel IPsec para tráfego altamente seguro e o uso de emparelhamento privado para todos os outros tráfegos ajuda a otimizar a utilização da largura de banda do ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site a site como um caminho de failover seguro para o ExpressRoute

O ExpressRoute funciona como um par de circuitos redundantes para garantir alta disponibilidade. Você pode configurar a conectividade de ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, conforme demonstrado em nossa configuração do teste, em uma região do Azure, é possível usar uma VPN site a site para criar um caminho de failover para a conectividade do ExpressRoute. Quando os mesmos prefixos são anunciados sobre o ExpressRoute e uma VPN site a site, o Azure prioriza o ExpressRoute. Para evitar roteamento assimétrico entre o ExpressRoute e a VPN site a site, a configuração de rede local também deverá retribuir usando a conectividade do ExpressRoute antes de usar a conectividade VPN site a site.

Para obter mais informações sobre como configurar conexões coexistentes para o ExpressRoute e uma VPN site a site, consulte [ExpressRoute e coexistência site a site][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Estender conectividade de back-end para VNets do spoke e localizações de branch

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade de VNet do spoke usando emparelhamento VNet

A arquitetura da VNet do hub e do spoke é amplamente usada. O hub é uma VNet no Azure que funciona como um ponto central de conectividade entre as VNets do spoke e a rede local. Os spokes são VNets que emparelham-se com o hub e que podem ser utilizadas para isolar as cargas de trabalho. O tráfego flui entre o datacenter local e o hub por meio de uma conexão de VPN ou ExpressRoute. Para obter mais informações sobre a arquitetura, confira [Implementar uma topologia de rede hub-spoke no Azure][Hub-n-Spoke].

Em emparelhamento VNet dentro de uma região, as VNets do spoke podem usar gateways de VNet do hub (ambos gateways de VPN e ExpressRoute) para comunicarem-se com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividade da VNet do branch usando VPN site a site

Talvez você queira que as VNets de branch, que estão em regiões diferentes, e as redes locais comuniquem-se entre si por meio de uma VNet de hub. A solução nativa do Azure para essa configuração é a conectividade de VPN site a site usando uma VPN. Uma alternativa é usar uma NVA (solução de virtualização de rede) para roteamento no hub.

Para obter mais informações, consulte [O que é Gateway de VPN? ][VPN] e [Implantar um NVA altamente disponível][Deploy-NVA].


## <a name="next-steps"></a>Próximas etapas

Consulte as [Perguntas frequentes sobre o ExpressRoute][ExR-FAQ] para:
-   Saber quantos circuitos do ExpressRoute podem ser conectados a um gateway do ExpressRoute.
-   Saiba quantos gateways do ExpressRoute podem ser conectados a um circuito do ExpressRoute.
-   Saber mais sobre outros limites de escala do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Exibição de conectividade do observador de rede de uma VNet do hub para uma VNet do spoke"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Exibição do observador de rede de conectividade de uma VNet de Hub para uma VNet de ramificação"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Exibição em grade do observador de rede de conectividade de uma VNet de Hub para uma VNet de ramificação"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Monitor de Desempenho de Rede exibição de conectividade da VM local 1 para a VNet do hub por meio do ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Monitor de Desempenho de Rede exibição de conectividade da VM local 1 para a VNet do hub por meio de uma VPN site a site"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md