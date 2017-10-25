---
title: "AMQP 1.0 em operações baseadas em solicitação-resposta do Barramento de Serviço do Azure | Microsoft Docs"
description: "Lista de operações baseadas em solicitação-resposta do Barramento de Serviço do Microsoft Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.openlocfilehash: 756565b3da6e0a818d1ee3d5e17f942d96be14f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 no Barramento de Serviço do Microsoft Azure: operações baseadas em solicitação-resposta

Este tópico define a lista de operações baseadas em solicitação/resposta do Barramento de Serviço do Microsoft Azure. Estas informações baseiam-se no rascunho funcional do Gerenciamento de AMQP Versão 1.0.  
  
Para um guia de protocolo AMQP 1.0 detalhado no nível de transmissão, que explica como o Barramento de Serviço implementa e se baseia na especificação técnica AMQP OASIS, confira o [AMQP 1.0 no guia de protocolo do Barramento de Serviço do Azure e dos Hubs de Eventos](service-bus-amqp-protocol-guide.md).  
  
## <a name="concepts"></a>Conceitos  
  
### <a name="entity-description"></a>Descrição de entidade  

Uma descrição de entidade refere-se a um objeto da [Classe QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [Classe TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) ou [Classe SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) do Barramento de Serviço.  
  
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
  
```  
requestLink.sendTransfer(  
        Message(  
                properties: {  
                        message-id: <request id>,  
                        reply-to: "<my response link unique address>"  
                },  
                application-properties: {  
                        "operation" -> "<operation>",  
                },  
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
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|string|Não|A descrição do status.|  
  
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
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|longo|Sim|Número de sequência do qual a espiada será iniciada.|  
|`message-count`|int|Sim|Número máximo de mensagens a serem espiadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 0xcc: nenhum conteúdo – não há mais mensagens|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|Sim|Lista de mensagens na qual cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|message|matriz de bytes|Sim|Mensagem codificada por transmissão AMQP 1.0.|  
  
### <a name="schedule-message"></a>Agendar mensagem  

Agenda mensagens.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|Sim|Lista de mensagens na qual cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|message-id|string|Sim|`amqpMessage.Properties.MessageId` como uma cadeia de caracteres|  
|session-id|string|Sim|`amqpMessage.Properties.GroupId as string`|  
|partition-key|string|Sim|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|  
|message|matriz de bytes|Sim|Mensagem codificada por transmissão AMQP 1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um mapa com as seguintes entradas:  
  
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
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matriz de long|Sim|Números de sequência das mensagens agendadas a serem canceladas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matriz de long|Sim|Número de sequência das mensagens agendadas. O número de sequência é usado para cancelar.|  
  
## <a name="session-operations"></a>Operações da sessão  
  
### <a name="session-renew-lock"></a>Renovar Bloqueio da Sessão  

Estende o bloqueio de uma mensagem até o momento especificado na descrição da entidade.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 0xcc: nenhum conteúdo – não há mais mensagens|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expiração|timestamp|Sim|Nova expiração.|  
  
### <a name="peek-session-message"></a>Espirar Mensagem da Sessão  

Espia as mensagens da sessão sem bloqueio.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|longo|Sim|Número de sequência do qual a espiada será iniciada.|  
|message-count|int|Sim|Número máximo de mensagens a serem espiadas.|  
|session-id|string|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 0xcc: nenhum conteúdo – não há mais mensagens|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um mapa com as seguintes entradas:  
  
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
|operation|string|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sim|ID da sessão.|  
|session-state|matriz de bytes|Sim|Dados binários opacos.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
### <a name="get-session-state"></a>Obter Estado de Sessão  

Obtém o estado de uma sessão.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
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
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|Sim|Um filtro para incluir apenas as sessões atualizadas após determinado tempo.|  
|skip|int|Sim|Ignore um número de sessões.|  
|top|int|Sim|Número máximo de sessões.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 0xcc: nenhum conteúdo – não há mais mensagens|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|skip|int|Sim|Número de sessões ignoradas se o código de status for 200.|  
|sessions-ids|matriz de cadeias de caracteres|Sim|Matriz de IDs de sessão se o código de status for 200.|  
  
## <a name="rule-operations"></a>Operações de regra  
  
### <a name="add-rule"></a>Adicionar regra  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|Sim|Nome da regra, não incluindo nomes de tópico e assinatura.|  
|rule-description|map|Sim|Descrição da regra, conforme especificado na próxima seção.|  
  
O mapa **rule-description** deve incluir as seguintes entradas, em que **sql-filter** e **correlation-filter** são mutuamente exclusivos:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Sim|`sql-filter`, conforme especificado na próxima seção.|  
|correlation-filter|map|Sim|`correlation-filter`, conforme especificado na próxima seção.|  
|sql-rule-action|map|Sim|`sql-rule-action`, conforme especificado na próxima seção.|  
  
O mapa sql-filter deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expressão|string|Sim|Expressão de filtro SQL.|  
  
O mapa **correlation-filter** deve incluir, pelo menos, uma das seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|Não||  
|message-id|string|Não||  
|para|string|Não||  
|reply-to|string|Não||  
|label|string|Não||  
|session-id|string|Não||  
|reply-to-session-id|string|Não||  
|content-type|string|Não||  
|propriedades|map|Não|É mapeado para [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties) do Barramento de Serviço.|  
  
O mapa **sql-rule-action** deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expressão|string|Sim|Expressão de ação do SQL.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
### <a name="remove-rule"></a>Remover Regra  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|Sim|Nome da regra, não incluindo nomes de tópico e assinatura.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
## <a name="deferred-message-operations"></a>Operações de mensagem adiada  
  
### <a name="receive-by-sequence-number"></a>Receber pelo número de sequência  

Recebe as mensagens adiadas por número de sequência.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|matriz de long|Sim|Números de sequência.|  
|receiver-settle-mode|ubyte|Sim|Modo de **liquidação do receptor**, conforme especificado no AMQP Core v1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|da nuvem para o dispositivo|lista de mapas|Sim|Lista de mensagens, em que cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|Sim|Token de bloqueio se `receiver-settle-mode` for 1.|  
|message|matriz de bytes|Sim|Mensagem codificada por transmissão AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Atualizar status de disposição  

Atualiza o status de disposição das mensagens adiadas.  
  
#### <a name="request"></a>Solicitação  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Sim|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite da operação no servidor em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **amqp-value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|Sim|concluído<br /><br /> abandonado<br /><br /> suspenso|  
|lock-tokens|matriz de uuid|Sim|Tokens de bloqueio de mensagem para atualizar o status de disposição.|  
|deadletter-reason|string|Não|Pode ser definido se o status de disposição é definido como **suspenso**.|  
|deadletter-description|string|Não|Pode ser definido se o status de disposição é definido como **suspenso**.|  
|properties-to-modify|map|Não|Lista de propriedades de mensagem agenciada do Barramento de Serviço a serem modificadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|A descrição do status.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o AMQP e o Barramento de Serviço, visite os seguintes links:

* [Visão geral do Barramento de Serviço para AMQP]
* [Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]
* [AMQP no Barramento de Serviço para Windows Server]

[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md
[Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP no Barramento de Serviço para Windows Server]: https://msdn.microsoft.com/library/dn574799.asp