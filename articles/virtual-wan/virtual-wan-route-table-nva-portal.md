---
title: 'WAN Virtual: Crie uma tabela de rota de hub virtual para o NVA: Portal do Azure'
description: Tabela de rotas do hub virtual da WAN Virtual para conduzir o tráfego para uma solução de virtualização de rede usando o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 409858b2a9005592d895f6044d028221769144cc
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058503"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Crie uma tabela de rota de hub de WAN Virtual para NVAs: Portal do Azure

Este artigo mostra como direcionar o tráfego de um branch (site local) conectado ao hub da WAN Virtual para uma rede virtual Spoke (VNet) por meio de uma NVA (Solução de Virtualização de Rede).

![Diagrama de WAN virtual](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios:

*  Você tem uma NVA (Solução de Virtualização da Rede). Uma Solução de Virtualização de Rede que é um software de terceiros de sua escolha, que normalmente é provisionado no Azure Marketplace em uma rede virtual.

    * Um endereço IP privado deve ser atribuído ao adaptador de rede da NVA.

    * A NVA não é implantada no hub virtual. Ela deve ser implantada em uma rede virtual separada.

    *  A rede virtual NVA pode ter uma ou várias redes virtuais conectadas a ela. Neste artigo, nos referimos à rede virtual NVA como uma "VNet spoke indireta". Essas redes virtuais podem ser conectadas à VNet NVA usando o emparelhamento VNet. Os links de emparelhamento VNet são representados por setas pretas na figura acima, entre VNet 1, VNet 2 e NVA VNet.
*  Você criou duas redes virtuais. Elas serão usadas como VNets spoke.

    * Os espaços de endereço da VNets spoke são: VNet1: 10.0.2.0/24 e VNet2: 10.0.3.0/24. Se você precisar obter informações sobre como criar uma rede virtual, confira [Criar uma rede virtual](../virtual-network/quick-create-portal.md).

    * Verifique se não há gateways de rede virtual nas VNets.

    * As VNets não exigem uma sub-rede de gateway.

## <a name="1-sign-in"></a><a name="signin"></a>1. Entrar

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Criar uma WAN virtual

Crie uma WAN virtual. Use os seguintes valores de exemplo:

* **Nome da WAN virtual:** myVirtualWAN
* **Grupo de recursos:** testRG
* **Localização:** Oeste dos EUA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Criar um hub

Criar o hub. Use os seguintes valores de exemplo:

* **Localização:** Oeste dos EUA
* **Nome:** westushub
* **Espaço de endereço privado do hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Criar e aplicar uma tabela de rotas de hub

Atualize o hub com uma tabela de rotas de hub. Use os seguintes valores de exemplo:

* **Espaços de endereço VNet spoke:** (VNet1 e VNet2) 10.0.2.0/24 e 10.0.3.0/24
* **Endereço IP privado da interface de rede de perímetro NVA:** 10.0.4.5

1. Navegue até a sua WAN virtual.
2. Clique no hub para o qual você deseja criar uma tabela de rotas.
3. Clique em **...** e, em seguida, clique em **Editar hub virtual**.
4. Na página **Editar hub virtual**, role para baixo e marque a caixa de seleção **Usar tabela para roteamento**.
5. Na coluna **Se o prefixo de destino for**, adicione os espaços de endereço. Na coluna **Enviar para o próximo salto**, adicione o endereço IP privado da interface de rede de perímetro NVA.

   > [!NOTE]
   > A rede de perímetro NVA é aplicável ao hub local.
   
6. Clique em **Confirmar** para atualizar o recurso de hub com as configurações da tabela de rotas.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Criar as conexões de VNet

Crie uma conexão de rede virtual de cada VNet spoke indireta (VNet1 e VNet2) para o hub. Essas conexões de rede virtual são representadas pelas setas azuis na figura acima. Em seguida, crie uma conexão de VNet da VNet NVA para o hub (seta preta na figura).

 Para esta etapa, você pode usar qualquer um dos seguintes valores:

| Nome da rede virtual| Nome da conexão|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Repita o procedimento a seguir para cada rede virtual que você deseja conectar.

1. Na página da WAN virtual, clique em **Conexões de rede virtual**.
2. Na página de conexão de rede virtual, clique em **+Adicionar conexão**.
3. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a conexão.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
