---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/19/2018
ms.date: 01/14/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 03a4005da7794a989166f914e4ba4d03f93ae8b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407503"
---
1. No portal, clique em **+Criar um recurso**.
2. Na caixa de pesquisa da página, insira **Gateway de rede local**, depois pressione **Enter** para pesquisar. Isso retornará uma lista de resultados. Clique em **Gateway de rede local**, depois clique no botão **Criar** para abrir a página **Criar gateway de rede local**.

   ![Criar o gateway de rede local](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Criar o gateway de rede local")

3. Na **página Criar gateway de rede local**, especifique os valores para seu objeto de gateway de rede local.

   - **Nome:** Especifique um nome para seu objeto de gateway de rede local.
   - **Endereço IP:** Este é o endereço IP público do dispositivo VPN ao qual você deseja que o Azure se conecte. Especifique um endereço IP público válido. Se você não tiver o endereço IP no momento, poderá usar os valores mostrados no exemplo, mas precisará voltar e substituir o seu endereço IP de espaço reservado com o endereço IP público de seu dispositivo VPN. Caso contrário, o Azure não será capaz de se conectar.
   - **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos que você especifica aqui não se sobrepõem aos intervalos de outras redes com que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. *Se deseja se conectar ao site local, use seus próprios valores aqui, e não os valores mostrados no exemplo*.
   - **Configurar as definições de BGP ASN**: Use somente ao configurar o BGP. Caso contrário, não selecione essa opção.
   - **Assinatura:** Verifique se a assinatura correta está sendo exibida.
   - **Grupo de recursos:** Selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
   - **Localização:** Selecione o local em que esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.

4. Ao terminar de especificar os valores, clique no botão **Criar**, na parte inferior da página, para criar o gateway de rede local.
