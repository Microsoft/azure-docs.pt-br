---
title: Como desligar a uma unidade de servidor de dados do Microsoft Azure FXT Edge
description: Procedimentos de inicialização e desligamento seguro de um nó de servidor de dados do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 6921e7a52e43a63055b59242c02cc6ca3b8c5313
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620093"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Como desligar com segurança o hardware de servidor de dados do Azure FXT Edge

Embora você possa usar o botão de energia físico para alternar em um nó individual, você não deve usá-lo para desligar a unidade sob circunstâncias normais.

Depois que um nó de servidor de dados do Azure FXT Edge está em uso como parte de um cluster, você deve usar o software de painel de controle de cluster para desligar o hardware. 

> [!NOTE] 
> Para evitar a possível perda de dados ou corrupção, sempre use o software do painel de controle para desligar um servidor de dados do Azure FXT Edge. Não use o botão de energia físico do desligamento, a menos que você é instruído a fazê-lo pelo suporte e atendimento ao cliente Microsoft.
> 
> Em caso de emergência elétrico, desconecte os cabos de energia ou use os dados de mecanismo de desconexão de eletricidade do centro.

## <a name="shut-down-a-node-from-the-control-panel"></a>Desativar um nó no painel de controle

Siga estas instruções para desligar com segurança um nó de servidor de dados do Azure FXT Edge:

1. Entrar no painel de controle do cluster. (Instruções em [abrir as páginas de configurações](fxt-cluster-create.md#open-the-settings-pages))
1. Clique o **as configurações** guia, em seguida, carregue o **Cluster** > **FXT nós** página.
1. Na lista de nós de cluster, encontre aquela que você deseja desligar. Clique o **desligue** botão no seu **ações** coluna. 
1. Aguarde alguns instantes. O nó será desligado e se desligar.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os LEDs de status e outros indicadores no [status de hardware do servidor de dados do Monitor do Azure FXT borda](fxt-monitor.md).
* Leia mais sobre a energia do servidor de dados do Azure FXT Edge fornece na [conectar os cabos de alimentação](fxt-network-power.md#connect-power-cables).
