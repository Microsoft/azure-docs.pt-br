---
title: Conectar logs de diagnóstico do AKS (serviço kubernetes do Azure) ao Azure Sentinel
description: Saiba como usar Azure Policy para conectar os logs de diagnóstico do serviço kubernetes do Azure ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: c3a4593aa92acededf9784974b2a1e2dd3cfb319
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507170"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Conectar logs de diagnóstico do serviço kubernetes do Azure

O AKS (serviço kubernetes do Azure) é um serviço de orquestração de contêiner de software livre e totalmente gerenciado que permite implantar, dimensionar e gerenciar contêineres e aplicativos baseados em contêineres do Docker em um ambiente de cluster.

Esse conector permite transmitir os logs de diagnóstico do AKS (serviço kubernetes do Azure) para o Azure Sentinel, permitindo que você monitore continuamente a atividade em todas as suas instâncias. 

Saiba mais sobre como [monitorar o serviço kubernetes do Azure](../azure-monitor/containers/container-insights-overview.md) e sobre a [telemetria de diagnóstico do AKS](../aks/view-control-plane-logs.md).

## <a name="prerequisites"></a>Pré-requisitos

Para ingerir os logs do AKS no Azure Sentinel:

- Você deve ter permissões de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Para usar Azure Policy para aplicar uma política de streaming de log aos recursos AKS, você deve ser atribuído à função proprietário para o escopo de atribuição de política.

## <a name="connect-to-azure-kubernetes-service"></a>Conectar-se ao serviço kubernetes do Azure

Esse conector usa Azure Policy para aplicar uma única configuração de streaming de log do serviço kubernetes do Azure a uma coleção de instâncias, definida como um escopo. Você pode ver os tipos de log ingeridos no serviço kubernetes do Azure no lado esquerdo da página conector, em **tipos de dados**.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Selecione **serviço de kubernetes do Azure (AKs)** na Galeria de conectores de dados e, em seguida, selecione a **página abrir conector** no painel de visualização.

1. Na seção **configuração** da página conector, expanda **Habilitar logs de diagnóstico no serviço kubernetes do Azure (AKs)**.

1. Selecione o botão **iniciar Azure Policy atribuição do assistente** .

    O assistente de atribuição de política é aberto, pronto para criar uma nova política chamada **implantar-definir configurações de diagnóstico para o serviço kubernetes do Azure para log Analytics espaço de trabalho**.

    1. Na guia **noções básicas** , clique no botão com os três pontos em **escopo** para selecionar sua assinatura (e, opcionalmente, um grupo de recursos). Também é possível adicionar uma descrição.

    1. Na guia **parâmetros** , escolha seu espaço de trabalho do Azure Sentinel na lista suspensa **log Analytics espaço de trabalho** . Os campos restantes da lista suspensa representam os tipos de log de diagnóstico disponíveis. Deixe marcado como "verdadeiro" todos os tipos de log que você deseja ingerir.

    1. Para aplicar a política em seus recursos existentes, selecione a guia **correção** e marque a caixa de seleção **criar uma tarefa de correção** .

    1. Na guia **Revisar + criar**, clique em **Criar**. Agora, sua política está atribuída ao escopo escolhido.

> [!NOTE]
>
> Com esse conector de dados específico, os indicadores de status de conectividade (uma faixa de cores na Galeria de conectores de dados e os ícones de conexão ao lado dos nomes dos tipos de dados) serão mostrados como *conectados* (verde) somente se os dados tiverem sido ingeridos em algum momento nas duas últimas semanas. Depois que duas semanas tiverem passado sem ingestão de dados, o conector será exibido como sendo desconectado. O momento em que mais dados surgem, o status *conectado* retornará.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o serviço kubernetes do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
