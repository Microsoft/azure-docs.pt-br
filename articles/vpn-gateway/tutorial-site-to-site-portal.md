---
title: 'Tutorial – Conectar a rede local a uma rede virtual: Portal do Azure'
description: Crie uma conexão IPsec de Gateway de VPN site a site da sua rede local com uma rede virtual do Azure pela Internet pública usando o portal.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945199"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>Tutorial: Criar uma conexão Site a Site no portal do Azure

As conexões VPN do Microsoft Azure fornecem conectividade segura entre os locais do cliente e o Microsoft Azure. Este tutorial mostra como usar o portal do Azure para criar uma conexão de gateway de VPN Site a Site de sua rede local para a rede virtual. Você também pode criar essa configuração usando o [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) ou a [CLI do Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md).

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Diagrama de conexão Site a Site de Gateway de VPN entre locais":::

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de VPN
> * Criar um gateway de rede local
> * Criar uma conexão VPN
> * Verificar conexão
> * Conectar-se a uma máquina virtual

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. Caso não tenha uma, [crie uma gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Verifique se você possui um dispositivo VPN compatível e alguém que possa configurá-lo. Para obter mais informações sobre dispositivos VPN compatíveis e a configuração de dispositivo, confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).
* Verifique se você possui um endereço IPv4 público voltado para o exterior para seu dispositivo VPN.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede local, você precisará trabalhar em conjunto com alguém que possa lhe fornecer os detalhes. Ao criar essa configuração, você deve especificar os prefixos de intervalo de endereços IP que o Azure roteará para seu local. Nenhuma das sub-redes da rede local podem se sobrepor às sub-redes de rede virtual às quais você deseja se conectar.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Criar uma rede virtual

Crie uma VNet (rede virtual) usando os seguintes valores:

* **Grupo de recursos:** TestRG1
* **Nome:** VNet1
* **Região:** (EUA) Leste dos EUA
* **Espaço de endereço IPv4:** 10.1.0.0/16
* **Nome da sub-rede:** FrontEnd
* **Espaço de endereço de sub-rede:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Criar um gateway de VPN

Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

### <a name="about-the-gateway-subnet"></a>Sobre a sub-rede de gateway

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Criar o gateway

Crie um gateway de VPN usando os seguintes valores:

* **Nome:** VNet1GW
* **Região:** Leste dos EUA
* **Tipo de gateway:** VPN
* **Tipo de VPN:** Baseado em rotas
* **SKU:** VpnGw1
* **Geração:** Geração 1
* **Rede virtual:** VNet1
* **Intervalo de endereços da sub-rede do gateway:** 10.1.255.0/27
* **Endereço IP público:** Criar Novo
* **Endereço IP público:** VNet1GWpip
* **Habilitar o modo ativo-ativo:** Desabilitado
* **Configurar BGP:** Desabilitado

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>Exibir o endereço IP público

Você pode ver o endereço IP público do gateway na página **Visão geral** para seu gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Página de visão geral":::

Para ver informações adicionais sobre o objeto de endereço IP público, clique no link nome/endereço IP ao lado de **Endereço IP público**.

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Criar um gateway de rede local

O gateway de rede local é um objeto específico que representa sua localização local (o site) para fins de roteamento. Você atribui um nome ao site pelo qual o Azure pode fazer referência a ele e especifica o endereço IP do dispositivo VPN local para o qual você criará uma conexão. Você também pode especificar os prefixos de endereço IP que serão roteados por meio do gateway de VPN para o dispositivo VPN. Os prefixos de endereço que você especifica são os prefixos localizados em sua rede local. Se as alterações de rede local ou se você precisar alterar o endereço IP público para o dispositivo VPN, poderá atualizar facilmente os valores mais tarde.

Crie um gateway de rede local usando os seguintes valores:

* **Nome:** Site1
* **Grupo de recursos:** TestRG1
* **Localização:** Leste dos EUA

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Configurar o dispositivo VPN

As conexões Site a Site para uma rede local exigem um dispositivo VPN. Nesta etapa, você deve configurar seu dispositivo VPN. Ao configurar seu dispositivo VPN, você precisará dos seguintes valores:

* Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada básica. Recomendamos gerar uma chave mais complexa para uso.
* O endereço IP público do seu gateway de rede virtual. Você pode exibir o endereço IP público usando o portal do Azure, o PowerShell ou a CLI. Para localizar o endereço IP público do seu gateway de VPN usando o portal do Azure, navegue até **Gateways de rede virtual** e selecione o nome do seu gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Criar uma conexão VPN

Crie a conexão VPN Site a Site entre o gateway de rede virtual e o dispositivo VPN local.

Crie uma conexão usando os seguintes valores:

* **Nome do gateway de rede local:** Site1
* **Nome da conexão:** VNet1toSite1
* **Chave compartilhada:** para esse exemplo, usaremos abc123. Mas você pode usar o que for compatível com o hardware de VPN. O importante é que os valores correspondam em ambos os lados da conexão.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>Verificar a conexão VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>Conectar-se a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>Etapas opcionais

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>Adicionar conexões adicionais com o gateway

Você pode inserir conexões adicionais desde que nenhum dos espaços de endereço se sobreponham entre conexões.

1. Para inserir uma conexão adicional, navegue até o gateway de VPN e selecione **Conexões** para abrir a página Conexões.
1. Selecione **+Adicionar** para adicionar sua conexão. Ajuste o tipo de conexão para refletir cada rede virtual para rede virtual (se conectando a outro gateway de rede virtual) ou site a site.
1. Se você estiver se conectando usando o Site a site e ainda não criou um gateway de rede local para o site ao qual deseja se conectar, é possível criar um novo.
1. Especifique a chave compartilhada que deseja usar e depois selecione **OK** para criar a conexão.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>Redimensionar um SKU de gateway

Há regras específicas sobre o redimensionamento versus a alteração de um SKU de gateway. Nesta seção, redimensionaremos a SKU. Para obter mais informações, confira [Configurações de gateway – redimensionar e alterar SKUs](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>Redefinir um gateway

Redefinir um gateway de VPN do Azure é útil se você perde a conectividade VPN entre locais em um ou mais túneis de VPN site a site. Nessa situação, os dispositivos VPN locais estão funcionando corretamente, mas não são capazes de estabelecer túneis IPsec com os gateways de VPN do Azure.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>Considerações de configuração adicionais

As configurações S2S podem ser personalizadas de várias maneiras. Para obter mais informações, consulte os seguintes artigos:

* Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Para obter mais informações sobre túneis forçados, consulte [Sobre o túnel forçado](vpn-gateway-forced-tunneling-rm.md).
* Para obter informações sobre Conexões Altamente Disponíveis Ativo-Ativo, consulte [Conectividade Altamente Disponível entre locais e Rede Virtual para Rede Virtual](vpn-gateway-highlyavailable.md).
* Para obter mais informações sobre como limitar o tráfego de rede para recursos em uma rede virtual, consulte [Segurança de Rede](../virtual-network/network-security-groups-overview.md).
* Para obter mais informações sobre como o Azure roteia o tráfego entre o Azure, locais e recursos da Internet, consulte [Roteamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não for continuar usando este aplicativo ou for para o próximo tutorial, exclua esses recursos seguindo as seguintes etapas:

1. Insira o nome do grupo de recursos na caixa **Pesquisar** na parte superior do portal e selecione-o nos resultados da pesquisa.

1. Selecione **Excluir grupo de recursos**.

1. Insira seu grupo de recursos para obter a opção **DIGITAR O NOME DO GRUPO DE RECURSOS** e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar uma conexão site a site, você poderá adicionar uma conexão P2S ao mesmo gateway.

> [!div class="nextstepaction"]
> [Conexão VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
