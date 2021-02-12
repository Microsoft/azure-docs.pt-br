---
title: Exibir métricas da grade de eventos do Azure e definir alertas
description: Este artigo descreve como usar o portal do Azure para exibir métricas para tópicos e assinaturas da grade de eventos do Azure e criar alertas nelas.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 518d34d39e6fbecc408fe9a44d899fe4745d60d0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008949"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorar a entrega de mensagens da Grade de Eventos 
Este artigo descreve como usar o portal para ver as métricas para tópicos e assinaturas da grade de eventos e criar alertas nelas. 

## <a name="metrics"></a>Métricas

O portal exibe a métrica do status de entrega das mensagens de evento.

Para tópicos, aqui estão algumas das métricas:

* **Publicação bem-sucedida**: evento enviado com êxito para o tópico e processado com uma resposta 2xx.
* **Falha na Publicação**: evento enviado para o tópico, mas rejeitado com um código de erro.
* **Sem correspondência**: evento publicado com êxito para o tópico, mas não corresponde a uma assinatura de evento. O evento foi cancelado.

Para assinaturas, aqui estão algumas das métricas:

* **Entrega bem-sucedida**: evento entregue com êxito ao ponto de extremidade da assinatura e recebeu uma resposta de 2xx.
* **Falha na entrega**: toda vez que o serviço tenta entregar e o manipulador de eventos não retorna um código de 2xx de sucesso, o contador de **falha de entrega** é incrementado. Se tentarmos entregar o mesmo evento várias vezes e falhar, o contador de **falha de entrega** será incrementado para cada falha.
* **Eventos Expirados**: evento não foi entregue e todas as tentativas de repetição foram enviadas. O evento foi cancelado.
* **Eventos correspondidos**: evento no tópico foi correspondido pela assinatura do evento.

    > [!NOTE]
    > Para obter a lista completa de métricas, consulte [métricas com suporte na grade de eventos do Azure](metrics.md).

## <a name="view-custom-topic-metrics"></a>Exibir métricas de tópico personalizado

Se tiver publicado um tópico personalizado, você poderá exibir as métricas dele. 

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Na barra de pesquisa no tópico, digite **Tópicos de grade de eventos** e, em seguida, selecione tópicos da grade de **eventos** na lista suspensa. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Pesquise e selecione Tópicos da Grade de Eventos":::
3. Selecione o tópico personalizado na lista de tópicos. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Selecione seu tópico personalizado":::
4. Exiba as métricas para o tópico de evento personalizado na página de **Tópicos da grade de eventos** . Na imagem a seguir, a seção **Essentials** que mostra o grupo de recursos, a assinatura etc. é minimizada. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Exibir métricas de evento":::

Você pode criar gráficos com métricas com suporte usando a guia **métricas** da página de **Tópicos da grade de eventos** .

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Tópico – página de métricas":::

Para saber mais sobre métricas, confira [métricas em Azure monitor](../azure-monitor/platform/data-platform-metrics.md)

Por exemplo, consulte o gráfico de métricas para a métrica de **eventos publicados** .

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Métrica de eventos publicados":::


## <a name="view-subscription-metrics"></a>Exibir métricas de assinatura
1. Navegue até a página de **tópico da grade de eventos** seguindo as etapas da seção anterior. 
2. Selecione a assinatura no painel inferior, conforme mostrado no exemplo a seguir. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Selecione a assinatura do evento":::    

    Você também pode procurar **assinaturas de grade de eventos** na barra de pesquisa na portal do Azure, selecionar tipo de **tópico**, **assinatura** e **local** para ver uma assinatura de evento. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Selecionar assinatura de evento na página de assinaturas da grade de eventos":::        

    Para tópicos personalizados, selecione **Tópicos da grade de eventos** como **tipo de tópico**. Para tópicos do sistema, selecione o tipo de recurso do Azure, por exemplo, **contas de armazenamento (BLOB, GPv2)**. 
3. Consulte as métricas para a assinatura no home page para a assinatura em um gráfico. Você pode ver as métricas **geral**, **erro**, **latência** e **mensagens mortas** para as últimas 1 hora, 6 horas, 12 horas, 1 dia, 7 dias ou 30 dias. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Métricas na assinatura home page":::    

## <a name="view-system-topic-metrics"></a>Exibir métricas de tópico do sistema

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Na barra de pesquisa no tópico, digite **Tópicos do sistema de grade de eventos** e, em seguida, selecione tópicos do sistema de grade de **eventos** na lista suspensa. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Pesquisar e selecionar tópicos do sistema de grade de eventos":::
3. Selecione o tópico do sistema na lista de tópicos. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Selecione o tópico do sistema":::
4. Exiba as métricas do tópico sistema na página de **Tópicos sistema da grade de eventos** . Na imagem a seguir, a seção **Essentials** que mostra o grupo de recursos, a assinatura etc. é minimizada. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Exibir métricas de tópico do sistema na página Visão geral":::

Você pode criar gráficos com métricas com suporte usando a guia **métricas** da página de **Tópicos da grade de eventos** .

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Tópico do sistema – página métricas":::

Para saber mais sobre métricas, confira [métricas em Azure monitor](../azure-monitor/platform/data-platform-metrics.md)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- Para saber como criar alertas sobre as operações de log de atividades e métricas, consulte [set Alerts](set-alerts.md).
- Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
