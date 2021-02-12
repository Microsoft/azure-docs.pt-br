---
title: Entrega e repetição da Grade de Eventos do Azure
description: Descreve como a Grade de Eventos do Azure entrega eventos e como ela trata mensagens não entregues.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 3c4ed6ec2c9eae4dbcf70a831e3e7f70a28a57a0
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247362"
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega e repetição de mensagens da Grade de Eventos

Este artigo descreve como a Grade de Eventos do Azure manipula eventos quando a entrega não é confirmada.

A entrega proporcionada pela Grade de Eventos tem um tempo de duração. Ele entrega cada mensagem **pelo menos uma vez** para cada assinatura. Os eventos são enviados para o ponto de extremidade registrado de cada assinatura imediatamente. Se um ponto de extremidade não confirmar o recebimento de um evento, a Grade de Eventos tentará entregá-lo novamente.

> [!NOTE]
> A grade de eventos não garante a ordem de entrega de eventos, portanto, o assinante pode recebê-las fora de ordem. 

## <a name="batched-event-delivery"></a>Entrega de eventos em lote

A grade de eventos usa como padrão o envio de cada evento individualmente aos assinantes. O assinante recebe uma matriz com um único evento. Você pode configurar a grade de eventos para eventos em lote para entrega para melhorar o desempenho de HTTP em cenários de alta taxa de transferência.

A entrega em lote tem duas configurações:

* **Máximo de eventos por lote** – número máximo de eventos que a grade de eventos fornecerá por lote. Esse número nunca será excedido, mas menos eventos poderão ser entregues se nenhum outro evento estiver disponível no momento da publicação. A grade de eventos não atrasa eventos para criar um lote se menos eventos estiverem disponíveis. Deve estar entre 1 e 5.000.
* **Tamanho de lote preferencial em kilobytes** -teto de destino para o tamanho do lote em kilobytes. Semelhante a Max Events, o tamanho do lote pode ser menor se mais eventos não estiverem disponíveis no momento da publicação. É possível que um lote seja maior do que o tamanho de lote preferencial *se* um único evento for maior do que o tamanho preferencial. Por exemplo, se o tamanho preferencial for de 4 KB e um evento de 10 KB for enviado para a grade de eventos, o evento de 10 KB ainda será entregue em seu próprio lote, em vez de ser Descartado.

Entrega em lote em configurada por assinatura por evento por meio do portal, da CLI, do PowerShell ou de SDKs.

### <a name="azure-portal"></a>Portal do Azure: 
![Configurações de entrega em lotes](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>CLI do Azure
Ao criar uma assinatura de evento, use os seguintes parâmetros: 

- **Max – eventos por lote** – número máximo de eventos em um lote. Deve ser um número entre 1 e 5000.
- **preferencial-tamanho do lote-em quilobytes** -tamanho de lote preferido em kilobytes. Deve ser um número entre 1 e 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Para obter mais informações sobre como usar CLI do Azure com a grade de eventos, consulte [rotear eventos de armazenamento para o ponto de extremidade da Web com CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Agendamento de nova tentativa e duração

Quando o EventGrid recebe um erro para uma tentativa de entrega de evento, o EventGrid decide se deve tentar novamente a entrega ou mensagens mortas ou descartar o evento com base no tipo de erro. 

Se o erro retornado pelo ponto de extremidade assinado for um erro relacionado à configuração que não pode ser corrigido com repetições (por exemplo, se o ponto de extremidade for excluído), EventGrid irá executar mensagens mortas no evento ou descartar o evento se a letra inativa não estiver configurada.

A seguir estão os tipos de pontos de extremidade para os quais repetir não acontece:

| Tipo de Ponto de Extremidade | Códigos do Erro |
| --------------| -----------|
| Recursos do Azure | 400 solicitação inadequada, entidade de solicitação 413 muito grande, 403 Proibido | 
| webhook | 400 solicitação inválida, entidade de solicitação 413 muito grande, 403 Proibido, 404 não encontrado, 401 não autorizado |
 
> [!NOTE]
> Se Dead-Letter não estiver configurado para o ponto de extremidade, os eventos serão descartados quando ocorrerem erros acima. Considere configurar mensagens mortas se você não quiser que esses tipos de eventos sejam removidos.

Se o erro retornado pelo ponto de extremidade assinado não estiver entre a lista acima, o EventGrid executará a repetição usando as políticas descritas abaixo:

A grade de eventos aguarda 30 segundos por uma resposta depois de entregar uma mensagem. Após 30 segundos, se o ponto de extremidade não tiver respondido, a mensagem será enfileirada para tentar novamente. A Grade de Eventos usa uma política de repetição de retirada exponencial para a entrega de eventos. A grade de eventos repete a entrega na seguinte agenda com base no melhor esforço:

- 10 segundos
- 30 segundos
- 1 minuto
- 5 minutos
- 10 minutos
- 30 minutos
- 1 hora
- 3 horas
- 6 horas
- A cada 12 horas até 24 horas


Se o ponto de extremidade responder em 3 minutos, a grade de eventos tentará remover o evento da fila de repetição em uma base de melhor esforço, mas as duplicatas ainda poderão ser recebidas.

A grade de eventos adiciona uma pequena aleatoriedade a todas as etapas de repetição e pode ignorar oportunamente determinadas tentativas se um ponto de extremidade estiver consistentemente não íntegro, inativo por um longo período ou parecer sobrecarregado.

Para comportamento determinístico, defina a vida útil do evento e as tentativas de entrega máxima nas [políticas de repetição de assinatura](manage-event-delivery.md).

Por padrão, a Grade de Eventos expira todos os eventos que não são entregues em 24 horas. Você pode [personalizar a política de repetição](manage-event-delivery.md) ao criar uma assinatura de evento. Forneça o número máximo de tentativas de entrega (o padrão é 30) e a vida útil do evento (o padrão é 1440 minutos).

## <a name="delayed-delivery"></a>Entrega atrasada

Como um ponto de extremidade apresenta falhas de entrega, a grade de eventos começará a atrasar a entrega e a repetição de eventos para esse ponto de extremidade. Por exemplo, se os 10 primeiros eventos publicados em um ponto de extremidade falharem, a grade de eventos presumirá que o ponto de extremidade está apresentando problemas e atrasará todas as novas tentativas subsequentes *e novos* entregas por algum tempo, em alguns casos, até várias horas.

A finalidade funcional da entrega atrasada é proteger pontos de extremidade não íntegros, bem como o sistema de grade de eventos. Sem retirada e atraso de entrega para pontos de extremidade não íntegros, a política de repetição da grade de eventos e os recursos de volume podem facilmente sobrecarregar um sistema.

## <a name="dead-letter-events"></a>Eventos de mensagens mortas
Quando a grade de eventos não pode entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um determinado número de vezes, ele pode enviar o evento não entregue a uma conta de armazenamento. Esse processo é conhecido como **mensagens mortas**. A grade de eventos não segue um evento quando **uma das condições a seguir** é atendida. 

- O evento não é entregue dentro do período de **vida útil** . 
- O **número de tentativas** de entregar o evento excedeu o limite.

Se qualquer uma das condições for atendida, o evento será descartado ou inativo.  Por padrão, a Grade de Eventos não ativa o armazenamento de mensagens mortas. Para habilitá-lo, você deve especificar uma conta de armazenamento para reter eventos que não foram entregues ao criar a assinatura do evento. Você aciona eventos dessa conta de armazenamento para resolver as entregas.

A Grade de Eventos enviará um evento ao local de mensagens mortas quando ela tiver tentado todas as suas tentativas de repetição. Se a grade de eventos receber um código de resposta 400 (solicitação inadequada) ou 413 (entidade de solicitação muito grande), ele agendará imediatamente o evento para mensagens mortas. Esses códigos de resposta indicam que a entrega do evento nunca terá êxito.

A expiração de vida útil é verificada apenas na próxima tentativa de entrega agendada. Portanto, mesmo se o tempo de vida expirar antes da próxima tentativa de entrega agendada, a expiração do evento será verificada somente no momento da próxima entrega e, em seguida, mensagens mortas em seguida. 

Há um atraso de cinco minutos entre a última tentativa de entregar de um evento e quando ela é entregue para o local de inatividade. Esse atraso tem como objetivo reduzir o número de operações de armazenamento Blob. Se o local de mensagens mortas não estiver disponível por quatro horas, o evento será descartado.

Antes de configurar o local de mensagens mortas, você deve ter uma conta de armazenamento com um contêiner. É possível fornecer o ponto de extremidade para esse contêiner ao criar a assinatura do evento. O ponto de extremidade está no formato de: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Talvez você queira ser notificado quando um evento tiver sido enviado para o local de mensagens mortas. Para usar a Grade de Eventos para responder a eventos não entregues, [crie uma assinatura de evento](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) para o armazenamento de blob de mensagens mortas. Toda vez que seu armazenamento de blob de mensagens mortas recebe um evento não entregue, a Grade de Eventos notifica o manipulador. O manipulador responde com ações que você deseja executar para reconciliar eventos não entregues. Para obter um exemplo de como configurar um local de mensagens mortas e tentar políticas de repetição, veja [políticas de inatividade e repetição](manage-event-delivery.md).

## <a name="delivery-event-formats"></a>Formatos de evento de entrega
Esta seção fornece exemplos de eventos e eventos inativos em formatos de esquema de entrega diferentes (esquema de grade de eventos, esquema CloudEvents 1,0 e esquema personalizado). Para obter mais informações sobre esses formatos, consulte artigos sobre esquema de [grade de eventos](event-schema.md) e [eventos de nuvem 1,0](cloud-event-schema.md) . 

### <a name="event-grid-schema"></a>Esquema da Grade de Eventos

#### <a name="event"></a>Evento 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Evento de mensagens mortas

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>Esquema CloudEvents 1,0

#### <a name="event"></a>Evento

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Evento de mensagens mortas

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Esquema personalizado

#### <a name="event"></a>Evento

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Evento de mensagens mortas
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>Status de entrega da mensagem

A Grade de Eventos usa códigos de resposta HTTP para confirmar o recebimento de eventos. 

### <a name="success-codes"></a>Códigos de sucesso

A grade de eventos considera **apenas** os seguintes códigos de resposta http como entregas bem-sucedidas. Todos os outros códigos de status são considerados entregas com falha e serão repetidos ou mortodos conforme apropriado. Após receber um código de status bem-sucedido, a grade de eventos considera a entrega concluída.

- 200 OK
- 201 Criado
- 202 Aceito
- 203 informações não autoritativas
- 204 Sem Conteúdo

### <a name="failure-codes"></a>Códigos de falha

Todos os outros códigos que não estão no conjunto acima (200-204) são considerados falhas e serão repetidos (se necessário). Alguns têm políticas de repetição específicas ligadas a eles descritos abaixo, todos os outros seguem o modelo de retirada exponencial padrão. É importante ter em mente que, devido à natureza altamente paralelizada da arquitetura da grade de eventos, o comportamento de repetição é não determinístico. 

| Código de status | Tentar comportamento novamente |
| ------------|----------------|
| 400 Solicitação Inválida | Não repetido |
| 401 Não Autorizado | Tente novamente após 5 minutos ou mais para os pontos de extremidade de recursos do Azure |
| 403 Proibido | Não repetido |
| 404 Não Encontrado | Tente novamente após 5 minutos ou mais para os pontos de extremidade de recursos do Azure |
| 408 Tempo Limite da Solicitação | Tentar novamente após 2 minutos ou mais |
| Solicitação 413 entidade muito grande | Não repetido |
| 503 Serviço Indisponível | Tentar novamente após 30 segundos ou mais |
| Todos os outros | Tentar novamente após 10 segundos ou mais |


## <a name="next-steps"></a>Próximas etapas

* Para exibir o status de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para personalizar as opções de entrega de eventos, confira [Dead letter and retry policies](manage-event-delivery.md) (Políticas de mensagens mortas e repetição).
