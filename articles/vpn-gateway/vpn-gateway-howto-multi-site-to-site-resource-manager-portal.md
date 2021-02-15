---
title: 'Adicionar várias conexões site a site de gateway de VPN a uma VNet: portal do Azure'
description: Adicionar conexões S2S de vários sites a um gateway de VPN que tenha uma conexão existente
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "92890132"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>Adicionar conexões S2S adicionais a uma VNet: portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>

Este artigo ajuda você a adicionar conexões de site a site (S2S) adicionais a um gateway de VPN que tem uma conexão existente. Essa arquitetura é geralmente conhecida como uma configuração de "vários sites". Você pode adicionar uma conexão S2S a uma VNet que já tenha uma conexão S2S, uma conexão ponto a site ou uma conexão de VNet para VNet. Existem algumas limitações ao adicionar conexões. Verifique a seção [pré-requisitos](#before) deste artigo para verificar antes de iniciar a configuração.

Este artigo se aplica a VNets do Gerenciador de Recursos que têm um gateway de VPN RouteBased. Essas etapas não se aplicam a novas configurações de conexão coexistentes do ExpressRoute/site a site. No entanto, se você estiver simplesmente adicionando uma nova conexão VPN a uma configuração Coexist existente, poderá usar estas etapas. Confira [Conexões coexistentes ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obter informações sobre conexões coexistentes.

## <a name="prerequisites"></a><a name="before"></a>Pré-requisitos

Verifique os itens a seguir:

* Você não está configurando uma nova configuração de gateway de ExpressRoute e VPN coexistente.
* Você tem uma rede virtual que foi criada usando o modelo de implantação do Gerenciador de Recursos com uma conexão existente.
* O gateway de rede virtual para sua VNet é RouteBased. Se você tiver um gateway de VPN PolicyBased, deverá excluir o gateway de rede virtual e criar um novo gateway de VPN como RouteBased.
* Nenhum dos intervalos de endereço se sobrepõe a qualquer uma das VNets a que essa VNet está se conectando.
* Você possui um dispositivo VPN compatível e alguém que possa configurá-lo. Confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.
* Você possui um endereço IP público voltado para o exterior para seu dispositivo VPN.

## <a name="configure-a-connection"></a><a name="configure"></a>Configurar uma conexão

1. Em um navegador, navegue até a [portal do Azure](https://portal.azure.com) e, se necessário, entre com sua conta do Azure.
1. Selecione **todos os recursos** e localize seu **Gateway de rede virtual** na lista de recursos e selecione-o.
1. Na página **Gateway de rede virtual** , selecione **conexões**.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="Conexões de gateway de VPN":::
1. Na página **conexões** , selecione **+ Adicionar**.
1. Isso abre a página **Adicionar conexão** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="Página Adicionar conexão":::
1. Na página **Adicionar conexão** , preencha os seguintes campos:

   * **Nome:** o nome que deseja dar ao site para o qual você está criando a conexão.
   * **Tipo de conexão:** Selecione **site a site (IPSec)**.

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Adicionar um gateway de rede local

1. Para o campo **Gateway de rede local** , selecione **_escolher um gateway de rede local_*_. Isso abre a página _* escolher gateway de rede local** .
1. Selecione **+ criar novo** para abrir a página **criar gateway de rede local** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Página Criar gateway de rede local":::
1. Na página **Criar gateway de rede local** , preencha os seguintes campos:

   * **Nome:** o nome que você deseja atribuir ao seu recursos de gateway de rede local.
   * **Ponto de extremidade:** O endereço IP público do dispositivo VPN no site ao qual você deseja se conectar ou o FQDN do ponto de extremidade.
   * **Espaço de endereço:** o espaço de endereço que você deseja que seja roteado para o novo site de rede local.
1. Selecione **OK** na página **criar gateway de rede local** para salvar as alterações.

## <a name="add-the-shared-key"></a><a name="part3"></a>Adicionar a chave compartilhada

1. Depois de criar o gateway de rede local, retorne à página **Adicionar conexão** .
1. Preencha os campos restantes. Para a **chave compartilhada (PSK)** , você pode obter a chave compartilhada do seu dispositivo VPN ou criar uma aqui e, em seguida, configurar o dispositivo VPN para usar a mesma chave compartilhada. O importante é que as chaves sejam exatamente as mesmas.

## <a name="create-the-connection"></a><a name="create"></a>Criar a conexão

1. Na parte inferior da página, selecione **OK** para criar a conexão. A conexão começa a ser criada imediatamente.
1. Quando a conexão for concluída, você poderá exibi-la e verificá-la.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>Exibir e verificar a conexão VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para obter mais informações, consulte [caminhos de aprendizado de máquinas virtuais](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
