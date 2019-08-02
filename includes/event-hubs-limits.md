---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b105fb14608d53c5c2ef469ab44e211ccdf4d3c8
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812441"
---
A tabela a seguir relaciona as cotas e limites específicos para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para saber mais sobre os preços dos Hubs de Eventos, veja os [preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Escopo | Observações | Valor |
| --- | --- | --- | --- |
| Número de namespaces de Hubs de Eventos do Azure por assinatura |Assinatura |- |100 |
| Número de hubs de eventos por namespace |Namespace |Solicitações subsequentes para a criação de um novo hub de eventos são rejeitadas. |10 |
| O número de partições por hub de eventos |Entidade |- |32 |
| Número de grupos de consumidores por hub de eventos |Entidade |- |20 |
| Número de conexões AMQP por namespace |Namespace |Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |5\.000 |
| Tamanho máximo de eventos de Hubs de Eventos|Entidade |- |1 MB |
| Tamanho máximo do nome de um hub de eventos |Entidade |- |50 caracteres |
| Número de destinatários sem época por grupo de consumidores |Entidade |- |5 |
| Período de retenção máximo dos dados do evento |Entidade |- |Um a sete dias |
| Unidades de produtividade máxima |Namespace |Exceder o limite de unidade de produtividade faz com que seus dados para ser limitadas e gera uma [exceção de servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um número maior de unidades de taxa de transferência para uma camada Standard, arquivo de um [solicitação de suporte](/azure/azure-supportability/how-to-create-azure-support-request). As [unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20, em uma base de compra garantida. |20 |
| Número de regras de autorização por namespace |Namespace|Solicitações subsequentes de criação de regra de autorização são rejeitadas.|12 |
| Número de chamadas para o método GetRuntimeInformation | Entidade | - | 50 por segundo | 
| Número de regras de configuração de IP e a rede virtual (VNet) | Entidade | - | 128 | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>Dedicada dos Hubs de eventos - cotas e limites
A oferta de Hubs de eventos dedicados é cobrada por um preço mensal fixo, com um mínimo de 4 horas de uso. A camada dedicada oferece todos os recursos do plano Standard, mas com limites e capacidade em escala empresarial para clientes com cargas de trabalho exigentes. 

| Recurso | limites |
| --- | ---|
| Largura de banda |  20 CUs |
| Namespaces | 50 por CU |
| Hubs de Eventos |  1000 por namespace |
| Eventos de entrada | Incluso |
| Tamanho da mensagem | 1 milhão de Bytes |
| Partições | 2000 por CU |
| Grupos de consumidores | Nenhum limite por CU, 1000 por hub de eventos |
| Conexões orientadas | 100.000 incluídos |
| Retenção de mensagem | Backup (retenção de 90 dias em breve), de 7 dias, 10 TB incluído por CU |
| Captura | Incluso |
