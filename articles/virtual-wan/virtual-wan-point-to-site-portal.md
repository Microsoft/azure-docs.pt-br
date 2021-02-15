---
title: 'Tutorial: Usar a WAN Virtual do Azure para criar uma conexão Ponto a Site com o Azure'
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar uma conexão VPN ponto a site com o Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: 1876ab86e6f4c46edc23361dd884d8b32328f36c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919052"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma conexão de VPN do Usuário usando a WAN Virtual do Azure

Este tutorial mostra como usar a WAN Virtual para se conectar-se aos recursos no Azure em uma conexão VPN IPsec/IKE (IKEv2) ou OpenVPN. Esse tipo de conexão exige que o cliente VPN seja configurado no computador cliente. Para saber mais sobre WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma WAN virtual
> * Criar uma configuração P2S
> * Criar um hub virtual
> * Escolher pools de endereços de cliente
> * Especificar servidores de DNS
> * Gerar um pacote de configuração do perfil de cliente VPN
> * Configurar clientes de VPN
> * Exibir a WAN virtual

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar uma WAN Virtual

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Criar uma configuração P2S

Uma configuração P2S (ponto a site) define os parâmetros para conexão de clientes remotos.

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>Criar um hub virtual e um gateway

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]


## <a name="choose-p2s-client-address-pools"></a><a name="chooseclientpools"></a> Escolher pools de endereços de cliente P2S

[!INCLUDE [Choose pools](../../includes/virtual-wan-allocating-p2s-pools.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Especificar servidor DNS

Você pode definir essa configuração durante o processo de criação do hub ou modificá-la posteriormente. Para modificar, localize o hub virtual. Em **VPN de Usuário (ponto a site)** , selecione **Configurar** e insira os endereços IP do servidor DNS nas caixas de texto **Servidores DNS Personalizados**. Você pode especificar até cinco servidores DNS.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personalizado" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>Gerar pacote de perfil do cliente VPN

Gere e baixe o pacote de perfil de cliente VPN para configurar os clientes VPN.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>Configurar clientes de VPN

Use o pacote de perfil baixado para configurar os clientes VPN de acesso remoto. O procedimento para cada sistema operacional é diferente. Siga as instruções que se aplicam ao sistema.
Depois de concluir a configuração do cliente, você poderá se conectar.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Exibir a WAN virtual

1. Navegue até a WAN virtual.
1. Na página **Visão Geral**, cada ponto no mapa representa um hub.
1. Na seção **Hubs e conexões**, você pode ver o status do hub, o site, a região, o status de conexão de VPN e os bytes de entrada e saída.



## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando não precisar mais desses recursos, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, para saber mais sobre a WAN Virtual, confira:

> [!div class="nextstepaction"]
> * [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md)
