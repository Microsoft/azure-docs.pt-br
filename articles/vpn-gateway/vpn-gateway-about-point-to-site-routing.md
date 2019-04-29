---
title: Sobre o roteamento Ponto a Site do Azure | Microsoft Docs
description: Este artigo ajuda você a entender como o roteamento de VPN Ponto a Site se comporta.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/28/2019
ms.author: anzaman
ms.openlocfilehash: 486a910226db5dc7b36aaf873e7bb8115eb78805
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653454"
---
# <a name="about-point-to-site-vpn-routing"></a>Sobre o roteamento VPN Ponto a Site

Este artigo ajuda você a entender como o roteamento de VPN Ponto a Site do Azure se comporta. O comportamento de roteamento de VPN P2S depende do sistema operacional do cliente, o protocolo usado para a conexão VPN e como as redes virtuais (VNets) são conectadas umas às outras.

Azure atualmente oferece suporte a dois protocolos para acesso remoto, IKEv2 e SSTP. IKEv2 tem suporte em muitos sistemas operacionais de cliente, incluindo Windows, Linux, MacOS, Android e iOS. SSTP só tem suporte no Windows. Se você fizer uma alteração na topologia de rede e tiver clientes VPN do Windows, o pacote de cliente VPN para clientes do Windows deve ser baixado e instalado novamente para que as alterações sejam aplicadas ao cliente.

> [!NOTE]
> Este artigo se aplica somente a IKEv2.
>

## <a name="diagrams"></a>Sobre os diagramas

Há vários diagramas diferentes neste artigo. Cada seção mostra uma topologia ou configuração diferente. Para os fins deste artigo, as conexões VNet a VNet e Site a Site (S2S) funcionam da mesma maneira, pois ambas são túneis IPsec. Todos os gateways VPN neste artigo são baseadas em rota.

## <a name="isolatedvnet"></a>Uma VNet isolada

A conexão de gateway VPN Ponto a Site neste exemplo é para uma rede virtual que não é conectada ou emparelhada com qualquer outra rede virtual (VNet1). Neste exemplo, os clientes que usam SSTP ou IKEv2 podem acessar VNet1.

![roteamento de rede virtual isolada](./media/vpn-gateway-about-point-to-site-routing/1.jpg "roteamento de rede virtual isolada")

### <a name="address-space"></a>Espaço de endereço

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem acessar VNet1

* Os clientes não Windows podem acessar VNet1

## <a name="multipeered"></a>Vários emparelhadas VNets

Neste exemplo, a conexão de gateway VPN Ponto a Site é para VNet1. VNet1 é emparelhada com VNet2. VNet2 é emparelhada com VNet3. VNet1 é emparelhada com VNet4. Não há nenhuma correspondência direta entre VNet1 e VNet3. VNet1 tem "Permitir o tráfego de gateway" e VNet2 tem "Usar gateways remotas" habilitadas.

Os clientes que usam o Windows podem acessar VNets emparelhadas diretamente, mas o cliente VPN deve ser baixado novamente, se as alterações são feitas para emparelhamento de rede virtual ou a topologia de rede. Os clientes não Windows podem acessar VNets emparelhadas diretamente. O acesso não é transitivo e é limitado a apenas VNets emparelhadas diretamente.

![várias VNets emparelhadas](./media/vpn-gateway-about-point-to-site-routing/2.jpg "várias VNets emparaelhadas")

### <a name="address-space"></a>Espaço de endereço:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem acessar VNet1, VNet2 e VNet4, mas o cliente VPN deve ser baixado novamente para que as alterações de topologia entrem em vigor.

* Os clientes não Windows podem acessar VNet1, VNet2 e VNet4

## <a name="multis2s"></a>Várias VNets conectadas usando uma VPN S2S

Neste exemplo, a conexão de gateway VPN Ponto a Site é para VNet1. VNet1 é conectada à VNet2 usando uma conexão VPN Site a Site. VNet2 é conectada à VNet3 usando uma conexão VPN Site a Site. Não há nenhuma conexão VPN Site a Site ou emparelhamento direto entre VNet1 e VNet3. Todas as conexões Site a Site não estão executando o BGP para roteamento.

Os clientes que usam o Windows ou outro sistema operacional com suporte só podem acessar VNet1. Para acessar VNets adicionais, o BGP deve ser usado.

![vários VNets e S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "vários VNets e S2S")

### <a name="address-space"></a>Espaço de endereço

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem acessar apenas VNet1

* Os clientes não Windows podem acessar apenas VNet1

## <a name="multis2sbgp"></a>Várias VNets conectadas usando uma VPN S2S (BGP)

Neste exemplo, a conexão de gateway VPN Ponto a Site é para VNet1. VNet1 é conectada à VNet2 usando uma conexão VPN Site a Site. VNet2 é conectada à VNet3 usando uma conexão VPN Site a Site. Não há nenhuma conexão VPN Site a Site ou emparelhamento direto entre VNet1 e VNet3. Todas as conexões Site a Site não estão executando o BGP para roteamento.

Clientes que usam Windows ou outro sistema operacional com suporte podem acessar todas as VNets que estão conectadas usando a conexão VPN Site a Site, mas as rotas a VNets conectadas devem ser adicionadas manualmente aos clientes Windows.

![vários VNets e S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "vários VNets e S2S BGP")

### <a name="address-space"></a>Espaço de endereço

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes Windows: 10.1.0.0/16

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem acessar VNet1, VNet2 e VNet3, mas rotas para VNet2 e VNet3 devem ser adicionadas manualmente.

* Os clientes não Windows podem acessar VNet1, VNet2 e VNet3

## <a name="vnetbranch"></a>Uma VNet e uma filial

Neste exemplo, a conexão de gateway VPN Ponto a Site é para VNet1. VNet1 não é conectada/emparelhada com nenhuma outra rede virtual, mas é conectado a um site local por meio de uma conexão VPN Site a Site que não esteja executando o BGP.

Os clientes do Windows e não Windows podem acessar apenas VNet1.

![roteamento com uma VNet e uma filial](./media/vpn-gateway-about-point-to-site-routing/5.jpg "roteamento com uma VNet e uma filial")

### <a name="address-space"></a>Espaço de endereço

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem acessar apenas VNet1

* Os clientes não Windows podem acessar apenas VNet1

## <a name="vnetbranchbgp"></a>Uma VNet e uma filial (BGP)

Neste exemplo, a conexão de gateway VPN Ponto a Site é para VNet1. VNet1 não é conectada ou emparelhada com nenhuma outra rede virtual, mas é conectado a um site local (Site1) por meio de uma conexão VPN Site a Site executando o BGP.

Os clientes Windows podem acessar VNet e filial (Site1), mas as rotas para Site1 devem ser adicionadas manualmente ao cliente. Os clientes não Windows podem acessar a VNet, bem como a filial local.

![uma VNet e uma filial (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "uma VNet e uma filial")

### <a name="address-space"></a>Espaço de endereço

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes Windows: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem acessar VNet1 e Site1, mas rotas para Site1 devem ser adicionadas manualmente.

* Os clientes não Windows podem acessar VNet1 e Site1.


## <a name="multivnets2sbranch"></a>Várias VNets conectadas usando S2S e uma filial

Neste exemplo, a conexão de gateway VPN Ponto a Site é para VNet1. VNet1 é conectada à VNet2 usando uma conexão VPN Site a Site. VNet2 é conectada à VNet3 usando uma conexão VPN Site a Site. Não há nenhum tunel VPN Site a Site ou emparelhamento direto entre as redes VNet1 e VNet3. VNet3 é conectada a uma filial (Site1) usando uma conexão VPN Site a Site. Todas as conexões VPN não estão executando o BGP.

Todos os clientes podem acessar apenas VNet1.

![várias VNet S2S e filial](./media/vpn-gateway-about-point-to-site-routing/7.jpg "várias VNet S2S e filial")

### <a name="address-space"></a>Espaço de endereço

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem acessar apenas VNet1

* Os clientes não Windows podem acessar apenas VNet1

## <a name="multivnets2sbranchbgp"></a>Várias VNets conectadas usando S2S e uma filial (BGP)

Neste exemplo, a conexão de gateway VPN Ponto a Site é para VNet1. VNet1 é conectada à VNet2 usando uma conexão VPN Site a Site. VNet2 é conectada à VNet3 usando uma conexão VPN Site a Site. Não há nenhum tunel VPN Site a Site ou emparelhamento direto entre as redes VNet1 e VNet3. VNet3 é conectada a uma filial (Site1) usando uma conexão VPN Site a Site. Todas as conexões VPN estão executando o BGP.

Os clientes que usam o Windows podem acessar VNets e sites que são conectados usando uma conexão VPN Site a Site, mas as rotas para VNet2, VNet3 e Site1 devem ser adicionados manualmente ao cliente. Os clientes não Windows podem acessar VNets e sites que são conectados usando uma conexão VPN Site a Site sem qualquer intervenção manual. O acesso é transitivo, e os clientes podem acessar recursos em todos os sites (local) e VNets conectadas.

![várias VNet S2S e filial](./media/vpn-gateway-about-point-to-site-routing/8.jpg "várias VNet S2S e filial")

### <a name="address-space"></a>Espaço de endereço

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Rotas adicionadas

* Rotas adicionadas aos clientes: 10.1.0.0/16, 192.168.0.0/24

* Rotas adicionadas aos clientes não Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Os clientes Windows podem acessar VNet1, VNet2, VNet3 e Site1, mas as rotas para VNet2, VNet3 e Site1 devem ser adicionadas manualmente ao cliente.

* Os clientes não Windows podem acessar VNet1, Vnet2, VNet3 e Site1.

## <a name="next-steps"></a>Próximas etapas

Consulte [Criar uma VPN P2S usando o portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para começar a criar sua VPN P2S.
