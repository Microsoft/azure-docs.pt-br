---
title: Operações de solicitação/resposta do AMQP 1,0 no barramento de serviço do Azure
description: Este artigo define a lista de operações baseadas em solicitação/resposta AMQP no Barramento de Serviço do Microsoft Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b845f4086ee1ac4fe868571c1754caf6d29b9021
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88064407"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 no Barramento de Serviço do Microsoft Azure: operações baseadas em solicitação-resposta

Este artigo define a lista de operações baseadas em solicitação/resposta do Barramento de Serviço do Microsoft Azure. Estas informações baseiam-se no rascunho funcional do Gerenciamento de AMQP Versão 1.0.  
  
Para um guia de protocolo AMQP 1.0 detalhado no nível de transmissão, que explica como o Barramento de Serviço implementa e se baseia na especificação técnica AMQP OASIS, confira o [AMQP 1.0 no guia de protocolo do Barramento de Serviço do Azure e dos Hubs de Eventos][AMQP 1.0 no guia de protocolo].  
  
## <a name="concepts"></a>Conceitos  
  
### <a name="entity-description"></a>Descrição de entidade  

Uma descrição de entidade refere-se a um objeto da [classe QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [classe TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) ou [classe SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) do Barramento de Serviço.  
  
### <a name="brokered-message"></a>Mensagem agenciada  

Representa uma mensagem no Barramento de Serviço, que é mapeada para uma mensagem AMQP. O mapeamento é definido no [guia de protocolo do AMQP no Barramento de Serviço](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Anexar ao nó de gerenciamento de entidades  

Todas as operações descritas neste documento seguem um padrão de solicitação/resposta, estão dentro do escopo de uma entidade e exigem a anexação a um nó de gerenciamento de entidades.  
  
### <a name="create-link-for-sending-requests"></a>Criar link para enviar solicitações  

Cria um link para o envio de solicitações do nó de gerenciamento.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Criar link para receber respostas  

Cria um link para receber as respostas do nó de gerenciamento.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Transferir uma mensagem de solicitação  

Transfere uma mensagem de solicitação.  
Um estado de transação pode ser adicionado, opcionalmente, para operações que dão suporte a transações.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Receber uma mensagem de resposta  

Recebe a mensagem de resposta do link de resposta.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
A mensagem de resposta está no seguinte formato:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Endereço de entidade do Barramento de Serviço  

As entidades do Barramento de Serviço devem ser abordadas da seguinte maneira:  
  
|Tipo de entidade|Endereço|Exemplo|  
|-----------------|-------------|-------------|  
|fila|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|topic|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscription|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operações de mensagem  
  
### <a name="message-renew-lock"></a>Renovar Bloqueio da Mensagem  

Estende o bloqueio de uma mensagem até o momento especificado na descrição da entidade.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
 O corpo da mensagem de solicitação deve consistir em uma seção amqp-value que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matriz de uuid|Sim|Tokens de bloqueio de mensagem a serem renovados.|  

> [!NOTE]
> Lock tokens são a propriedade `DeliveryTag` nas mensagens recebidas. Consulte o exemplo a seguir no [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) que os recupera. O token também pode aparecer no 'DeliveryAnnotations' como 'x-opt-lock-token', no entanto, isso não é garantido e `DeliveryTag` deve ser o preferencial. 
> 
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção amqp-value que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expirations|matriz de carimbo de data/hora|Sim|Nova expiração do token de bloqueio de mensagem correspondente aos tokens de bloqueio da solicitação.|  
  
### <a name="peek-message"></a>Espiar mensagem  

Espia mensagens sem bloqueio.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Sim|Número de sequência do qual a espiada será iniciada.|  
|`message-count`|INT|Sim|Número máximo de mensagens a serem espiadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: sem conteúdo – não há mais mensagens|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|Sim|Lista de mensagens na qual cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|message|matriz de bytes|Sim|Mensagem codificada por transmissão AMQP 1.0.|  
  
### <a name="schedule-message"></a>Agendar mensagem  

Agenda mensagens. Esta operação oferece suporte a transações.
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|Sim|Lista de mensagens na qual cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|message-id|string|Sim|`amqpMessage.Properties.MessageId` como uma cadeia de caracteres|  
|session-id|Cadeia de caracteres|No|`amqpMessage.Properties.GroupId as string`|  
|partition-key|Cadeia de caracteres|No|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|chave por meio de partição|Cadeia de caracteres|No|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|matriz de bytes|Sim|Mensagem codificada por transmissão AMQP 1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matriz de long|Sim|Número de sequência das mensagens agendadas. O número de sequência é usado para cancelar.|  
  
### <a name="cancel-scheduled-message"></a>Cancelar mensagem agendada  

Cancela as mensagens agendadas.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matriz de long|Sim|Números de sequência das mensagens agendadas a serem canceladas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|   
  
## <a name="session-operations"></a>Operações da sessão  
  
### <a name="session-renew-lock"></a>Renovar Bloqueio da Sessão  

Estende o bloqueio de uma mensagem até o momento especificado na descrição da entidade.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: sem conteúdo – não há mais mensagens|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expiração| timestamp|Sim|Nova expiração.|  
  
### <a name="peek-session-message"></a>Espirar Mensagem da Sessão  

Espia as mensagens da sessão sem bloqueio.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|Sim|Número de sequência do qual a espiada será iniciada.|  
|message-count|INT|Sim|Número máximo de mensagens a serem espiadas.|  
|session-id|string|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: sem conteúdo – não há mais mensagens|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|Sim|Lista de mensagens na qual cada mapa representa uma mensagem.|  
  
 O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|message|matriz de bytes|Sim|Mensagem codificada por transmissão AMQP 1.0.|  
  
### <a name="set-session-state"></a>Definir Estado de Sessão  

Define o estado de uma sessão.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sim|ID da sessão.|  
|session-state|matriz de bytes|Sim|Dados binários opacos.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
### <a name="get-session-state"></a>Obter Estado de Sessão  

Obtém o estado de uma sessão.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-state|matriz de bytes|Sim|Dados binários opacos.|  
  
### <a name="enumerate-sessions"></a>Enumerar Sessões  

Enumera as sessões em uma entidade de mensagens.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|last-updated-time| timestamp|Sim|Um filtro para incluir apenas as sessões atualizadas após determinado tempo.|  
|skip|INT|Sim|Ignore um número de sessões.|  
|top|INT|Sim|Número máximo de sessões.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: sem conteúdo – não há mais mensagens|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|skip|INT|Sim|Número de sessões ignoradas se o código de status for 200.|  
|sessions-ids|Matriz de cadeias de caracteres|Sim|Matriz de IDs de sessão se o código de status for 200.|  
  
## <a name="rule-operations"></a>Operações de regra  
  
### <a name="add-rule"></a>Adicionar regra  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|Sim|Nome da regra, não incluindo nomes de tópico e assinatura.|  
|rule-description|map|Sim|Descrição da regra, conforme especificado na próxima seção.|  
  
O mapa de **Descrição de regra** deve incluir as entradas a seguir, onde **SQL-Filter** e **correlação-Filter** são mutuamente exclusivos:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Sim|`sql-filter`, conforme especificado na próxima seção.|  
|correlation-filter|map|Sim|`correlation-filter`, conforme especificado na próxima seção.|  
|sql-rule-action|map|Sim|`sql-rule-action`, conforme especificado na próxima seção.|  
  
O mapa sql-filter deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expressão|string|Sim|Expressão de filtro SQL.|  
  
O mapa **correlação-filtro** deve incluir pelo menos uma das seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|correlation-id|Cadeia de caracteres|No||  
|message-id|Cadeia de caracteres|Não||  
|para|Cadeia de caracteres|No||  
|reply-to|Cadeia de caracteres|No||  
|label|Cadeia de caracteres|No||  
|session-id|Cadeia de caracteres|No||  
|reply-to-session-id|Cadeia de caracteres|No||  
|content-type|Cadeia de caracteres|No||  
|properties|mapa|Não|É mapeado para [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) do Barramento de Serviço.|  
  
O mapa **SQL-Rule-Action** deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expressão|string|Sim|Expressão de ação do SQL.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
### <a name="remove-rule"></a>Remover Regra  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|Sim|Nome da regra, não incluindo nomes de tópico e assinatura.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
### <a name="get-rules"></a>Obter regras

#### <a name="request"></a>Solicitação

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:

|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  

O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|top|INT|Sim|O número de regras para buscar na página.|  
|skip|INT|Sim|O número de regras a serem ignoradas. Define o índice inicial (+ 1) na lista de regras. | 

#### <a name="response"></a>Resposta

O mensagem de resposta inclui as seguintes propriedades:

|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|regras| matriz de mapa|Sim|Matriz de regras. Cada regra é representada por um mapa.|

Cada entrada de mapa na matriz inclui as seguintes propriedades:

|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|rule-description|matriz de objetos descritos|Sim|`com.microsoft:rule-description:list` com AMQP descrito código 0x0000013700000004| 

`com.microsoft.rule-description:list` é uma matriz de objetos descritos. A matriz inclui o seguinte:

|Índice|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
| 0 | matriz de objetos descritos | Sim | `filter` conforme especificado abaixo. |
| 1 | matriz de objeto descrito | Sim | `ruleAction` conforme especificado abaixo. |
| 2 | string | Sim | nome da regra. |

`filter` pode ser de qualquer um dos seguintes tipos:

| Nome do descritor | Código do descritor | Valor |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtro SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtro de correlação |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Filtro True que representa 1=1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtro False que representa 1=0 |

`com.microsoft:sql-filter:list` é uma matriz descrita que inclui:

|Índice|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
| 0 | string | Sim | Expressão de filtro SQL |

`com.microsoft:correlation-filter:list` é uma matriz descrita que inclui:

|Índice (se existir)|Tipo de valor|Conteúdo de valor|  
|---------|----------------|--------------|
| 0 | string | ID de Correlação |
| 1 | string | ID da mensagem |
| 2 | string | Para |
| 3 | string | Responder Para |
| 4 | string | Label |
| 5 | string | ID da sessão |
| 6 | string | ID da Sessão Responder Para|
| 7 | string | Tipo de conteúdo |
| 8 | Map | Mapa de propriedades de aplicativo definido |

`ruleAction` pode ser de qualquer um dos seguintes tipos:

| Nome do descritor | Código do descritor | Valor |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Ação de regra vazia - nenhuma ação de regra presente |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Ação de regra SQL |

`com.microsoft:sql-rule-action:list` é uma matriz de objetos descritos cuja primeira entrada é uma cadeia de caracteres que contém a expressão da ação de regra SQL.

## <a name="deferred-message-operations"></a>Operações de mensagem adiada  
  
### <a name="receive-by-sequence-number"></a>Receber pelo número de sequência  

Recebe as mensagens adiadas por número de sequência.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matriz de long|Sim|Números de sequência.|  
|receiver-settle-mode|ubyte|Sim|Modo de **liquidação do destinatário** , conforme especificado no AMQP Core v 1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|Sim|Lista de mensagens, em que cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|Sim|Token de bloqueio se `receiver-settle-mode` for 1.|  
|message|matriz de bytes|Sim|Mensagem codificada por transmissão AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Atualizar status de disposição  

Atualiza o status de disposição das mensagens adiadas. Esta operação oferece suporte a transações.
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|Sim|concluído<br /><br /> abandonado<br /><br /> suspenso|  
|lock-tokens|matriz de uuid|Sim|Tokens de bloqueio de mensagem para atualizar o status de disposição.|  
|deadletter-reason|Cadeia de caracteres|No|Pode ser definido se o status de disposição é definido como **suspenso**.|  
|deadletter-description|Cadeia de caracteres|No|Pode ser definido se o status de disposição é definido como **suspenso**.|  
|properties-to-modify|mapa|Não|Lista de propriedades de mensagem agenciada do Barramento de Serviço a serem modificadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|INT|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|No|A descrição do status.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o AMQP e o Barramento de Serviço, visite os seguintes links:

* [Visão geral do Barramento de Serviço para AMQP]
* [Guia do protocolo AMQP 1.0]
* [AMQP no Barramento de Serviço para Windows Server]

[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no Barramento de Serviço para Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)