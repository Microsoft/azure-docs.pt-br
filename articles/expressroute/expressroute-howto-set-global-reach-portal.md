---
title: 'Azure ExpressRoute: configurar Alcance Global usando o portal do Azure'
description: Este artigo ajuda você a vincular circuitos do ExpressRoute em conjunto para fazer uma rede privada entre suas redes locais e habilitar Alcance Global usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 8366978d50875389ce872c2d1402f0defa2a7371
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539342"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Configurar Alcance Global ExpressRoute usando o portal do Azure

Este artigo ajuda você a configurar o Alcance Global do ExpressRoute usando o PowerShell. Para obter mais informações, confira [Alcance Global do ExpressRouteRoute](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme os seguintes critérios:

* Você entende os [fluxos de trabalho](expressroute-workflows.md)de provisionamento de circuito do ExpressRoute.
* Os circuitos do ExpressRoute estão em um estado provisionado.
* O emparelhamento privado do Azure é configurado em seus circuitos do ExpressRoute.
* Se você quiser executar o PowerShell localmente, verifique se a versão mais recente do Azure PowerShell está instalada em seu computador.

## <a name="identify-circuits"></a>Identificar circuitos

1. Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

2. Identifique os circuitos do ExpressRoute que você deseja usar. Você pode habilitar o ExpressRoute Alcance Global entre o emparelhamento privado de quaisquer dois circuitos de ExpressRoute, desde que eles estejam localizados nos países/regiões com suporte. Os circuitos precisam ser criados em diferentes locais de emparelhamento. 

   * Se sua assinatura tiver os dois circuitos, será possível escolher qualquer circuito para executar a configuração nas seções a seguir.
   * Se os dois circuitos estiverem em diferentes assinaturas do Azure, você precisará de autorização de uma assinatura do Azure. Em seguida, você passa a chave de autorização quando executa o comando de configuração em outra assinatura do Azure.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Lista de circuitos de ExpressRoute":::

## <a name="enable-connectivity"></a>Habilitar a conectividade

Habilite a conectividade entre suas redes locais. Há conjuntos separados de instruções para circuitos que estão na mesma assinatura do Azure e circuitos que são assinaturas diferentes.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuitos do ExpressRoute na mesma assinatura do Azure

1. Selecione a configuração de emparelhamento **privado do Azure** . 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Visão geral de emparelhamento do ExpressRoute":::

1. Marque a caixa de seleção **habilitar alcance global** e, em seguida, selecione **Adicionar alcance global** para abrir a página de configuração *Adicionar alcance global* .

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Habilitar alcance global do emparelhamento privado":::

1. Na página *Adicionar* configuração de alcance global, dê um nome para essa configuração. Selecione o *circuito de ExpressRoute* ao qual você deseja conectar esse circuito e insira em um **/29 IPv4** para a *sub-rede alcance global*. Usamos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Não use os endereços nessa sub-rede em suas redes virtuais do Azure ou em sua rede local. Selecione **Adicionar** para adicionar o circuito à configuração de emparelhamento privado.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Página de configuração do Alcance Global":::

1. Selecione **salvar** para concluir a configuração de alcance global. Quando a operação for concluída, você terá conectividade entre as duas redes locais por meio de ambos os circuitos do ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Salvando a configuração de emparelhamento privado":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos do ExpressRoute em diferentes assinaturas do Azure

Se os dois circuitos não estiverem na mesma assinatura do Azure, você precisará de autorização. Na configuração a seguir, a autorização é gerada a partir da assinatura do circuito 2. A chave de autorização é passada para o circuito 1.

1. Gere uma chave de autorização.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Gerar chave de autorização"::: 

   Anote a ID do recurso de circuito do circuito 2 e a chave de autorização.

1. Selecione a configuração de emparelhamento **privado do Azure** . 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Visão geral do emparelhamento 1 do circuito":::

1. Marque a caixa de seleção **habilitar alcance global** e, em seguida, selecione **Adicionar alcance global** para abrir a página de configuração *Adicionar alcance global* .

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Habilitar alcance global do circuito 1":::

1. Na página *Adicionar* configuração de alcance global, dê um nome para essa configuração. Marque a caixa **resgatar autorização** . Insira a **chave de autorização** e a **ID de circuito do ExpressRoute** geradas e obtidas na etapa 1. Em seguida, forneça um **/29 IPv4** para a *sub-rede alcance global*. Usamos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Não use os endereços nessa sub-rede em suas redes virtuais do Azure ou em sua rede local. Selecione **Adicionar** para adicionar o circuito à configuração de emparelhamento privado.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Adicionar Alcance Global com a chave de autorização":::

1. Selecione **salvar** para concluir a configuração de alcance global. Quando a operação for concluída, você terá conectividade entre as duas redes locais por meio de ambos os circuitos do ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Salvando a configuração de emparelhamento privado no circuito 1":::

## <a name="verify-the-configuration"></a>Verificar a configuração

Verifique a configuração do Alcance Global selecionando *emparelhamento privado* na configuração do circuito do ExpressRoute. Quando configurado corretamente, a sua configuração deve ter a aparência seguida:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Verificar configuração de Alcance Global":::

## <a name="disable-connectivity"></a>Desabilitar a conectividade

Você tem duas opções quando se trata de desabilitar Alcance Global. Para desabilitar a conectividade entre todos os circuitos, desmarque **habilitar alcance global** para desabilitar a conectividade entre todos os circuitos. Para desabilitar a conectividade entre um circuito individual, selecione o botão excluir ao lado do *nome do alcance global* para remover a conectividade entre eles. Em seguida, selecione **salvar** para concluir a operação.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Desabilitar configuração de Alcance Global":::

Depois que a operação for concluída, você não terá mais conectividade entre sua rede local por meio de circuitos do ExpressRoute.

## <a name="next-steps"></a>Próximas etapas
1. [Saiba mais sobre o Alcance Global do ExpressRoute](expressroute-global-reach.md)
2. [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Vincular um circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)
