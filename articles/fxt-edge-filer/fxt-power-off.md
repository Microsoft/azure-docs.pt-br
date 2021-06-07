---
title: Como desligar uma Microsoft Azure unidade de arquivo de borda do FXT
description: Conheça os procedimentos para inicialização e desligamento seguro de um nó de filer do Azure FXT Edge usando o software do painel de controle do cluster.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 01c34304ac0e3e7faa42611758d77893e149a2f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218724"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Como desligar com segurança o hardware do Azure FXT Edge Filer

Embora você possa usar o botão de energia física para alternar um nó individual, não deve usá-lo para desligar a unidade em circunstâncias normais.

Depois que um nó de filer do Azure FXT Edge estiver em uso como parte de um cluster, você deverá usar o software do painel de controle de cluster para desligar o hardware.

> [!NOTE]
> Para evitar a possível perda ou corrupção de dados, sempre use o software do painel de controle para desligar um Filer do Azure FXT Edge. Não use o botão de energia física para desligar, a menos que você seja instruído a fazer isso pelo serviço de suporte e atendimento ao cliente da Microsoft.
>
> Em uma emergência elétrica, desconecte os cabos de alimentação ou use o mecanismo de desconexão de eletricidade de data center.

## <a name="shut-down-a-node-from-the-control-panel"></a>Desligar um nó do painel de controle

Siga estas instruções para desligar com segurança um nó de filer do Azure FXT Edge:

1. Entre no painel de controle do cluster. (Instruções em [abrir as páginas de configurações](fxt-cluster-create.md#open-the-settings-pages))
1. Clique na guia **configurações** e carregue a página nós do **cluster**  >  **FXT** .
1. Na lista de nós de cluster, localize aquele que você deseja desligar. Clique no **botão desligar** em sua coluna **ações** .
1. Aguarde alguns instantes. O nó será desligado e desligará.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os LEDs de status e outros indicadores em [monitorar o status de hardware do arquivo de borda do Azure FXT Edge](fxt-monitor.md).
* Leia mais sobre as fontes de alimentação do Azure FXT Edge Filer em [conectar cabos de energia](fxt-network-power.md#connect-power-cables).
