---
title: Como configurar um roteamento de hub virtual
titleSuffix: Azure Virtual WAN
description: Este artigo descreve como configurar o roteamento de Hub virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91313681"
---
# <a name="how-to-configure-virtual-hub-routing"></a>Como configurar um roteamento de hub virtual

Um hub virtual pode conter vários gateways, como um gateway de VPN site a site, gateway de ExpressRoute, gateway de ponto a site e firewall do Azure. Os recursos de roteamento no Hub virtual são fornecidos por um roteador que gerencia todo o roteamento, incluindo o roteamento de trânsito, entre os gateways usando Border Gateway Protocol (BGP). Esse roteador também fornece conectividade de trânsito entre redes virtuais que se conectam a um hub virtual e podem dar suporte a uma taxa de transferência agregada de 50 Gbps. Esses recursos de roteamento se aplicam aos clientes de WAN virtual padrão.

Para obter mais informações, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="create-a-route-table"></a><a name="create-table"></a>Criar uma tabela de rotas

1. No portal do Azure, navegue até o Hub virtual.
2. Em **conectividade**, selecione **Roteamento**. Na página roteamento, você vê as tabelas **padrão** e **nenhuma** rota.

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="Página de roteamento":::
3. Selecione **+ criar tabela de rotas** para abrir a página **criar tabela de rotas** .
4. Na guia **básico** da página criar tabela de rotas, preencha os campos a seguir.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="Guia Básico":::

   * **Nome**
   * **Rotas**
   * **Nome da rota**
   * **Tipo de destino**
   * **Prefixo de destino**: você pode agregar prefixos. Por exemplo: VNet 1:10.1.0.0/24 e VNet 2:10.1.1.0/24 pode ser agregado como 10.1.0.0/16. As rotas de **ramificação** se aplicam a todos os sites VPN conectados, circuitos do ExpressRoute e conexões VPN do usuário.
   * **Próximo salto**: uma lista de conexões de rede virtual ou o Firewall do Azure.

     Se você selecionar uma conexão de rede virtual, verá **configurar rotas estáticas**. Essa é uma configuração opcional. Para obter mais informações, consulte [Configurando rotas estáticas](about-virtual-hub-routing.md#static).

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="Próximo salto":::

5. Selecione a guia **Rótulos** para configurar nomes de rótulo. Os rótulos fornecem um mecanismo para agrupar logicamente as tabelas de rotas.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="Configurar nomes de rótulo":::

6. Selecione a guia **associações** para associar as conexões à tabela de rotas.
Você verá **branches**, **redes virtuais** e as **configurações atuais** das conexões.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="Conexões de associação com a tabela de rotas":::

7. Selecione a guia **propagações** para propagar rotas de conexões à tabela de rotas.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="Propagar rotas":::

8. Selecione **criar** para criar a tabela de rotas.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>Para editar uma tabela de rotas

Na portal do Azure, localize a tabela de rotas do seu hub virtual. Selecione a tabela de rotas para editar qualquer informação.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>Para excluir uma tabela de rotas

Na portal do Azure, localize a tabela de rotas do seu hub virtual. Não é possível excluir uma tabela de rotas Default ou None. No entanto, você pode excluir todas as tabelas de rotas personalizadas. Clique em **"..."** e, em seguida, selecione **excluir**.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>Para exibir rotas efetivas

Na portal do Azure, localize a tabela de rotas do seu hub virtual. Clique em **"..."** e selecione **rotas efetivas** para exibir as rotas aprendidas pela tabela de rotas selecionada. As rotas propagadas da conexão para a tabela de rotas são preenchidas automaticamente em **rotas efetivas** da tabela de rotas. Para obter mais informações, consulte [sobre rotas efetivas](effective-routes-virtual-hub.md).

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="Exibir rotas efetivas" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>Para configurar a configuração de roteamento para uma conexão de rede virtual

1. No portal do Azure, navegue até sua WAN virtual e, em **conectividade**, selecione **conexões de rede virtual**.
1. Selecione **+ Adicionar conexão**.
1. Selecione a rede virtual na lista suspensa.
1. Defina a configuração de roteamento para associar a uma tabela de rotas. Para **associar tabela de rotas**, selecione a tabela de rotas na lista suspensa.
1. Defina a configuração de roteamento para propagar para uma ou várias tabelas de rotas. Para **propagar para a tabela de rotas**, selecione na lista suspensa.
1. Para **rotas estáticas**, configure rotas estáticas para solução de virtualização de rede (se aplicável). A WAN virtual dá suporte a um único IP do próximo salto para rota estática em uma conexão de rede virtual. Por exemplo, se você tiver um dispositivo virtual separado para fluxos de tráfego de entrada e saída, seria melhor ter os dispositivos virtuais em VNETs separados e anexar o VNETs ao Hub virtual.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="Configurar a configuração de roteamento" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
