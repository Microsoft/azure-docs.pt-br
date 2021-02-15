---
title: Compartilhar um serviço de vínculo privado na WAN virtual
titleSuffix: Azure Virtual WAN
description: Etapas para configurar o link privado na WAN virtual
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913748"
---
# <a name="use-private-link-in-virtual-wan"></a>Usar o link privado na WAN virtual

O [link privado do Azure](../private-link/private-link-overview.md) é uma tecnologia que permite que você conecte as ofertas de plataforma como serviço do Azure usando a conectividade de endereço IP privado expondo [pontos de extremidade privados](../private-link/private-endpoint-overview.md). Com a WAN virtual do Azure, você pode implantar um ponto de extremidade privado em uma das redes virtuais conectadas a qualquer Hub virtual. Isso fornece conectividade com qualquer outra rede virtual ou ramificação conectada à mesma WAN virtual.

## <a name="before-you-begin"></a>Antes de começar

As etapas neste artigo pressupõem que você já tenha implantado uma WAN virtual com um ou mais hubs, bem como pelo menos duas redes virtuais conectadas à WAN virtual.

Para criar uma nova WAN virtual e um novo hub, siga as etapas nos artigos a seguir:

* [Criação de uma WAN virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criação de um hub](virtual-wan-site-to-site-portal.md#hub)
* [Conectar uma VNET a um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Criar um ponto de extremidade de link privado

Você pode criar um ponto de extremidade de link privado para vários serviços diferentes. Neste exemplo, usaremos o banco de dados SQL do Azure. Você pode encontrar mais informações sobre como criar um ponto de extremidade privado para um banco de dados SQL do Azure no [início rápido: criar um ponto de extremidade privado usando o portal do Azure](../private-link/create-private-endpoint-portal.md). A imagem a seguir mostra a configuração de rede do banco de dados SQL do Azure:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="criar link privado" lightbox="./media/howto-private-link/create-private-link.png":::

Depois de criar o banco de dados SQL do Azure, você pode verificar se o endereço IP do ponto de extremidade privado está navegando pelos pontos de extremidades particulares:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="criar link privado" lightbox="./media/howto-private-link/endpoints.png":::

Ao clicar no ponto de extremidade privado que criamos, você deverá ver seu endereço IP privado, bem como seu FQDN (nome de domínio totalmente qualificado). Observe que o ponto de extremidade privado tem um endereço IP no intervalo da VNet em que foi implantado (10.1.3.0/24):

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="criar link privado" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Verificar a conectividade da mesma VNet

Neste exemplo, verificaremos a conectividade com o banco de dados SQL do Azure de uma máquina virtual Ubuntu com ferramentas MS SQL instaladas. A primeira etapa é verificar se a resolução de DNS funciona e o nome de domínio totalmente qualificado do banco de dados SQL do Azure é resolvido para um endereço IP privado, na mesma VNet em que o ponto de extremidade privado foi implantado (10.1.3.0/24):

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Como você pode ver na saída anterior, o FQDN `wantest.database.windows.net` é mapeado para `wantest.privatelink.database.windows.net` , que a zona DNS privada criada ao longo do ponto de extremidade privado será resolvida para o endereço IP privado `10.1.3.228` . Examinar a zona DNS privada confirmará que há um registro A para o ponto de extremidade privado mapeado para o endereço IP privado:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="criar link privado" lightbox="./media/howto-private-link/dns-zone.png":::

Depois de verificar a resolução DNS correta, podemos tentar se conectar ao banco de dados:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Como você pode ver, estamos usando uma consulta SQL especial que nos fornece o endereço IP de origem que o SQL Server vê do cliente. Nesse caso, o servidor vê o cliente com seu IP privado ( `10.1.3.75` ), o que significa que o tráfego vai da VNet diretamente para o ponto de extremidade privado.

Observe que você precisa definir as variáveis `username` e `password` para corresponder às credenciais definidas no banco de dados SQL do Azure para fazer com que os exemplos neste guia funcionem.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>Conectar-se de uma VNet diferente

Agora que uma VNet na WAN virtual do Azure tem conectividade com o ponto de extremidade privado, todos os outros VNets e branches conectados à WAN virtual também podem ter acesso a ele. Você precisa fornecer conectividade por meio de qualquer um dos modelos com suporte pela WAN virtual do Azure, como o cenário de [qualquer para qualquer](scenario-any-to-any.md) ou o [cenário de VNet de serviços compartilhados](scenario-shared-services-vnet.md), para nomear dois exemplos.

Depois de ter conectividade entre a VNet ou a ramificação para a VNet em que o ponto de extremidade privado foi implantado, você precisa configurar a resolução DNS:

* Se estiver se conectando ao ponto de extremidade privado de uma VNet, você poderá usar a mesma zona privada que foi criada com o banco de dados SQL do Azure.
* Se estiver se conectando ao ponto de extremidade privado de um Branch (VPN site a site, VPN ponto a site ou ExpressRoute), você precisará usar a resolução de DNS local.

Neste exemplo, vamos nos conectar de uma VNet diferente, portanto, primeiro anexaremos a zona DNS privada à nova VNet para que suas cargas de trabalho possam resolver o nome de domínio totalmente qualificado do banco de dados SQL do Azure para o endereço IP privado. Isso é feito por meio da vinculação da zona DNS privada à nova VNet:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="criar link privado" lightbox="./media/howto-private-link/dns-link.png":::

Agora, qualquer máquina virtual na VNet conectada deve resolver corretamente o FQDN do banco de dados SQL do Azure para o endereço IP privado do link privado:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Para verificar se essa VNet (10.1.1.0/24) tem conectividade com a VNet original em que o ponto de extremidade privado foi configurado (10.1.3.0/24), você pode verificar a tabela de rotas em vigor em qualquer máquina virtual na VNet:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="criar link privado" lightbox="./media/howto-private-link/effective-routes.png":::

Como você pode ver, há uma rota apontando para a VNet 10.1.3.0/24 injetada pelos gateways de rede virtual na WAN virtual do Azure. Agora, podemos finalmente testar a conectividade com o banco de dados:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

Com este exemplo, vimos como a criação de um ponto de extremidade privado em um dos VNets anexados a uma WAN virtual fornece conectividade ao restante de VNets e ramificações na WAN virtual.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
