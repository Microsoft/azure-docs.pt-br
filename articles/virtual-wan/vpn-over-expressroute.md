---
title: 'Configurar a criptografia do ExpressRoute: IPsec sobre o ExpressRoute para WAN virtual do Azure'
description: Saiba como usar a WAN virtual do Azure para criar uma conexão VPN site a site por meio do emparelhamento privado do ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: b8dde3ed76587e2343edaec8626287853ec6ef9b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487500"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Criptografia do ExpressRoute: IPsec sobre ExpressRoute para WAN virtual

Este artigo mostra como usar a WAN virtual do Azure para estabelecer uma conexão VPN IPsec/IKE de sua rede local para o Azure por meio do emparelhamento privado de um circuito do Azure ExpressRoute. Essa técnica pode fornecer um trânsito criptografado entre as redes locais e as redes virtuais do Azure no ExpressRoute, sem passar pela Internet pública ou usar endereços IP públicos.

## <a name="topology-and-routing"></a>Topologia e roteamento

O diagrama a seguir mostra um exemplo de conectividade de VPN sobre o emparelhamento privado do ExpressRoute:

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="VPN no ExpressRoute":::

O diagrama mostra uma rede na rede local conectada ao gateway de VPN do Hub do Azure sobre o emparelhamento privado do ExpressRoute. O estabelecimento da conectividade é simples:

1. Estabeleça a conectividade do ExpressRoute com um circuito de ExpressRoute e emparelhamento privado.
2. Estabeleça a conectividade VPN, conforme descrito neste artigo.

Um aspecto importante dessa configuração é o roteamento entre as redes locais e o Azure por meio dos caminhos de ExpressRoute e VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Tráfego de redes locais para o Azure

Para o tráfego de redes locais para o Azure, os prefixos do Azure (incluindo o Hub virtual e todas as redes virtuais spoke conectadas ao Hub) são anunciados por meio do BGP de emparelhamento privado do ExpressRoute e do BGP VPN. Isso resulta em duas rotas de rede (caminhos) para o Azure a partir das redes locais:

- Um no caminho protegido por IPsec
- Um diretamente sobre o ExpressRoute *sem* proteção de IPSec 

Para aplicar a criptografia à comunicação, você deve certificar-se de que, para a rede conectada VPN no diagrama, as rotas do Azure por meio do gateway de VPN local são preferenciais sobre o caminho direto do ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Tráfego do Azure para redes locais

O mesmo requisito se aplica ao tráfego do Azure para redes locais. Para garantir que o caminho IPsec seja preferencial em relação ao caminho direto do ExpressRoute (sem IPsec), você tem duas opções:

- Anuncie prefixos mais específicos na sessão BGP VPN para a rede conectada VPN. Você pode anunciar um intervalo maior que abrange a rede conectada VPN por meio do emparelhamento privado do ExpressRoute e intervalos mais específicos na sessão BGP VPN. Por exemplo, Anunci 10.0.0.0/16 por meio do ExpressRoute e 10.0.1.0/24 pela VPN.

- Anuncie prefixos não contíguos para VPN e ExpressRoute. Se os intervalos de rede conectados à VPN forem separados de outras redes conectadas do ExpressRoute, você poderá anunciar os prefixos nas sessões VPN e BGP do ExpressRoute, respectivamente. Por exemplo, Anunci 10.0.0.0/24 por meio do ExpressRoute e 10.0.1.0/24 pela VPN.

Em ambos os exemplos, o Azure enviará tráfego para 10.0.1.0/24 pela conexão VPN em vez de diretamente pelo ExpressRoute sem proteção de VPN.

> [!WARNING]
> Se você anunciar os *mesmos* prefixos em conexões de EXPRESSROUTE e VPN, o Azure usará o caminho do expressroute diretamente sem a proteção de VPN.
>

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. criar uma WAN virtual e um hub com gateways

Os seguintes recursos do Azure e as configurações locais correspondentes devem estar em vigor antes de você prosseguir:

- Uma WAN virtual do Azure
- Um hub de WAN virtual com um [Gateway de ExpressRoute](virtual-wan-expressroute-portal.md) e um gateway de [VPN](virtual-wan-site-to-site-portal.md)

Para obter as etapas para criar uma WAN virtual do Azure e um hub com uma associação de ExpressRoute, consulte [criar uma associação de expressroute usando a WAN virtual do Azure](virtual-wan-expressroute-portal.md). Para obter as etapas para criar um gateway de VPN na WAN virtual, consulte [criar uma conexão site a site usando a WAN virtual do Azure](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. criar um site para a rede local

O recurso do site é o mesmo que os sites VPN não ExpressRoute para uma WAN virtual. O endereço IP do dispositivo VPN local agora pode ser um endereço IP privado ou um endereço IP público na rede local acessível por meio do emparelhamento privado do ExpressRoute criado na etapa 1.

> [!NOTE]
> O endereço IP para o dispositivo VPN local *deve* fazer parte dos prefixos de endereço anunciados para o Hub Wan virtual por meio do emparelhamento privado do Azure ExpressRoute.
>

1. Vá para o portal do Azure em seu navegador. 
1. Selecione o Hub que você criou. Na página Hub de WAN virtual, em **conectividade**, selecione **sites VPN**.
1. Na página **sites VPN** , selecione **+ criar site**.
1. Na página **Criar site**, preencha os seguintes campos:
   * **Assinatura**: Verifique a assinatura.
   * **Grupo de recursos**: selecione ou crie o grupo de recursos que você deseja usar.
   * **Região**: Insira a região do Azure para o recurso de site de VPN.
   * **Nome**: Insira o nome pelo qual você deseja fazer referência ao site local.
   * **Fornecedor do dispositivo**: Insira o fornecedor do dispositivo VPN local.
   * **Border Gateway Protocol**: selecione "habilitar" se sua rede local usar o BGP.
   * **Espaço de endereço privado**: Insira o espaço de endereço IP que está localizado no site local. O tráfego destinado a esse espaço de endereço é roteado para a rede local por meio do gateway de VPN.
   * **Hubs**: selecione um ou mais hubs para conectar este site de VPN. Os hubs selecionados devem ter gateways de VPN já criados.
1. Selecione **Avançar: Links >** para as configurações do link VPN:
   * **Nome do link**: o nome pelo qual você deseja fazer referência a essa conexão.
   * **Nome do provedor**: o nome do provedor de serviços de Internet para este site. Para uma rede local do ExpressRoute, é o nome do provedor de serviços do ExpressRoute.
   * **Velocidade**: a velocidade do link do serviço de Internet ou do circuito do ExpressRoute.
   * **Endereço IP**: o endereço IP público do dispositivo VPN que reside em seu site local. Ou, para o ExpressRoute local, é o endereço IP privado do dispositivo VPN por meio do ExpressRoute.

   Se o BGP estiver habilitado, ele será aplicado a todas as conexões criadas para este site no Azure. Configurar o BGP em uma WAN virtual é equivalente a configurar o BGP em um gateway de VPN do Azure. 
   
   Seu endereço de par de BGP local *não deve* ser o mesmo que o endereço IP de sua VPN para o dispositivo ou o espaço de endereço de rede virtual do site de VPN. Use um endereço IP diferente no dispositivo VPN para o IP de par no nível de protocolo BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. No entanto, ele *não pode* ser um APIPA (169,254.*x*. *x*). Especifique esse endereço no site VPN correspondente que representa o local. Para saber os pré-requisitos do BGP, confira [Sobre o BGP com o Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Selecione **Avançar: examinar + criar >** para verificar os valores de configuração e criar o site VPN. Se você selecionou **hubs** para se conectar, a conexão será estabelecida entre a rede local e o gateway de VPN de Hub.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. atualize a configuração de conexão VPN para usar o ExpressRoute

Depois de criar o site VPN e conectar-se ao Hub, use as seguintes etapas para configurar a conexão para usar o emparelhamento privado do ExpressRoute:

1. Volte para a página de recursos de WAN virtual e selecione o recurso de Hub. Ou navegue do site VPN para o Hub conectado.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="Selecione um hub":::
1. Em **conectividade**, selecione **VPN (site a site)**.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="Selecionar VPN (site a site)":::
1. Selecione as reticências (**...**) no site VPN no ExpressRoute e selecione **Editar conexão VPN para esse Hub**.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="Inserir o menu de configuração":::
1. Para **usar o endereço IP privado do Azure**, selecione **Sim**. A configuração configura o gateway de VPN do hub para usar endereços IP privados dentro do intervalo de endereços do Hub no gateway para essa conexão, em vez dos endereços IP públicos. Isso garantirá que o tráfego da rede local percorra os caminhos de emparelhamento privado do ExpressRoute em vez de usar a Internet pública para essa conexão VPN. A captura de tela a seguir mostra a configuração:

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="Configuração para usar um endereço IP privado para a conexão VPN" border="false":::
1. Selecione **Salvar**.

Depois de salvar as alterações, o gateway de VPN de Hub usará os endereços IP privados no gateway de VPN para estabelecer as conexões IPsec/IKE com o dispositivo VPN local no ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. obter os endereços IP privados para o gateway de VPN do Hub

Baixe a configuração do dispositivo VPN para obter os endereços IP privados do gateway de VPN do Hub. Você precisa desses endereços para configurar o dispositivo VPN local.

1. Na página do Hub, selecione **VPN (site a site)** em **conectividade**.
1. Na parte superior da página **visão geral** , selecione **baixar configuração de VPN**. 

   O Azure cria uma conta de armazenamento no grupo de recursos "Microsoft-Network-[Location]", em que *Location* é o local da WAN. Depois de aplicar a configuração aos dispositivos VPN, você poderá excluir essa conta de armazenamento.
1. Depois que o arquivo for criado, selecione o link para baixá-lo.
1. Aplique a configuração ao dispositivo VPN.

### <a name="vpn-device-configuration-file"></a>Arquivo de configuração do dispositivo VPN

O arquivo de configuração do dispositivo contém as configurações a serem usadas quando você estiver configurando seu dispositivo VPN local. Ao exibir esse arquivo, observe as seguintes informações:

* **vpnSiteConfiguration**: Esta seção denota os detalhes do dispositivo configurados como um site que está se conectando à WAN virtual. Ele inclui o nome e o endereço IP público do dispositivo de ramificação.
* **vpnSiteConnections**: Esta seção fornece informações sobre as seguintes configurações:

    * Espaço de endereço da rede virtual do Hub virtual.<br/>Exemplo:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Espaço de endereço das redes virtuais que estão conectadas ao Hub.<br>Exemplo:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * Endereços IP do gateway de VPN do Hub virtual. Como cada conexão do gateway de VPN é composta de dois túneis na configuração ativa-ativa, você verá os dois endereços IP listados nesse arquivo. Neste exemplo, você vê `Instance0` e `Instance1` para cada site e são endereços IP privados em vez de endereços IP públicos.<br>Exemplo:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Detalhes de configuração para a conexão de gateway de VPN, como BGP e chave pré-compartilhada. A chave pré-compartilhada é gerada automaticamente para você. Você sempre pode editar a conexão na página **visão geral** de uma chave pré-compartilhada personalizada.
  
### <a name="example-device-configuration-file"></a>Exemplo de arquivo de configuração de dispositivo

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Configurar o dispositivo VPN

Se você precisar de instruções para configurar o dispositivo, poderá usar as instruções sobre a [página de scripts de configuração de dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) com as seguintes condições:

* As instruções na página do dispositivo VPN não são gravadas para uma WAN virtual. Mas você pode usar os valores de WAN virtual do arquivo de configuração para configurar manualmente seu dispositivo VPN. 
* Os scripts de configuração do dispositivo para download que são para o gateway de VPN não funcionam para a WAN virtual, pois a configuração é diferente.
* Uma nova WAN virtual pode dar suporte A IKEv1 e IKEv2.
* Uma WAN virtual pode usar somente dispositivos VPN baseados em rota e instruções de dispositivo.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. exibir sua WAN virtual

1. Vá para a WAN virtual.
1. Na página **Visão Geral**, cada ponto no mapa representa um hub.
1. Na seção **hubs e conexões** , você pode exibir o status do Hub, do site, da região e da conexão VPN. Você também pode exibir bytes de entrada e saída.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. monitorar uma conexão

Crie uma conexão para monitorar a comunicação entre uma VM (máquina virtual) do Azure e um site remoto. Para obter informações sobre como configurar um monitor de conexão, consulte [Monitorar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o IP da VM no Azure e o IP de destino é o IP do site.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. Limpar os recursos

Quando você não precisar mais desses recursos, poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Execute o seguinte comando do PowerShell e substitua `myResourceGroup` pelo nome do seu grupo de recursos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Este artigo ajuda você a criar uma conexão VPN no emparelhamento privado do ExpressRoute usando a WAN virtual. Para saber mais sobre WAN virtual e recursos relacionados, consulte [visão geral da WAN virtual](virtual-wan-about.md).
