---
title: Logs de diagnóstico para Conexões Híbridas
description: Este artigo fornece uma visão geral de todos os logs de atividade e de diagnóstico que estão disponíveis para a Retransmissão do Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 7ca6dc95567a4867d3c58e0efad0fc74289e586c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079090"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Habilitar logs de diagnóstico para conexões híbridas de Retransmissão do Azure
Ao começar a usar as conexões híbridas de Retransmissão do Azure, convém monitorar como e quando seus ouvintes e remetentes são abertos e fechados e como suas conexões híbridas são criadas e as mensagens são enviadas. Este artigo fornece uma visão geral dos logs de atividade e de diagnóstico fornecidos pelo serviço de Retransmissão do Azure. 

É possível exibir dois tipos de logs para a Retransmissão do Azure:

- [Logs de atividades](../azure-monitor/essentials/platform-logs-overview.md): Esses logs têm informações sobre as operações executadas em relação ao namespace no portal do Azure ou por meio do modelo de Azure Resource Manager. Esses logs estão sempre habilitados. Por exemplo:  "Criar ou atualizar o namespace", "Criar ou atualizar a conexão híbrida". 
- [Logs de diagnóstico](../azure-monitor/essentials/platform-logs-overview.md): Você pode configurar os logs de diagnóstico para uma exibição mais rica de tudo o que acontece com operações e ações que são realizadas em seu namespace usando a API ou o SDK de linguagem.

## <a name="view-activity-logs"></a>Exibir logs de atividade
Para exibir os logs de atividade para o namespace de Retransmissão do Azure, alterne para a página do **log de atividades** no portal do Azure.

![Retransmissão do Azure – log de atividades](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Habilitar logs de diagnóstico

> [!NOTE]
> Os logs de diagnóstico estão disponíveis apenas para conexões híbridas, não para a retransmissão do WCF (Windows Communication Foundation).

Para habilitar logs de diagnóstico, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), vá para o namespace de Retransmissão do Azure e, em **Monitoramento**, selecione **Configurações de diagnóstico**.
1. Na página **Configurações de diagnóstico**, selecione **Adicionar configuração de diagnóstico**.  

   ![O link "Adicionar configuração de diagnóstico"](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Defina as configurações de diagnóstico realizando as seguintes etapas:
    1. Na caixa **Nome**, insira um nome para as configurações de diagnóstico.  
    2. Selecione **HybridConnectionsEvent** para o tipo de log. 
    3. Selecione um dos seguintes três **destinos** para seus logs de diagnóstico:  
        1. Se você selecionar **Arquivar em uma conta de armazenamento**, configure a conta de armazenamento em que os logs de diagnóstico serão armazenados.  
        2. Se você selecionar **Transmitir para um hub de eventos**, configure o Hub de Eventos para o qual você deseja transmitir os logs de diagnóstico.
        3. Se você selecionar **Enviar para Log Analytics**, especifique para qual instância do Log Analytics o diagnóstico será enviado.  

        ![Configurações de diagnóstico de exemplo](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Selecione **Salvar** na barra de ferramentas para salvar as configurações.

As novas configurações terão efeito em aproximadamente dez minutos. Os logs aparecerão no destino de arquivamento configurado, no painel **Logs de diagnóstico**. Para obter mais informações sobre como definir configurações de diagnóstico, confira a [visão geral dos logs de diagnóstico do Azure](../azure-monitor/essentials/platform-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Esquema para eventos de conexões híbridas
As cadeias de caracteres JSON do log de evento de conexões híbridas incluem os elementos listados na seguinte tabela:

| Nome | Descrição |
| ------- | ------- |
| ResourceId | ID de recurso do Azure Resource Manager |
| ActivityId | ID interna, usada para identificar a operação especificada. Também pode ser conhecida como "TrackingId" |
| Ponto de extremidade | O endereço do recurso de Retransmissão |
| OperationName | O tipo da operação de conexões híbridas que está sendo registrada |
| EventTimeString | O carimbo de data/hora, em UTC, do registro de log |
| Mensagem | A mensagem detalhada do evento |
| Categoria | Categoria do evento. Atualmente há apenas `HybridConnectionsEvents`. 


## <a name="sample-hybrid-connections-event"></a>Exemplo de evento de conexões híbridas
Veja um exemplo de evento de conexões híbridas no formato JSON. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>Eventos e operações capturados nos logs de diagnóstico

| Operação                           | Descrição                                                     |
|-------------------------------------|-----------------------------------------------------------------|
| AuthorizationFailed                 | Falha na autorização.                                           |
| InvalidSasToken                     | Token SAS inválido.                                              |
| ListenerAcceptingConnection         | O ouvinte está aceitando a conexão.                           |
| ListenerAcceptingConnectionTimeout  | O tempo limite para aceitação da conexão pelo ouvinte foi atingido.                |
| ListenerAcceptingHttpRequestFailed  | Falha na aceitação de solicitação HTTP pelo ouvinte devido a uma exceção. |
| ListenerAcceptingRequestTimeout     | O tempo limite para aceitação da solicitação pelo ouvinte foi atingido.                   |
| ListenerClosingFromExpiredToken     | O ouvinte está fechando porque o token de segurança expirou. |
| ListenerRejectedConnection          | O ouvinte rejeitou a conexão.                       |
| ListenerReturningHttpResponse       | O ouvinte está retornando uma resposta HTTP.                     |
| ListenerReturningHttpResponseFailed | O ouvinte está retornando uma resposta HTTP com um código de falha. |
| ListenerSentHttpResponse            | O serviço de Retransmissão recebeu uma resposta HTTP do ouvinte.  |
| ListenerUnregistered                | O registro do ouvinte foi cancelado.                                   |
| ListenerUnresponsive                | O ouvinte não está respondendo ao retornar uma resposta.         |
| MessageSendingToListener            | A mensagem está sendo enviada para o ouvinte.                              |
| MessageSentToListener               | A mensagem foi enviada ao ouvinte.                                    |
| NewListenerRegistered               | Novo ouvinte registrado.                                        |
| NewSenderRegistering                | O novo remetente está se registrando.                                      |
| ProcessingRequestFailed             | Falha no processamento de uma operação de conexão híbrida.     |
| SenderConnectionClosed              | A conexão do remetente está fechada.                                |
| SenderListenerConnectionEstablished | O remetente e o ouvinte estabeleceram a conexão com êxito.    |
| SenderSentHttpRequest               | O remetente enviou uma solicitação HTTP.                                |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Retransmissão do Azure, confira:

* [Introdução à Retransmissão do Azure](relay-what-is-it.md)
* [Introdução às Conexões Híbridas de Retransmissão](relay-hybrid-connections-dotnet-get-started.md)
