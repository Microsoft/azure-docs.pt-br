---
title: Hubs de eventos do Azure-exceções .NET
description: Este artigo fornece uma lista de exceções de mensagens do .NET dos hubs de eventos do Azure e ações sugeridas.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: spelluru
ms.openlocfilehash: af2d820fd255d041ade21a00d7d7a9ac6b12fcba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745895"
---
# <a name="eventhubsexception---net"></a>EventHubsException-.NET
Um **EventHubsException** é disparado quando uma operação específica para os hubs de eventos causou um problema, incluindo ambos os erros dentro do serviço e específico para o cliente. 

## <a name="exception-information"></a>Informações da exceção
A exceção inclui as seguintes informações contextuais para ajudar a entender o contexto do erro e sua severidade relativa. 

- **Istransitória**: identifica se a exceção é considerada recuperável ou não. No caso em que foi considerado transitório, a política de repetição apropriada já foi aplicada e as novas tentativas não foram bem-sucedidas.
- **Motivo**: fornece um conjunto de motivos conhecidos para a falha que ajudam a categorizar e esclarecer a causa raiz. Esses motivos destinam-se a permitir a aplicação de filtragem de exceção e outra lógica ao inspecionar o texto de uma mensagem de exceção que não seria ideal. Alguns motivos de falha de chave são:
    - **Cliente fechado**: ocorre quando um cliente do hub de eventos já foi fechado ou descartado. Recomendamos que você verifique o código do aplicativo para garantir que os objetos da biblioteca de cliente dos hubs de eventos sejam criados e fechados no escopo pretendido.
    - **Tempo limite do serviço**: indica que o serviço de hubs de eventos não respondeu a uma operação dentro do período de tempo esperado. Esse problema pode ter sido causado por um problema de rede ou problema de serviço transitório. O serviço de hubs de eventos pode ou não ter concluído a solicitação com êxito; o status não é conhecido. É recomendável tentar verificar o estado atual e tentar novamente, se necessário.
    - **Cota excedida**: indica que há muitas operações de leitura ativas para um único grupo de consumidores. Esse limite depende da camada do namespace dos hubs de eventos e a mudança para uma camada mais alta pode ser necessária. Uma alternativa seria criar grupos de consumidores adicionais e garantir que o número de leituras de clientes do consumidor para qualquer grupo esteja dentro do limite. Para obter mais informações, consulte [limites e cotas de hubs de eventos do Azure](event-hubs-quotas.md).
    - **Tamanho da mensagem excedido**: dados de evento como um tamanho máximo permitido para um evento individual e um lote de eventos. Ele inclui os dados do evento e quaisquer metadados associados e sobrecarga do sistema. Para resolver esse erro, reduza o número de eventos enviados em um lote ou reduza o tamanho dos dados incluídos na mensagem. Como os limites de tamanho estão sujeitos a alterações, consulte para as [cotas e limites dos hubs de eventos do Azure](event-hubs-quotas.md) para obter informações específicas.
    - **Consumidor desconectado**: um cliente consumidor foi desconectado pelo serviço Hub de eventos da instância do hub de eventos. Normalmente ocorre quando um consumidor com um nível mais alto de proprietário declara a propriedade de um emparelhamento de grupo de consumidores e partições.
    - **Recurso não encontrado**: o serviço de hubs de eventos não encontrou um recurso como um hub de eventos, grupo de consumidores ou partição. Ele pode ter sido excluído ou há um problema com o próprio serviço de hubs de eventos.

## <a name="handling-exceptions"></a>Tratamento de exceções
Você pode reagir a uma razão de falha específica para o **EventHubException**  de várias maneiras. Uma delas é aplicar uma cláusula de filtro de exceção como parte do bloco catch.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Próximas etapas
Há outras exceções documentadas no [artigo herdado](event-hubs-messaging-exceptions.md). Alguns deles se aplicam somente à biblioteca de cliente .NET dos hubs de eventos herdados.