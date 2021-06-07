---
title: 'Tutorial – Criar e gerenciar um Gateway de VPN: Portal do Azure'
description: Siga este tutorial para aprender a criar, implantar e gerenciar um Gateway de VPN do Azure usando o portal
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 820482a268af038737557c517ccda086cd65d943
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880552"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>Tutorial: Criar e gerenciar um gateway de VPN usando o portal do Azure

As conexões VPN do Microsoft Azure fornecem conectividade segura entre os locais do cliente e o Microsoft Azure. Este tutorial aborda itens básicos da implantação de gateway VPN do Azure como criar e gerenciar um gateway de VPN. Você também pode criar um gateway usando a [CLI do Azure](create-routebased-vpn-gateway-cli.md) ou o [Azure PowerShell](create-routebased-vpn-gateway-powershell.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de VPN
> * Exibir o endereço IP público do gateway
> * Redimensionar um gateway de VPN (redimensionar SKU)
> * Redefinir um gateway de VPN

O diagrama a seguir mostra a rede virtual e o gateway da VPN criados como parte deste tutorial.

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="Diagrama do gateway de VNet e VPN":::

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. Caso não tenha uma, [crie uma gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Criar uma rede virtual

Crie uma VNet usando os seguintes valores:

* **Grupo de recursos:** TestRG1
* **Nome:** VNet1
* **Região:** (EUA) Leste dos EUA
* **Espaço de endereço IPv4:** 10.1.0.0/16
* **Nome da sub-rede:** FrontEnd
* **Espaço de endereço de sub-rede:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Criar um gateway de VPN

Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

Crie um gateway de rede virtual usando os seguintes valores:

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

## <a name="view-the-public-ip-address"></a><a name="view"></a>Exibir o endereço IP público

Você pode ver o endereço IP público do gateway na página **Visão geral** para seu gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Página de visão geral":::

Para ver informações adicionais sobre o objeto de endereço IP público, clique no link nome/endereço IP ao lado de **Endereço IP público**.

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>Redimensionar um SKU de gateway

Há regras específicas sobre o redimensionamento versus a alteração de um SKU de gateway. Nesta seção, redimensionaremos a SKU. Para obter mais informações, confira [Configurações de gateway – redimensionar e alterar SKUs](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>Redefinir um gateway

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não for continuar usando este aplicativo ou for para o próximo tutorial, exclua esses recursos seguindo as seguintes etapas:

1. Insira o nome do grupo de recursos na caixa **Pesquisar** na parte superior do portal e selecione-o nos resultados da pesquisa.

1. Selecione **Excluir grupo de recursos**.

1. Insira seu grupo de recursos para obter a opção **DIGITAR O NOME DO GRUPO DE RECURSOS** e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Depois de ter um gateway de VPN, você poderá configurar conexões. Os seguintes artigos ajudarão você a criar algumas das configurações mais comuns:

> [!div class="nextstepaction"]
> [Conexões VPN site a site](./tutorial-site-to-site-portal.md)

> [!div class="nextstepaction"]
> [Conexão VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)