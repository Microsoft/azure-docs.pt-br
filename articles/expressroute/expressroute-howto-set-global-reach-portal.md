---
title: 'Azure ExpressRoute: configurar Alcance Global usando o portal do Azure'
description: Este artigo ajuda você a vincular circuitos do ExpressRoute em conjunto para fazer uma rede privada entre suas redes locais e habilitar Alcance Global usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/05/2021
ms.author: duau
ms.openlocfilehash: 336bd4aaf881b7315921ef374c92a2ac95ff3c8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431288"
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

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Captura de tela da lista de circuitos do ExpressRoute.":::

## <a name="enable-connectivity"></a>Habilitar a conectividade

Habilite a conectividade entre suas redes locais. Há conjuntos separados de instruções para circuitos que estão na mesma assinatura do Azure e circuitos que são assinaturas diferentes.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuitos do ExpressRoute na mesma assinatura do Azure

1. Selecione a configuração de emparelhamento **privado do Azure** . 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Captura de tela da página Visão geral do ExpressRoute.":::

1. Selecione **adicionar alcance global** para abrir a página *Adicionar alcance global* configuração.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Habilitar alcance global do emparelhamento privado":::

1. Na página *Adicionar* configuração de alcance global, dê um nome para essa configuração. Selecione o *circuito de ExpressRoute* ao qual você deseja conectar esse circuito e insira em um **/29 IPv4** para a *sub-rede alcance global*. Usamos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Não use os endereços nessa sub-rede em suas redes virtuais do Azure ou em sua rede local. Selecione **Adicionar** para adicionar o circuito à configuração de emparelhamento privado.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Captura de tela da adição de Alcance Global no emparelhamento privado.":::

1. Selecione **salvar** para concluir a configuração de alcance global. Quando a operação for concluída, você terá conectividade entre as duas redes locais por meio de ambos os circuitos do ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Captura de tela do salvamento de configurações de emparelhamento privado.":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos do ExpressRoute em diferentes assinaturas do Azure

Se os dois circuitos não estiverem na mesma assinatura do Azure, você precisará de autorização. Na configuração a seguir, a autorização é gerada a partir da assinatura do circuito 2. A chave de autorização é passada para o circuito 1.

1. Gere uma chave de autorização.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Captura de tela da geração da chave de autorização."::: 

   Anote a ID do recurso de circuito do circuito 2 e a chave de autorização.

1. Selecione a configuração de emparelhamento **privado do Azure** . 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Captura de tela de emparelhamento privado na página Visão geral.":::

1. Selecione **adicionar alcance global** para abrir a página *Adicionar alcance global* configuração.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Captura de tela de adicionar Alcance Global em emparelhamento privado.":::

1. Na página *Adicionar* configuração de alcance global, dê um nome para essa configuração. Marque a caixa **resgatar autorização** . Insira a **chave de autorização** e a **ID de circuito do ExpressRoute** geradas e obtidas na etapa 1. Em seguida, forneça um **/29 IPv4** para a *sub-rede alcance global*. Usamos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Não use os endereços nessa sub-rede em suas redes virtuais do Azure ou em sua rede local. Selecione **Adicionar** para adicionar o circuito à configuração de emparelhamento privado.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Captura de tela de adicionar Alcance Global com a chave de autorização.":::

1. Selecione **salvar** para concluir a configuração de alcance global. Quando a operação for concluída, você terá conectividade entre as duas redes locais por meio de ambos os circuitos do ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Captura de tela de salvar a configuração de emparelhamento privado com Alcance Global.":::

## <a name="verify-the-configuration"></a>Verificar a configuração

Verifique a configuração do Alcance Global selecionando *emparelhamento privado* na configuração do circuito do ExpressRoute. Quando configurado corretamente, a sua configuração deve ter a aparência seguida:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Captura de tela de Alcance Global configurada.":::

## <a name="disable-connectivity"></a>Desabilitar a conectividade

Para desabilitar a conectividade entre um circuito individual, selecione o botão excluir ao lado do *nome do alcance global* para remover a conectividade entre eles. Em seguida, selecione **salvar** para concluir a operação.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Captura de tela mostrando como desabilitar Alcance Global.":::

Depois que a operação for concluída, você não terá mais conectividade entre sua rede local por meio de circuitos do ExpressRoute.

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre o Alcance Global do ExpressRoute](expressroute-global-reach.md)
- [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
- [Vincular um circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)
