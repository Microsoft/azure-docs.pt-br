---
title: Entrega e repetição da Grade de Eventos do Azure
description: Descreve como a Grade de Eventos do Azure entrega eventos e como ela trata mensagens não entregues.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: spelluru
ms.openlocfilehash: 6dfa84eff8dcc104ae6f9c16262f3b1c697df6c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561991"
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega e repetição de mensagens da Grade de Eventos

Este artigo descreve como a Grade de Eventos do Azure manipula eventos quando a entrega não é confirmada.

A entrega proporcionada pela Grade de Eventos tem um tempo de duração. Cada mensagem é entregue pelo menos uma vez para cada assinatura. Os eventos são enviados para o ponto de extremidade registrado de cada assinatura imediatamente. Se um ponto de extremidade não confirmar o recebimento de um evento, a Grade de Eventos tentará entregá-lo novamente.

Atualmente, a Grade de Eventos envia cada evento individualmente aos assinantes. O assinante recebe uma matriz com um único evento.

## <a name="retry-schedule-and-duration"></a>Agendamento de nova tentativa e duração

A Grade de Eventos usa uma política de repetição de retirada exponencial para a entrega de eventos. Se um ponto de extremidade não responde ou retorna um código de falha, a grade de eventos repete a entrega na seguinte agenda em uma base de melhor esforço:

1. 10 segundos
1. 30 segundos
1. 1 minuto
1. 5 minutos
1. 10 minutos
1. 30 minutos
1. 1 hora
1. Por hora por até 24 horas

Grade de eventos adiciona uma pequena aleatoriedade para todas as etapas de repetição e oportunamente pode ignorar determinadas tentativas se um ponto de extremidade não está íntegro consistentemente, inativo por um longo período ou parece estar sobrecarregado.

Para um comportamento determinístico, defina a hora do evento ao vivo e tentativas de entrega máximo do [políticas de repetição de assinatura](manage-event-delivery.md).

Por padrão, a Grade de Eventos expira todos os eventos que não são entregues em 24 horas. Você pode [personalizar a política de repetição](manage-event-delivery.md) ao criar uma assinatura de evento. Forneça o número máximo de tentativas de entrega (o padrão é 30) e a vida útil do evento (o padrão é 1440 minutos).

## <a name="dead-letter-events"></a>Eventos de mensagens mortas

Quando a Grade de Eventos não pode fornecer um evento, ela pode enviar o evento não entregue para uma conta de armazenamento. Este processo é conhecido como armazenamento de mensagens mortas. Por padrão, a Grade de Eventos não ativa o armazenamento de mensagens mortas. Para habilitá-lo, você deve especificar uma conta de armazenamento para reter eventos que não foram entregues ao criar a assinatura do evento. Você aciona eventos dessa conta de armazenamento para resolver as entregas.

A Grade de Eventos enviará um evento ao local de mensagens mortas quando ela tiver tentado todas as suas tentativas de repetição. Se a Grade de Eventos receber um código de resposta 400 (Solicitação incorreta) ou 413 (A entidade da solicitação é grande demais), ela enviará o evento imediatamente ao ponto de extremidade de mensagens mortas. Esses códigos de resposta indicam que a entrega do evento nunca terá êxito.

Há um atraso de cinco minutos entre a última tentativa de entregar de um evento e quando ela é entregue para o local de inatividade. Esse atraso tem como objetivo reduzir o número de operações de armazenamento Blob. Se o local de mensagens mortas não estiver disponível por quatro horas, o evento será descartado.

Antes de configurar o local de mensagens mortas, você deve ter uma conta de armazenamento com um contêiner. É possível fornecer o ponto de extremidade para esse contêiner ao criar a assinatura do evento. O ponto de extremidade está no formato de: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Talvez você queira ser notificado quando um evento tiver sido enviado para o local de mensagens mortas. Para usar a Grade de Eventos para responder a eventos não entregues, [crie uma assinatura de evento](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) para o armazenamento de blob de mensagens mortas. Toda vez que seu armazenamento de blob de mensagens mortas recebe um evento não entregue, a Grade de Eventos notifica o manipulador. O manipulador responde com ações que você deseja executar para reconciliar eventos não entregues.

Para ver um exemplo de como configurar um local de mensagens mortas, confira [Dead letter and retry policies](manage-event-delivery.md) (Políticas de mensagens mortas e repetição).

## <a name="message-delivery-status"></a>Status de entrega da mensagem

A Grade de Eventos usa códigos de resposta HTTP para confirmar o recebimento de eventos. 

### <a name="success-codes"></a>Códigos de êxito

Os códigos de resposta HTTP a seguir indicam que um evento foi entregue com êxito ao webhook. A Grade de Eventos considera entrega concluída.

- 200 OK
- 202 Aceito

### <a name="failure-codes"></a>Códigos de falha

Os códigos de resposta HTTP a seguir indicam que houve falha na tentativa de entrega do evento.

- 400 Solicitação Inválida
- 401 Não Autorizado
- 404 Não Encontrado
- 408 Tempo limite da solicitação
- Solicitação 413 entidade muito grande
- 414 URI muito longo
- 429 Número excessivo de solicitações
- 500 Erro Interno do Servidor
- 503 Serviço Indisponível
- 504 Tempo Limite do Gateway

## <a name="next-steps"></a>Próximas etapas

* Para exibir o status de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para personalizar as opções de entrega de eventos, confira [Dead letter and retry policies](manage-event-delivery.md) (Políticas de mensagens mortas e repetição).