---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d9825ea41937dc9436fe8b465b48b378e13407c1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171794"
---
1. No portal, de **Todos os recursos**, clique em **+ Adicionar**.
2. Na caixa de pesquisa da página **Tudo**, digite **Gateway de rede local** e clique para retornar uma lista de recursos. Clique em **Gateway de rede local** para abrir a página, clique em **Criar** para abrir a página **Criar gateway de rede local**.

   ![criar gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Na **página Criar gateway de rede local**, especifique os valores para seu objeto de gateway de rede local.

   - **Nome:** Especifique um nome para seu objeto de gateway de rede local. Se possível, use algo intuitivo, como **ClassicVNetLocal** ou **TestVNet1Local**. Isso facilita identificar o gateway de rede local no portal.
   - **Endereço IP:** Especifique um **endereço IP** público válido para o dispositivo VPN ou um gateway de rede virtual ao qual você deseja conectar-se.

     * **Se essa rede local representa uma localização local:** Especifique o endereço IP público do dispositivo VPN que você deseja conectar. Ele não pode estar por trás do NAT e deve poder ser acessado pelo Azure.
     * **Se essa rede local representa outra VNet:** Especifique o endereço IP público atribuído ao gateway de rede virtual para essa VNet.
     * **Se você ainda não tiver o endereço IP:** Poderá criar um endereço IP de espaço reservado válido, em seguida, voltar e modificar essa configuração antes de se conectar.
   - **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos especificados aqui não se sobrepõem aos intervalos de outras redes com as quais você deseja conectar-se.
   - **Configurar as definições de BGP ASN**: Use somente ao configurar o BGP. Caso contrário, não selecione essa opção.
   - **Assinatura:** Verifique se a assinatura correta está sendo exibida.
   - **Grupo de recursos:** Selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
   - **Localização:** Selecione o local em que esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.

4. Clique em **Criar** para criar o gateway de rede local.
