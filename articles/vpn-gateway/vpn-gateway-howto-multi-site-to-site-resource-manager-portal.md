---
title: 'Adicionar múltiplas conexões site a site ao Gateway de VPN a uma VNet: Portal do Azure: Resource Manager | Microsoft Docs'
description: Adicionar conexões S2S de vários sites a um gateway de VPN que tenha uma conexão existente
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 4b9f007e00d0912687b723bd4f7e747da893948d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60760392"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicione uma conexão Site a Site a uma rede virtual com uma conexão de gateway de VPN existente

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
> 

Este artigo ajuda você a usar conexões S2S (Site a Site) a um gateway de VPN que tenha uma conexão existente usando o portal do Azure. Esse tipo de conexão é frequentemente chamado de configuração de "vários sites". Você pode adicionar uma conexão S2S a uma VNet que já tenha uma conexão S2S, uma conexão ponto a site ou uma conexão de VNet para VNet. Existem algumas limitações ao adicionar conexões. Verifique a seção [Antes de começar](#before) neste artigo, antes de iniciar a configuração. 

Este artigo se aplica a VNets do Gerenciador de Recursos que têm um gateway de VPN RouteBased. Essas etapas não se aplicam a configurações de conexão coexistentes ExpressRoute/Site a Site. Confira [Conexões coexistentes ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obter informações sobre conexões coexistentes.

### <a name="deployment-models-and-methods"></a>Modelos e métodos de implantação
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela conforme novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo estiver disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Antes de começar
Verifique os itens a seguir:

* Você não está criando uma conexão ExpressRoute/S2S coexistente.
* Você tem uma rede virtual que foi criada usando o modelo de implantação do Gerenciador de Recursos com uma conexão existente.
* O gateway de rede virtual para sua VNet é RouteBased. Se você tiver um gateway de VPN PolicyBased, deverá excluir o gateway de rede virtual e criar um novo gateway de VPN como RouteBased.
* Nenhum dos intervalos de endereço se sobrepõe a qualquer uma das VNets a que essa VNet está se conectando.
* Você possui um dispositivo VPN compatível e alguém que possa configurá-lo. Confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.
* Você possui um endereço IP público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.

## <a name="part1"></a>Parte 1 - Configurar uma conexão
1. Em um navegador, navegue até o [portal do Azure](https://portal.azure.com) e, se necessário, entre com sua conta do Azure.
2. Clique em **Todos os recursos** e localize seu **gateway de rede virtual** na lista de recursos e clique nele.
3. Na página **Gateway de rede virtual**, clique em **Conexões**.
   
    ![Página Conexões](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Página Conexões")<br>
4. Na página **Conexões**, clique em **+Adicionar**.
   
    ![Adicionar botão de conexão](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Adicionar botão de conexão")<br>
5. Na página **Adicionar conexão**, preencha os seguintes campos:
   
   * **Nome:** O nome que você deseja dar ao site para o qual você está criando a conexão.
   * **Tipo de conexão**: Selecione **Site a site (IPsec)**.
     
     ![Adicionar página de conexão](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Adicionar página de conexão")<br>

## <a name="part2"></a>Parte 2 - Adicionar um gateway de rede local
1. Clique em **Gateway de rede Local** ***Escolher um gateway de rede local***. Isso abrirá a página **Escolher gateway de rede local**.
   
    ![Escolher gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Escolher gateway de rede local")<br>
2. Clique em **Criar novo** para abrir a página **Criar gateway de rede local**.
   
    ![Criar página de gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Criar gateway de rede local")<br>
3. Na página **Criar gateway de rede local**, preencha os seguintes campos:
   
   * **Nome:** O nome que você deseja atribuir aos seus recursos de gateway de rede local.
   * **Endereço IP:** Endereço IP público do dispositivo VPN no site ao qual você deseja se conectar.
   * **Espaço de endereço:** O espaço de endereço que você deseja que seja roteado para o novo site de rede local.
4. Clique em **OK** na página **Criar gateway de rede local** para salvar as alterações.

## <a name="part3"></a>Parte 3 - Adicionar a chave compartilhada e criar a conexão
1. Na página **Adicionar conexão**, adicione a chave compartilhada que você deseja usar para criar a conexão. Você pode obter a chave compartilhada do seu dispositivo VPN, ou fazer uma aqui e, em seguida, configurar seu dispositivo VPN para usar a mesma chave compartilhada. O importante é que as chaves sejam exatamente as mesmas.
   
    ![Chave compartilhada](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Chave compartilhada")<br>
2. Na parte inferior da página, clique em **OK** para criar a conexão.

## <a name="part4"></a>Parte 4 - Verificar a conexão VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Consulte o roteiro de aprendizagem das [máquinas virtuais](/learn/paths/deploy-a-website-with-azure-virtual-machines/) para obter mais informações.
