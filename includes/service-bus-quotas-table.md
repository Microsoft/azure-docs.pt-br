---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6c5d881c5ca6eee14835ab0ec10bff2749299dc
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68502260"
---
A tabela a seguir lista informações de cota específicas para mensagens do barramento de serviço do Azure. Para obter informações sobre preços e outras cotas para o barramento de serviço, consulte [preços do barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).

| Nome de cota | Escopo | Observações | Valor |
| --- | --- | --- | --- |
| Número máximo de namespaces Basic ou Standard por assinatura do Azure |Namespace |As solicitações subsequentes para namespaces básicos ou padrão adicionais são rejeitadas pelo portal do Azure. |100|
| Número máximo de namespaces Premium por assinatura do Azure |Namespace |As solicitações subsequentes para namespaces Premium adicionais são rejeitadas pelo portal. |50 |
| Tamanho da fila ou do tópico |Entidade |Definido na criação da fila ou do tópico. <br/><br/> As mensagens de entrada subsequentes são rejeitadas e uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 GB ou 5 GB.<br /><br />No SKU Premium e o SKU Standard com [particionamento](/azure/service-bus-messaging/service-bus-partitioning) habilitado, o tamanho máximo da fila ou do tópico é de 80 GB. |
| Número de conexões simultâneas em um namespace |Namespace |As solicitações subsequentes para conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. As operações REST não contam para conexões TCP simultâneas. |NetMessaging: 1.000.<br /><br />AMQP: 5.000. |
| Número de solicitações de recebimento simultâneas em uma entidade de fila, tópico ou assinatura |Entidade |As solicitações de recebimento subsequentes são rejeitadas e uma exceção é recebida pelo código de chamada. Essa cota aplica-se ao número combinado de operações de recebimento simultâneas em todas as assinaturas em um tópico. |5\.000 |
| Número de tópicos ou filas por namespace |Namespace |Solicitações subsequentes de criação de um novo tópico ou fila no namespace são rejeitadas. Como resultado, se configurado por meio da [portal do Azure][Azure portal], uma mensagem de erro será gerada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |10.000 para a camada básica ou Standard. O número total de tópicos e filas em um namespace deve ser menor ou igual a 10.000. <br/><br/>Para a camada Premium, 1.000 por unidade de mensagens (MU). O limite máximo é de 4.000. |
| Número de [Tópicos ou filas particionados](/azure/service-bus-messaging/service-bus-partitioning) por namespace |Namespace |As solicitações subsequentes para a criação de um novo tópico ou fila particionado no namespace são rejeitadas. Como resultado, se configurado por meio da [portal do Azure][Azure portal], uma mensagem de erro será gerada. Se chamado da API de gerenciamento, a exceção **QuotaExceededException** será recebida pelo código de chamada. |Camadas básica e Standard: 100.<br/><br/>Não há suporte para entidades particionadas na camada [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) .<br/><br />Cada fila ou tópico particionado conta para a cota de 1.000 entidades por namespace. |
| Tamanho máximo de qualquer caminho de entidade de mensagens: fila ou tópico |Entidade |- |260 caracteres. |
| Tamanho máximo de qualquer nome de entidade de mensagens: namespace, assinatura ou regra de assinatura |Entidade |- |50 caracteres. |
| Tamanho máximo de uma [mensagem ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entidade |- | 128 |
| Tamanho máximo de uma [ID de sessão](/dotnet/api/microsoft.azure.servicebus.message.sessionid) de mensagem | Entidade |- | 128 |
| Tamanho da mensagem para uma fila, tópico ou entidade de assinatura |Entidade |Mensagens de entrada que excedem essas cotas são rejeitadas e uma exceção é recebida pelo código de chamada. |Tamanho máximo da mensagem: 256 KB para a [camada Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB para a [camada Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Devido à sobrecarga do sistema, esse limite é menor do que esses valores.<br /><br />Tamanho máximo do cabeçalho: 64 KB.<br /><br />Número máximo de propriedades de cabeçalho no recipiente de propriedades: **byte/int. MaxValue**.<br /><br />Tamanho máximo da propriedade no recipiente de propriedades: Nenhum limite explícito. Limitado pelo tamanho máximo do cabeçalho. |
| Tamanho da propriedade da mensagem para uma fila, um tópico ou uma entidade de assinatura |Entidade | A exceção **serializaexception** é gerada. |O tamanho máximo da propriedade de mensagem para cada propriedade é 32.000. O tamanho cumulativo de todas as propriedades não pode exceder 64.000. Esse limite se aplica ao cabeçalho inteiro do [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), que tem propriedades do usuário e propriedades do sistema, como [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [rótulo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)e [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Número de assinaturas por tópico |Entidade |As solicitações subsequentes para a criação de assinaturas adicionais para o tópico são rejeitadas. Como resultado, se configuradas por meio do portal, uma mensagem de erro é mostrada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |2\.000 por tópico para a camada básica ou Standard. |
| Número de filtros SQL por tópico |Entidade |As solicitações subsequentes para a criação de filtros adicionais no tópico são rejeitadas e uma exceção é recebida pelo código de chamada. |2\.000 |
| Número de filtros de correlação por tópico |Entidade |As solicitações subsequentes para a criação de filtros adicionais no tópico são rejeitadas e uma exceção é recebida pelo código de chamada. |100.000 |
| Tamanho de filtros ou ações SQL |Namespace |As solicitações subsequentes para a criação de filtros adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |Comprimento máximo da cadeia de caracteres de condição de filtro: 1.024 (1 K).<br /><br />Comprimento máximo da cadeia de caracteres de condição de função: 1.024 (1 K).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de regras [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) por namespace, fila ou tópico |Entidade, namespace |As solicitações subsequentes para a criação de regras adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |Número máximo de regras: 12. <br /><br /> As regras configuradas em um namespace do Barramento de Serviço se aplicam a todas as filas e tópicos no namespace. |
| Número de mensagens por transação | Transação | Mensagens de entrada adicionais são rejeitadas e uma exceção informando "não é possível enviar mais de 100 mensagens em uma única transação" é recebida pelo código de chamada. | 100 <br /><br /> Para ambas as operações **Send()** e **SendAsync()** . |

[Azure portal]: https://portal.azure.com
