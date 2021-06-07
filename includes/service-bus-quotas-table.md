---
title: incluir arquivo
description: incluir arquivo
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ee066ff46f319749469a41e6decf12b35c0ee27e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651929"
---
A tabela a seguir lista as informações de cota específicas às mensagens do Barramento de Serviço do Azure. Para obter informações sobre preços e outras cotas do Barramento de Serviço, confira [Preços do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

| Nome da cota | Escopo | Observações | Valor |
| --- | --- | --- | --- |
| Número máximo de namespaces Básico/Standard por assinatura do Azure |Namespace |As solicitações seguintes por namespaces adicionais Básico/Standard são rejeitadas pelo portal do Azure. |100|
| Número máximo de namespaces Premium por assinatura do Azure |Namespace |As solicitações subsequentes por namespaces adicionais Premium são rejeitadas pelo portal. |100 |
| Tamanho da fila ou do tópico |Entidade |Definida na criação da fila ou do tópico. <br/><br/> Mensagens de entrada seguintes são rejeitadas, e uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 GB ou 5 GB.<br /><br />No SKU Premium, e no SKU Standard com [particionamento](../articles/service-bus-messaging/service-bus-partitioning.md) habilitado, o tamanho máximo da fila ou do tópico é 80 GB. |
| Número de conexões simultâneas em um namespace |Namespace |Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. Operações REST não são consideradas conexões TCP simultâneas. |Mensagens de rede: 1.000.<br /><br />AMQP: 5.000. |
| Número de solicitações de recebimento simultâneas em uma entidade de fila, tópico ou assinatura |Entidade |As solicitações de recebimento seguintes são rejeitadas, e uma exceção é recebida pelo código de chamada. Essa cota aplica-se ao número combinado de operações de recebimento simultâneas em todas as assinaturas em um tópico. |5\.000 |
| Número de tópicos ou filas por namespace |Namespace |Solicitações subsequentes de criação de um novo tópico ou fila no namespace são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |10.000 para a camada Básica ou Standard. O número total de tópicos e filas em um namespace deve ser menor ou igual a 10.000. <br/><br/>Para a camada Premium, 1.000 por MU (unidade do sistema de mensagens). |
| Número de [tópicos ou filas particionados](../articles/service-bus-messaging/service-bus-partitioning.md) por namespace |Namespace |As solicitações subsequentes para a criação de um novo tópico ou fila particionado no namespace são rejeitadas. Consequentemente, se configuradas por meio do [Portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado da API de gerenciamento, a exceção **QuotaExceededException** é recebida pelo código de chamada. |Camadas Básica e Standard: 100.<br/><br/>Não há suporte para entidades particionadas na camada [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md).<br/><br />Cada fila ou tópico particionado conta para a cota de 1.000 entidades por namespace. |
| Tamanho máximo de qualquer caminho de entidade de mensagens: fila ou tópico |Entidade |- |260 caracteres. |
| Tamanho máximo de qualquer nome de entidade de mensagens: namespace, assinatura ou regra de assinatura |Entidade |- |50 caracteres. |
| Tamanho máximo de uma mensagem ID | Entidade |- | 128 |
| Tamanho máximo de uma ID da sessão de mensagem | Entidade |- | 128 |
| Tamanho da mensagem para uma entidade de fila, tópico ou assinatura |Entidade |As mensagens de entrada que excederem essas cotas são rejeitadas, e uma exceção é recebida pelo código de chamada. |Tamanho máximo da mensagem: 256 KB para a [camada Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB para a [camada Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Devido à sobrecarga do sistema, esse limite é menor do que esses valores.<br /><br />Tamanho máximo do cabeçalho: 64 KB.<br /><br />Número máximo de propriedades de cabeçalho no recipiente de propriedades: **byte/int.MaxValue**.<br /><br />tamanho máximo da propriedade no recipiente de propriedades: nenhum limite explícito. Limitado pelo tamanho máximo do cabeçalho. |
| Tamanho de propriedade de mensagem para uma entidade de fila, tópico ou assinatura |Entidade | A exceção `SerializationException` é gerada. |O tamanho máximo de propriedade de mensagem para cada propriedade é 32.000. O tamanho cumulativo de todas as propriedades não pode exceder 64.000. Esse limite se aplica a todo o cabeçalho da mensagem agenciada, que tem propriedades de usuário e de sistema, como número da sequência, rótulo e ID da mensagem. |
| Número de assinaturas por tópico |Entidade |As solicitações subsequentes para a criação de assinaturas adicionais para o tópico são rejeitadas. Como resultado, se configuradas por meio do portal, uma mensagem de erro é mostrada. Se chamado da API de gerenciamento, uma exceção é recebida pelo código de chamada. |2\.000 por tópico para a camada Standard e a camada Premium. |
| Número de filtros SQL por tópico |Entidade |As solicitações seguintes de criação de filtros adicionais para o tópico são rejeitadas, e uma exceção é recebida pelo código de chamada. |2\.000 |
| Número de filtros de correlação por tópico |Entidade |As solicitações seguintes de criação de filtros adicionais para o tópico são rejeitadas, e uma exceção é recebida pelo código de chamada. |100,000 |
| Tamanho de filtros ou ações SQL |Namespace |As solicitações seguintes de criação de filtros adicionais são rejeitadas, e uma exceção é recebida pelo código de chamada. |Comprimento máximo da cadeia de caracteres de condição de filtro: 1.024 (1.000).<br /><br />Comprimento máximo da cadeia de caracteres de condição de função: 1.024 (1.000).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de regras de autorização de acesso compartilhado por namespace, fila ou tópico |Entidade, namespace |As solicitações seguintes de criação de regras adicionais são rejeitadas, e uma exceção é recebida pelo código de chamada. |Número máximo de regras por tipo de entidade: 12. <br /><br /> As regras configuradas em um namespace do Barramento de Serviço se aplicam a todos os tipos: filas, tópicos. |
| Número de mensagens por transação | Transação | As mensagens de entrada adicionais são rejeitadas, e uma exceção indicando "Não é possível enviar mais de 100 mensagens em uma transação única" é recebida pelo código de chamada. | 100 <br /><br /> Para ambas as operações **Send()** e **SendAsync()**. |
| Número de regras de rede virtual e filtro IP | Namespace | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
