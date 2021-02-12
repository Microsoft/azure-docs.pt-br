---
title: 'Configurar uma conexão de gateway de VPN de VNet para VNet: portal do Azure'
description: Crie uma conexão de gateway de VPN entre redes virtuais usando o Resource Manager e o portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.openlocfilehash: 465d877da48e0d7027dbba6615302af32c6bb154
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872393"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Configurar uma conexão de gateway de VPN de VNet para VNet usando o portal do Azure

Este artigo ajuda você a conectar VNets (redes virtuais) usando o tipo de conexão de rede virtual a rede virtual. Redes virtuais podem estar em regiões diferentes e ser de assinaturas diferentes. Ao conectar VNets de assinaturas diferentes, as assinaturas não precisam ser associadas ao mesmo locatário do Active Directory. 

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet-vnet-diagram.png" alt-text="Diagrama de VNet para VNet":::

As etapas neste artigo se aplicam ao modelo de implantação do Azure Resource Manager e o uso do portal do Azure. Você pode criar essa configuração com um modelo ou ferramenta de implantação diferente usando as opções descritas nos artigos a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conectar modelos de implantação diferentes – portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conectar modelos de implantação diferentes - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Sobre a conexão de VNets

As seções a seguir descrevem as diferentes maneiras de conectar redes virtuais.

### <a name="vnet-to-vnet"></a>VNet a VNet

Configurar uma conexão de VNet a VNet é uma maneira simples de conectar VNets. Quando você conecta uma rede virtual a outra rede virtual com um tipo de conexão VNet2VNet (VNet a VNet), é semelhante a criar uma conexão IPsec Site a Site com uma localização local. Os dois tipos de conexão usam um gateway de VPN para fornecer um túnel seguro com IPsec/IKE e funcionam da mesma forma ao se comunicar. No entanto, diferem na maneira como gateway de rede local é configurado. 

Quando você cria uma conexão de VNet a VNet, o espaço de endereço de gateway de rede local é automaticamente criado e preenchido. Se você atualizar o espaço de endereço de uma VNet, a outra roteará automaticamente para o espaço de endereço atualizado. Normalmente, é mais rápido e fácil criar uma conexão de VNet a VNet do que uma conexão Site a Site.

### <a name="site-to-site-ipsec"></a>Site a Site (IPsec)

Se você estiver trabalhando com uma configuração de rede complicada, talvez prefira conectar suas VNets usando uma [Conexão Site a Site](./tutorial-site-to-site-portal.md). Quando você segue as etapas de IPsec Site a Site, cria e configura os gateways de rede local manualmente. O gateway de rede local para cada VNet trata a outra VNet como um site local. Estas etapas permitem que você especifique os espaços de endereço adicionais para o gateway de rede local para rotear o tráfego. Se o espaço de endereço para uma VNet mudar, você deverá atualizar manualmente o gateway de rede local correspondente.

### <a name="vnet-peering"></a>Emparelhamento VNet

Você também pode conectar suas VNets usando o Emparelhamento VNET. O Emparelhamento VNET não usa um gateway de VPN e tem restrições diferentes. Além disso, os [preços do emparelhamento VNet](https://azure.microsoft.com/pricing/details/virtual-network) são calculados de maneira diferente dos [preços de Gateway de VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Por que criar uma conexão de VNet a VNet?

Talvez você queira conectar redes virtuais usando uma conexão VNet a VNet pelos seguintes motivos:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Redundância geográfica entre regiões e presença geográfica

* Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade voltados para a Internet.
* Com o Azure Load Balancer e o Gerenciador de Tráfego do Azure você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Por exemplo, você pode configurar grupos de disponibilidade Always On do SQL Server em várias regiões do Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Aplicativos multicamadas regionais com limites administrativos ou de isolamento

* Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas devido aos requisitos administrativos ou de isolamento.

Você pode combinar a comunicação de VNet a VNet usando configurações multissite. Essas configurações permitem estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual, conforme mostrado no diagrama a seguir:

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections-diagram.png" alt-text="Diagrama de conexões VNet":::

Este artigo mostra como conectar VNets usando o tipo de conexão VNet a VNet. Ao realizar estas etapas como um exercício, você pode usar os valores de configurações de exemplo a seguir. No exemplo, as redes virtuais estão na mesma assinatura, mas em diferentes grupos de recursos. Se suas redes virtuais estiverem em assinaturas diferentes, não será possível criar a conexão no portal. Use [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) ou [CLI](vpn-gateway-howto-vnet-vnet-cli.md) em vez disso. Para obter mais informações sobre conexões de rede virtual para rede virtual, confira [Perguntas frequentes sobre VNet a VNet](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Configurações de exemplo

**Valores para VNet1:**

* **Configurações de rede virtual**
  * **Nome**: VNet1
  * **Espaço de endereço**: 10.1.0.0/16
  * **Assinatura**: Selecione a assinatura que você deseja usar.
  * **Grupo de recursos**: TestRG1
  * **Local**: leste dos EUA
  * **Sub-rede**
    * **Nome**: frontend
    * **Intervalo de endereços**: 10.1.0.0/24

* **Configurações de gateway de rede virtual**
  * **Nome**: VNet1GW
  * **Grupo de recursos**: leste dos EUA
  * **Geração**: geração 1
  * **Tipo de gateway**: selecione **VPN**.
  * **Tipo de VPN**: selecione **rota * com base**.
  * **SKU**: VpnGw1
  * **Rede virtual**: VNet1
  * **Intervalo de endereços da sub-rede do gateway**: 10.1.255.0/27
  * **Endereço IP público**: criar novo
  * **Nome do endereço IP público**: VNet1GWpip

* **Conexão**
  * **Nome**: VNet1toVNet4
  * **Chave compartilhada**: você pode criar a chave compartilhada por conta própria. Quando você cria a conexão entre as redes virtuais, os valores devem corresponder. Para este exercício, use abc123.

**Valores para VNet4:**

* **Configurações de rede virtual**
  * **Nome**: VNet4
  * **Espaço de endereço**: 10.41.0.0/16
  * **Assinatura**: Selecione a assinatura que você deseja usar.
  * **Grupo de recursos**: TestRG4
  * **Local**: oeste dos EUA
  * **Sub-rede**
  * **Nome**: frontend
  * **Intervalo de endereços**: 10.41.0.0/24

* **Configurações de gateway de rede virtual**
  * **Nome**: VNet4GW
  * **Grupo de recursos**: oeste dos EUA
  * **Geração**: geração 1
  * **Tipo de gateway**: selecione **VPN**.
  * **Tipo de VPN**: selecione **baseado em rota**.
  * **SKU**: VpnGw1
  * **Rede virtual**: VNet4
  * **Intervalo de endereços da sub-rede do gateway**: 10.41.255.0/27
  * **Endereço IP público**: criar novo
  * **Nome do endereço IP público**: VNet4GWpip

* **Conexão**
  * **Nome**: VNet4toVNet1
  * **Chave compartilhada**: você pode criar a chave compartilhada por conta própria. Quando você cria a conexão entre as redes virtuais, os valores devem corresponder. Para este exercício, use abc123.

## <a name="create-and-configure-vnet1"></a>Criar e configurar o VNet1

Se você já tiver uma rede virtual, verifique se as configurações são compatíveis com seu design de gateway de VPN. Preste atenção especial em todas as sub-redes que possam se sobrepor a outras redes. Sua conexão não funcionará corretamente se você tiver uma sobreposição de sub-redes.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Criar o gateway VNet1

Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado. Se você estiver criando esta configuração como um exercício, confira [Configurações de exemplo](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway da rede virtual

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Criar e configurar o VNet4

Depois de configurar o VNet1, crie VNet4 e o gateway VNet4 repetindo as etapas anteriores e substituindo os valores por valores de VNet4. Você não precisa esperar até que o gateway de rede virtual para VNet1 tenha concluído a criação antes de configurar o VNet4. Se você estiver usando seus próprios valores, verifique se os espaços de endereço não se sobrepõem a nenhuma das redes virtuais às quais você deseja se conectar.

## <a name="configure-the-vnet1-gateway-connection"></a>Configurar a conexão de gateway VNet1

Quando os gateways de rede virtual para VNet1 e VNet4 tiverem sido concluídos, você poderá criar suas conexões de gateway de rede virtual. Nesta seção, você cria uma conexão de VNet1 para VNet4. Estas etapas funcionam apenas para as VNets na mesma assinatura. Se as VNets estiverem em assinaturas diferentes, você deverá usar o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) para fazer a conexão. No entanto, se as redes virtuais estiverem em grupos de recursos diferentes na mesma assinatura, você poderá conectá-las usando o portal.

1. No portal do Azure, selecione **Todos os recursos**, insira *gateway de rede virtual* na pesquisa de caixa e, em seguida, navegue até o gateway de rede virtual para sua rede virtual. Por exemplo, **VNet1GW**. Selecione o gateway para abrir a página de **Gateway de rede virtual** .
1. Na página gateway, acesse **configurações->conexões**. Em seguida, selecione **+ Adicionar**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png" alt-text="Página conexões":::
1. A página **Adicionar conexão** é aberta.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4.png" alt-text="Adicionar Conexão":::

   Na página **Adicionar conexão**, preencha os valores para sua conexão:

   * **Nome**: Insira um nome para a conexão. Por exemplo, *VNet1toVNet4*.

   * **Tipo de conexão**: selecione **vnet a vnet** na lista suspensa.

   * **Primeiro gateway de rede virtual**: esse valor de campo é preenchido automaticamente porque você está criando essa conexão do gateway de rede virtual especificado.

   * **Segundo gateway de rede virtual**: esse campo é o gateway de rede virtual da VNet ao qual você deseja criar uma conexão. Selecione **Escolher outro gateway de rede virtual** para abrir a página **Escolher gateway de rede virtual**.

      :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/choose.png" alt-text="Escolher um gateway":::

     * Exiba os gateways de rede virtual listados nessa página. Observe que somente os gateways de rede virtual que estão em sua assinatura são listados. Se você quiser se conectar a um gateway de rede virtual que não esteja em sua assinatura, use o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     * Selecione o gateway de rede virtual ao qual você deseja se conectar.

   * **Chave compartilhada (PSK)**: nesse campo, insira uma chave compartilhada para sua conexão. Você pode gerar ou criar essa chave. Em uma conexão site a site, a chave usada é a mesma que para o dispositivo local e a conexão de gateway de rede virtual. O conceito é semelhante aqui, exceto que, em vez de se conectar a um dispositivo VPN, você se conectará a outro gateway de rede virtual.
1. Selecione **OK** para salvar suas alterações.

## <a name="configure-the-vnet4-gateway-connection"></a>Configurar a conexão de gateway VNet4

Em seguida, crie uma conexão de VNet4 para VNet1. No portal, localize o gateway de rede virtual associado ao VNet4. Siga as etapas da seção anterior, substituindo os valores para criar uma conexão de VNet4 para VNet1. Use a mesma chave compartilhada.

## <a name="verify-your-connections"></a>Verificar as conexões

1. Localize o gateway de rede virtual no portal do Azure. 
1. Na página **Gateway de rede virtual**, selecione **Conexões** para exibir a página **Conexões** para o gateway de rede virtual. Depois que a conexão for estabelecida, você verá os valores de **status** mudarem para **conectado**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png" alt-text="Verificar conexões":::
1. Na coluna **nome** , selecione uma das conexões para exibir mais informações. Quando dados começarem a fluir, você verá valores de **Entrada de dados** e de **Saída de dados**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png" alt-text="Captura de tela mostra um grupo de recursos com valores de entrada e saída de dados":::

## <a name="add-additional-connections"></a>Adicionar conexões extras

Se desejar adicionar conexões extras, navegue até o gateway de rede virtual do qual deseja criar a conexão e selecione **Conexões**. Você pode criar outra conexão de rede virtual a rede virtual ou criar uma conexão Site a site (IPsec) para um lugar local. Verifique se você ajustou o **Tipo de conexão** para que corresponda ao tipo de conexão que deseja criar. Antes de criar conexões adicionais, verifique se o espaço de endereço para sua rede virtual não se sobrepõe nenhum dos espaços de endereço aos quais você deseja se conectar. Para obter as etapas para criar uma conexão Site a site, consulte [Criar uma conexão Site a site](./tutorial-site-to-site-portal.md).

## <a name="vnet-to-vnet-faq"></a>Perguntas Frequentes sobre VNet a VNet

Exiba os detalhes de perguntas frequentes para obter informações adicionais sobre conexões de Rede Virtual para Rede Virtual.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como você pode limitar o tráfego de rede para recursos em uma rede virtual, confira [Segurança de Rede](../virtual-network/network-security-groups-overview.md).

* Para obter mais informações sobre como o Azure roteia o tráfego entre o Azure, locais e recursos da Internet, consulte [Roteamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).