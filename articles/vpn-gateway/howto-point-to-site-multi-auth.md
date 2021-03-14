---
title: 'Conectar-se a uma VNet usando P2S VPN & vários tipos de autenticação: Portal'
titleSuffix: Azure VPN Gateway
description: Conecte-se a uma VNet via P2S usando vários tipos de autenticação no portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744214"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Configure uma conexão VPN ponto a site para uma VNet usando vários tipos de autenticação: portal do Azure

Este artigo ajuda você a conectar com segurança clientes individuais que executam Windows, Linux ou macOS a uma VNet do Azure. As conexões VPN Ponto a Site são úteis quando você deseja se conectar à rede virtual de um local remoto, como ao trabalhar de casa ou em uma conferência. Também é possível usar P2S em vez de uma VPN Site a Site, quando você tiver apenas alguns clientes que precisam se conectar a uma VNet. As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público. A P2S cria a conexão VPN no SSTP (Secure Socket Tunneling Protocol) ou IKEv2. Para obter mais informações sobre conexões VPN Ponto a Site, consulte [Sobre VPN Ponto a Site](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Conectar-se de um computador a um diagrama de conexão de ponto a site da VNet do Azure":::

Para obter mais informações sobre VPN ponto a site, consulte [sobre VPN ponto a site](point-to-site-about.md). Para criar essa configuração usando o Azure PowerShell, consulte [Configurar uma VPN ponto a site usando Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

Há suporte para vários tipos de autenticação no mesmo gateway de VPN com o tipo de túnel OpenVPN.

### <a name="example-values"></a><a name="example"></a>Valores de exemplo

Você pode usar os seguintes valores para criar um ambiente de teste ou fazer referência a esses valores para entender melhor os exemplos neste artigo:

* **Nome da VNet:** VNet1
* **Espaço de endereço:** 10.1.0.0/16<br>Neste exemplo, usamos apenas um espaço de endereço. Você pode ter mais de um espaço de endereço para sua rede virtual.
* **Nome da sub-rede:** FrontEnd
* **Intervalo de endereços da sub-rede:** 10.1.0.0/24
* **Assinatura:** verifique se você tem mais de uma assinatura, verifique se está usando a correta.
* **Grupo de recursos:** TestRG1
* **Localização:** Leste dos EUA
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Nome do gateway de rede virtual:** VNet1GW
* **Tipo de gateway:** VPN
* **Tipo de VPN:** Baseado em rotas
* **Endereço IP público:** VNet1GWpip
* **Tipo de conexão:** ponto a site
* **Pool de endereços do cliente:** 172.16.201.0/24<br>Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP do pool de endereços do cliente.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Criar uma rede virtual

Antes de começar, verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Gateway de rede virtual

Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

>[!NOTE]
>O SKU do gateway básico não dá suporte ao tipo de túnel OpenVPN.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Pool de endereços do cliente

O pool de endereços do cliente é um intervalo de endereços IP que você especificar. Os clientes que se conectam por VPN ponto a site recebem dinamicamente um endereço IP desse intervalo. Use um intervalo de endereço IP privado que não coincida com o local de onde você se conecta ou com a rede virtual à qual você deseja se conectar. Se você configurar vários protocolos e o SSTP for um dos protocolos, o pool de endereços configurado será dividido entre os protocolos configurados igualmente.

1. Quando o gateway de rede virtual tiver sido criado, navegue até a seção **Configurações** da página do gateway de rede virtual. Em **configurações**, selecione **configuração ponto a site**. Selecione **Configurar agora** para abrir a página de configuração.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Captura de tela da página de configuração ponto a site." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Na página **configuração ponto a site** , você pode configurar uma variedade de configurações. Na caixa **pool de endereços** , adicione o intervalo de endereços IP privado que você deseja usar. Os clientes VPN recebem dinamicamente um endereço IP do intervalo que você especificar. A máscara de sub-rede mínima é 29 bits para ativo/passivo e 28 bits para configuração ativa/ativa.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Captura de tela do pool de endereços.":::

1. Continue na próxima seção para configurar a autenticação e os tipos de túnel.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Autenticação e tipos de túnel

Nesta seção, você configura o tipo de autenticação e o tipo de túnel. Na página **configuração ponto a site** , se você não vir o tipo de **túnel** ou **tipo de autenticação**, o gateway estará usando o SKU básico. A SKU Básica não dá suporte à autenticação IKEv2 ou RADIUS. Se você quiser usar essas configurações, será necessário excluir e recriar o gateway usando um SKU de gateway diferente.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Captura de tela do tipo de autenticação.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tipo de túnel

Na página **configuração ponto a site** , selecione **OpenVPN (SSL)** como o tipo de túnel.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Tipo de autenticação

Para **tipo de autenticação**, selecione os tipos desejados. As opções são:

* Certificado do Azure
* RAIO
* Azure Active Directory

Dependendo dos tipos de autenticação selecionados, você verá campos de configuração diferentes que precisarão ser preenchidos. Preencha as informações necessárias e selecione **salvar** na parte superior da página para salvar todas as definições de configuração.

Para obter mais informações sobre o tipo de autenticação, consulte:

* [Certificado do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RAIO](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Pacote de configuração de cliente VPN

Os clientes VPN devem ser definidos com as definições de configuração do cliente. O pacote de configuração de cliente VPN contém arquivos com as configurações para configurar clientes VPN a fim de se conectar a uma rede virtual por meio de uma conexão P2S.

Para obter instruções para gerar e instalar arquivos de configuração de cliente VPN, use o artigo que pertence à sua configuração:

* [Criar e instalar arquivos de configuração de cliente VPN para configurações nativas de P2S de autenticação de certificado do Azure](point-to-site-vpn-client-configuration-azure-cert.md).
* [Autenticação de Azure Active Directory: Configure um cliente VPN para conexões de protocolo P2S OpenVPN](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>Perguntas frequentes sobre Ponto a site

Esta seção contém informações de perguntas frequentes relacionadas a configurações de ponto a site. Você também pode exibir as [perguntas frequentes do gateway de VPN](vpn-gateway-vpn-faq.md) para obter informações adicionais sobre o gateway de VPN.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](../index.yml). Para saber mais sobre redes e máquinas virtuais, consulte [Visão geral de rede do Azure e VM Linux](../virtual-machines/network-overview.md).

Para obter informações sobre solução de problemas de P2S, consulte [Solução de problemas de conexões de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
