---
title: Entrega e repetição da Grade de Eventos do Azure
description: Descreve como a Grade de Eventos do Azure entrega eventos e como ela trata mensagens não entregues.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 0945b06f78ac34500f0b16a4a419cff12d1a4734
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812919"
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega e repetição de mensagens da Grade de Eventos

Este artigo descreve como a Grade de Eventos do Azure manipula eventos quando a entrega não é confirmada.

A entrega proporcionada pela Grade de Eventos tem um tempo de duração. Cada mensagem é entregue pelo menos uma vez para cada assinatura. Os eventos são enviados para o ponto de extremidade registrado de cada assinatura imediatamente. Se um ponto de extremidade não confirmar o recebimento de um evento, a Grade de Eventos tentará entregá-lo novamente.

Atualmente, a Grade de Eventos envia cada evento individualmente aos assinantes. O assinante recebe uma matriz com um único evento.

## <a name="retry-schedule-and-duration"></a>Agendamento de nova tentativa e duração

Grade de eventos espera 30 segundos para uma resposta após fornecer uma mensagem. Após 30 segundos, se o ponto de extremidade não tiver respondido, a mensagem está na fila de repetição. A Grade de Eventos usa uma política de repetição de retirada exponencial para a entrega de eventos. Grade de eventos repete a entrega na seguinte agenda em uma base de melhor esforço:

- 10 segundos
- 30 segundos
- 1 minuto
- 5 minutos
- 10 minutos
- 30 minutos
- 1 hora
- Por hora por até 24 horas

Se o ponto de extremidade responde nos 3 minutos, a grade de eventos tentará remover o evento da fila de repetição em uma base de melhor esforço, mas ainda é possível receber duplicatas.

Grade de eventos adiciona uma pequena aleatoriedade para todas as etapas de repetição e oportunamente pode ignorar determinadas tentativas se um ponto de extremidade não está íntegro consistentemente, inativo por um longo período ou parece estar sobrecarregado.

Para um comportamento determinístico, defina a hora do evento ao vivo e tentativas de entrega máximo do [políticas de repetição de assinatura](manage-event-delivery.md).

Por padrão, a Grade de Eventos expira todos os eventos que não são entregues em 24 horas. Você pode [personalizar a política de repetição](manage-event-delivery.md) ao criar uma assinatura de evento. Forneça o número máximo de tentativas de entrega (o padrão é 30) e a vida útil do evento (o padrão é 1440 minutos).

## <a name="delayed-delivery"></a>Entrega atrasada

Como um ponto de extremidade experiências falhas de entrega, a grade de eventos será iniciado atrasar a entrega e repetição de eventos para esse ponto de extremidade. Por exemplo, se os dez primeiros eventos publicados em um ponto de extremidade falharem, a grade de eventos assumirá que o ponto de extremidade está com problemas e atrasará a todas as tentativas subsequentes *novos e* entregas por algum tempo – em alguns casos até algumas horas .

A finalidade funcional de entrega atrasada é proteger os pontos de extremidade não íntegro, bem como o sistema de grade de eventos. Sem retirada e atraso de entrega para pontos de extremidade não íntegro, política de repetição da grade de eventos e recursos de volume podem facilmente sobrecarregar um sistema.

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

Grade de eventos considera **apenas** os seguintes códigos de resposta HTTP como entregas bem-sucedidas. Todos os outros status códigos são considerados entregas com falha e serão repetidos ou morto conforme apropriado. Ao receber um código de status de êxito, a grade de eventos considera entrega concluída.

- 200 OK
- 201 Criado
- 202 Aceito
- 203 informações não autorizadas
- 204 Sem Conteúdo

### <a name="failure-codes"></a>Códigos de falha

Todos os outros códigos não presentes no conjunto acima (200 204) são considerados falhas e serão repetidos. Alguns têm políticas de repetição específica vinculadas a eles descritas abaixo, todos os outros seguem o padrão exponencial retirada modelo. É importante ter em mente que, devido à natureza da arquitetura da grade de eventos altamente em paralelo, o comportamento de repetição é não determinística. 

| Código de status | Tentar comportamento novamente |
| ------------|----------------|
| 400 Solicitação Inválida | Tente novamente após cinco minutos ou mais (mensagens mortas imediatamente se a instalação de mensagens mortas) |
| 401 Não Autorizado | Tente novamente após cinco minutos ou mais |
| 403 Proibido | Tente novamente após cinco minutos ou mais |
| 404 Não Encontrado | Tente novamente após cinco minutos ou mais |
| 408 Tempo Limite da Solicitação | Tente novamente após 2 minutos ou mais |
| Solicitação 413 entidade muito grande | Tente novamente após 10 segundos ou mais (mensagens mortas imediatamente se a instalação de mensagens mortas) |
| 503 Serviço Indisponível | Tente novamente após 30 segundos ou mais |
| Todos os outros | Tente novamente após 10 segundos ou mais |


## <a name="next-steps"></a>Próximas etapas

* Para exibir o status de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para personalizar as opções de entrega de eventos, confira [Dead letter and retry policies](manage-event-delivery.md) (Políticas de mensagens mortas e repetição).
