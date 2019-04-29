---
title: Solucionar problemas de conexões com o Observador de Rede do Azure – Portal do Azure | Microsoft Docs
description: Saiba como usar o recurso de resolução de problemas de conexão do Observador de Rede do Azure usando o Portal do Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: kumud
ms.openlocfilehash: 65afc3374a306cf2bb1b80baa2aa0949240a674a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681554"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Solucionar problemas de conexões com o Observador de Rede do Azure usando o Portal do Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI do Azure](network-watcher-connectivity-cli.md)
> - [API REST do Azure](network-watcher-connectivity-rest.md)

Saiba como usar a resolução de problemas de conexão para verificar se uma conexão TCP direta de uma máquina virtual para um determinado ponto de extremidade pode ser estabelecida.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha os seguintes recursos:

* Uma instância do Observador de Rede na região onde você deseja solucionar problemas de uma conexão.
* Máquinas virtuais para solucionar problemas de conexões.

> [!IMPORTANT]
> A solução de problemas de conexão exige que a VM para solução de problemas tenha a extensão da VM `AzureNetworkWatcherExtension` instalada. Para instalar a extensão em uma VM do Windows, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e para a VM do Linux, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A extensão não é necessária no ponto de extremidade de destino.

## <a name="check-connectivity-to-a-virtual-machine"></a>Verificar a conectividade com uma máquina virtual

Este exemplo verifica a conectividade a uma máquina virtual de destino pela porta 80.

Navegue até o Observador de Rede e clique em **Solucionar problemas de conexão**. Selecione a máquina virtual para verificar a conectividade. No **destino** seção escolher **selecione uma máquina virtual** e escolha a máquina virtual correta e a porta para testar.

Depois de clicar em **Verificar**, a conectividade entre as máquinas virtuais na porta especificada está marcada. No exemplo, o VM de destino está inacessível, uma lista de saltos serão mostrados.

![Verificar os resultados de conectividade para uma máquina virtual][1]

## <a name="check-remote-endpoint-connectivity"></a>Verificar a conectividade de ponto de extremidade remoto

Para verificar a conectividade e a latência para um ponto de extremidade, escolha o **especificar manualmente** botão de opção no **destino** seção, a url e a porta de entrada e clique em **verificar**.  Isso é usado para pontos de extremidade remotos como pontos de extremidade de sites e de armazenamento.

![Resultados da verificação de conectividade para um site Web][2]

## <a name="next-steps"></a>Próximas etapas

Saiba como automatizar a captura de pacote com alertas de máquina Virtual por meio da exibição [criar uma captura de pacote acionado alerta](network-watcher-alert-triggered-packet-capture.md)

Localize se determinado tráfego é permitido dentro ou fora de sua VM visitando [Verificar o fluxo do IP](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png