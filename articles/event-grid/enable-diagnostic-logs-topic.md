---
title: Grade de eventos do Azure – Habilitar logs de diagnóstico para tópicos ou domínios
description: Este artigo fornece instruções passo a passo sobre como habilitar os logs de diagnóstico para um tópico da grade de eventos do Azure.
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576444"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Habilitar os logs de diagnóstico para os tópicos ou domínios da grade de eventos do Azure
Este artigo fornece instruções passo a passo para habilitar as configurações de diagnóstico para os tópicos ou domínios da grade de eventos.  Essas configurações permitem capturar e exibir os logs **de falha de publicação e entrega** . 

## <a name="prerequisites"></a>Pré-requisitos

- Um tópico de grade de eventos provisionado
- Um destino provisionado para capturar logs de diagnóstico. Pode ser um dos seguintes destinos no mesmo local que o tópico da grade de eventos:
    - Conta de Armazenamento do Azure
    - Hub de Eventos
    - Espaço de trabalho do Log Analytics

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Habilitar os logs de diagnóstico para um tópico personalizado

> [!NOTE]
> O procedimento a seguir fornece instruções passo a passo para habilitar os logs de diagnóstico para um tópico. As etapas para habilitar os logs de diagnóstico para um domínio são muito semelhantes. Na etapa 2, navegue até o **domínio** da grade de eventos na portal do Azure.  

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até o tópico da grade de eventos para o qual você deseja habilitar as configurações do log de diagnóstico. 
    1. Na barra de pesquisa na parte superior, procure **Tópicos de grade de eventos**. 
    
        ![Pesquisar tópicos personalizados](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Selecione o **tópico** na lista para a qual você deseja definir as configurações de diagnóstico. 
1. Selecione **configurações de diagnóstico** em **monitoramento** no menu à esquerda.
1. Na página **configurações de diagnóstico** , selecione **Adicionar nova configuração de diagnóstico**. 
    
    ![Botão Adicionar configuração de diagnóstico](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Especifique um **nome** para a configuração de diagnóstico. 
6. Selecione as opções **DeliveryFailures** e **PublishFailures** na seção **log** . 
    ![Selecionar as falhas](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Habilite um ou mais dos destinos de captura para os logs e, em seguida, configure-os selecionando um recurso de captura criado anteriormente. 
    - Se você selecionar **arquivar em uma conta de armazenamento**, selecione **conta de armazenamento-configurar** e, em seguida, selecione a conta de armazenamento em sua assinatura do Azure. 

        ![Captura de tela que mostra a página "configurações de diagnóstico" com "arquivar em uma conta de armazenamento do Azure" marcada e uma conta de armazenamento selecionada.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se você selecionar **fluxo para um hub de eventos**, selecione **Hub de eventos-configurar** e, em seguida, selecione o namespace de hubs de eventos, o Hub de eventos e a política de acesso. 
        ![Captura de tela que mostra a página "configurações de diagnóstico" com "fluxo para um hub de eventos" marcado.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se você selecionar **Enviar para log Analytics**, selecione o espaço de trabalho log Analytics.
        ![Captura de tela que mostra a página "configurações de diagnóstico" com "enviar para Log Analytics" marcada.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Clique em **Salvar**. Em seguida, selecione **X** no canto direito para fechar a página. 
9. Agora, de volta à página **configurações de diagnóstico** , confirme se você vê uma nova entrada na tabela configurações de **diagnóstico** . 
    ![Captura de tela que mostra a página "configurações de diagnóstico" com uma nova entrada realçada na tabela "configurações de diagnóstico".](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Você também pode habilitar a coleta de todas as métricas para o tópico. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Habilitar logs de diagnóstico para um tópico do sistema

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até o tópico da grade de eventos para o qual você deseja habilitar as configurações do log de diagnóstico. 
    1. Na barra de pesquisa na parte superior, procure **Tópicos do sistema de grade de eventos**. 
    
        ![Pesquisar tópicos do sistema](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Selecione o **tópico do sistema** para o qual você deseja definir as configurações de diagnóstico. 
    
        ![Selecionar tópico do sistema](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Selecione **configurações de diagnóstico** em **monitoramento** no menu à esquerda e, em seguida, selecione **Adicionar configuração de diagnóstico**. 

    ![Adicionar configurações de diagnóstico-botão](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Especifique um **nome** para a configuração de diagnóstico. 
7. Selecione o **DeliveryFailures** na seção **log** . 
    ![Selecionar falhas de entrega](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Habilite um ou mais dos destinos de captura para os logs e, em seguida, configure-os selecionando um recurso de captura criado anteriormente. 
    - Se você selecionar **Enviar para log Analytics**, selecione o espaço de trabalho log Analytics.
        ![Enviar para o Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Se você selecionar **arquivar em uma conta de armazenamento**, selecione **conta de armazenamento-configurar** e, em seguida, selecione a conta de armazenamento em sua assinatura do Azure. 

        ![Arquivar em uma conta de armazenamento do Azure](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Se você selecionar **fluxo para um hub de eventos**, selecione **Hub de eventos-configurar** e, em seguida, selecione o namespace de hubs de eventos, o Hub de eventos e a política de acesso. 
        ![Transmitir para um hub de eventos](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Clique em **Salvar**. Em seguida, selecione **X** no canto direito para fechar a página. 
9. Agora, de volta à página **configurações de diagnóstico** , confirme se você vê uma nova entrada na tabela configurações de **diagnóstico** . 
    ![Configuração de diagnóstico na lista](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     Você também pode habilitar a coleta de todas as **métricas** para o tópico do sistema.

    ![Tópico do sistema – habilitar todas as métricas](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Exibir logs de diagnóstico no armazenamento do Azure 

1. Depois de habilitar uma conta de armazenamento como destino de captura, a grade de eventos começa a emitir logs de diagnóstico. Você deve ver novos contêineres chamados **insights-logs-deliveryfailures** e **insights-logs-publishfailures** na conta de armazenamento. 

    ![Contêineres de armazenamento para logs de diagnóstico](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Ao navegar por um dos contêineres, você terminará em um blob no formato JSON. O arquivo contém entradas de log para uma falha de entrega ou uma falha de publicação. O caminho de navegação representa o **ResourceId** do tópico da grade de eventos e o carimbo de data/hora (nível de minuto) para quando as entradas de log foram emitidas. O arquivo blob/JSON, que pode ser baixado, no final adere ao esquema descrito na próxima seção. 

    [![Arquivo JSON no armazenamento ](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. Você deve ver o conteúdo no arquivo JSON semelhante ao exemplo a seguir: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>Próximas etapas
Para o esquema de log e outras informações conceituais sobre os logs de diagnóstico para tópicos ou domínios, consulte [logs de diagnóstico](diagnostic-logs.md).
