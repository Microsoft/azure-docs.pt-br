---
title: 'Azure ExpressRoute: redefinir emparelhamentos de circuito usando o portal do Azure'
description: Saiba como desabilitar e habilitar emparelhamentos de um circuito do Azure ExpressRoute usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680274"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Redefinir emparelhamentos de circuito do ExpressRoute usando o portal do Azure

Este artigo descreve como desabilitar e habilitar emparelhamentos de um circuito do Azure ExpressRoute usando o portal do Azure. Quando você desabilita um emparelhamento, a sessão de Border Gateway Protocol (BGP) para a conexão primária e secundária do circuito do ExpressRoute é desligada. Quando você habilita um emparelhamento, a sessão BGP nas conexões primária e secundária do circuito do ExpressRoute é restaurada.

> [!Note]
> Na primeira vez que você configurar os emparelhamentos no circuito do ExpressRoute, os emparelhamentos serão habilitados por padrão.

A redefinição de seus emparelhamentos do ExpressRoute pode ser útil nos seguintes cenários:

* Você está testando o design e a implementação da recuperação de desastre. Por exemplo, suponha que você tenha dois circuitos de ExpressRoute. Você pode desabilitar os emparelhamentos de um circuito e forçar o tráfego de rede a usar o outro circuito.

* Você deseja habilitar a detecção de encaminhamento bidirecional (BFD) no emparelhamento privado do Azure ou no emparelhamento da Microsoft. Se o circuito do ExpressRoute foi criado antes de 1º de agosto de 2018, no emparelhamento privado do Azure ou antes de 10 de janeiro de 2020, no emparelhamento da Microsoft, o BFD não foi habilitado por padrão. Redefina o emparelhamento para habilitar BFD.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Em um navegador, vá para a [portal do Azure](https://portal.azure.com)e, em seguida, entre com sua conta do Azure.

## <a name="reset-a-peering"></a>Redefinir um emparelhamento

Você pode redefinir o emparelhamento da Microsoft e o emparelhamento privado do Azure em um circuito do ExpressRoute de forma independente.

1. Escolha o circuito que você deseja alterar.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Captura de tela que mostra a escolha de um circuito na lista de circuitos do ExpressRoute.":::

1. Escolha a configuração de emparelhamento que você deseja redefinir.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Captura de tela que mostra a escolha de um emparelhamento na visão geral do circuito do ExpressRoute.":::

1. Desmarque a caixa de seleção **habilitar emparelhamento** e, em seguida, selecione **salvar** para desabilitar a configuração de emparelhamento.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Captura de tela que mostra a desmarcação da caixa de seleção Habilitar emparelhamento.":::

1. Marque a caixa de seleção **habilitar emparelhamento** e, em seguida, selecione **salvar** para reabilitar a configuração de emparelhamento.

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas do ExpressRoute, consulte os seguintes artigos:

* [Verificando a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Solucionando problemas de desempenho de rede](expressroute-troubleshooting-network-performance.md)
