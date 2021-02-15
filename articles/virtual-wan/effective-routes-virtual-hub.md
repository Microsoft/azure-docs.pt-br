---
title: 'Exibir rotas efetivas de um hub virtual: WAN virtual do Azure | Microsoft Docs'
description: Como exibir rotas efetivas para um hub virtual na WAN virtual do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983654"
---
# <a name="view-virtual-hub-effective-routes"></a>Exibir rotas efetivas do hub virtual

Você pode exibir todas as rotas do seu hub de WAN virtual no portal do Azure. Este artigo orienta você pelas etapas para exibir rotas efetivas. Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Selecionar conexões ou tabelas de rotas

1. Navegue até o Hub virtual e selecione **Roteamento**. Na página roteamento, selecione **rotas efetivas**.
1. No menu suspenso, você pode selecionar **tabela de rotas**. Se você não vir uma opção de tabela de rotas, isso significa que você não tem uma tabela de rotas personalizada ou padrão configurada nesse Hub virtual.

## <a name="view-output"></a><a name="output"></a>Exibir saída

A saída da página mostra os seguintes campos:

* **Prefixo**: prefixo de endereço conhecido para a entidade atual (Aprenda com o roteador do Hub virtual)
* **Tipo do próximo salto**: pode ser conexão de rede Virtual, VPN_S2S_Gateway, ExpressRouteGateway, Hub remoto ou firewall do Azure.
* **Próximo salto**: esse é o link para a ID de recurso do próximo salto ou simplesmente mostra no link para implicar o Hub atual.
* **Origem**: a ID de recurso da fonte de roteamento.
* **Como caminho**: o atributo BGP como caminho (sistema autônomo) lista todos os números de as que precisam ser percorridos para alcançar o local onde o prefixo ao qual o caminho está anexado, é anunciado.

### <a name="example"></a><a name="example"></a>Exemplo

Os valores na tabela de exemplo a seguir sugerem que a conexão do Hub virtual ou a tabela de rotas aprendeu a rota de 10.2.0.0/24 (um prefixo de ramificação). Ele aprendeu a rota devido ao tipo do **próximo salto da VPN** VPN_S2S_Gateway com a ID de recurso do gateway de VPN do **próximo salto** . A **origem da rota** aponta para a ID de recurso da conexão/tabela de rota/gateway de VPN de origem. As **path** indica o caminho as para a ramificação.

Use a barra de rolagem na parte inferior da tabela para exibir o "caminho AS".

| **Prefix** |  **Tipo do próximo salto** | **Próximo salto** |  **Origem da rota** |**Caminho AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /Providers/Microsoft.Network/vpnGateways/vpngw|/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /Providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Considerações:**

* Se você vir 0.0.0.0/0 na saída **obter rotas efetivas** , ele implica que a rota existe em uma das tabelas de rotas. No entanto, se essa rota foi configurada para Internet, um sinalizador adicional **"enableInternetSecurity": true** é necessário na conexão. A rota efetiva na NIC da VM não mostrará a rota se o sinalizador "enableInternetSecurity" na conexão for "false".

* O campo **propagar a rota padrão** é visto no portal de WAN virtual do Azure quando você edita uma conexão de rede virtual, uma conexão VPN ou uma conexão de ExpressRoute. Esse campo indica o sinalizador **enableInternetSecurity** , que é sempre por padrão "false" para conexões de EXPRESSROUTE e VPN, mas "true" para conexões de rede virtual.

* Ao exibir rotas efetivas em uma NIC de VM, se você vir o próximo salto como "gateway de rede virtual", isso implica o roteador do Hub virtual quando a VM está em um spoke conectado a um hub de WAN virtual.

* Exibir rotas efetivas para uma tabela de rotas de Hub virtual será preenchida somente se o Hub virtual tiver pelo menos um tipo de conexão (VPN/ER/VNET) conectado a ele.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
