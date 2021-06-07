---
title: Entrega e repetição da Grade de Eventos do Azure
description: Descreve como a Grade de Eventos do Azure entrega eventos e como ela trata mensagens não entregues.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: e24b7540ea1ac41774e2c23781265f9a61940cb1
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276732"
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega e repetição de mensagens da Grade de Eventos

Este artigo descreve como a Grade de Eventos do Azure manipula eventos quando a entrega não é confirmada.

A entrega proporcionada pela Grade de Eventos tem um tempo de duração. Cada mensagem é entregue **pelo menos uma vez** para cada assinatura. Os eventos são enviados para o ponto de extremidade registrado de cada assinatura imediatamente. Se um ponto de extremidade não confirmar o recebimento de um evento, a Grade de Eventos tentará entregá-lo novamente.

> [!NOTE]
> A Grade de Eventos não garante a ordem de entrega de eventos, portanto, os assinantes podem recebê-los fora de ordem. 

## <a name="batched-event-delivery"></a>Entrega de eventos em lote

A Grade de Eventos usa como padrão o envio individual de cada evento aos assinantes. O assinante recebe uma matriz com um único evento. Você pode configurar a Grade de Eventos para eventos em lote para entrega de um desempenho de HTTP aprimorado em cenários de alta taxa de transferência.

A entrega em lote tem duas configurações:

* **Máximo de eventos por lote** – Número máximo de eventos que a Grade de Eventos entregará por lote. Esse número nunca será excedido, mas menos eventos poderão ser entregues se nenhum outro evento estiver disponível no momento da publicação. A Grade de Eventos não atrasará eventos de criação de lote se menos eventos estiverem disponíveis. Esse valor precisa estar entre 1 e 5.000.
* **Tamanho de lote preferencial em quilobytes** – O limite de destino do tamanho do lote em quilobytes. Semelhante ao máximo de eventos, o tamanho do lote poderá ser menor se mais eventos não estiverem disponíveis no momento da publicação. É possível que um lote seja maior do que o tamanho de lote preferencial *se* um evento for maior do que o tamanho preferencial. Por exemplo, se o tamanho preferencial for 4 KB e um evento de 10 KB for enviado por push para a Grade de Eventos, o evento de 10 KB ainda será entregue no próprio lote, em vez de ser removido.

A entrega em lote é configurada em uma assinatura por evento por meio do portal, da CLI, do PowerShell ou de SDKs.

### <a name="azure-portal"></a>Portal do Azure: 
![Configurações de entrega em lote](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>CLI do Azure
Ao criar uma assinatura de evento, use os seguintes parâmetros: 

- **max-events-per-batch** – Número máximo de eventos em um lote. O valor precisa ser um número entre 1 e 5000.
- **preferred-batch-size-in-kilobytes** – Tamanho de lote preferencial em quilobytes. O valor precisa ser um número entre 1 e 1024.

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

Para obter mais informações sobre como usar a CLI do Azure com a Grade de Eventos, confira [Rotear eventos de armazenamento para o ponto de extremidade da Web com a CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Agendamento de nova tentativa e duração

Quando o EventGrid recebe um erro de uma tentativa de entrega de evento, ele decide se deve tentar novamente a entrega, armazena o evento como mensagem morta ou o remove de acordo com o tipo de erro. 

Se o erro retornado pelo ponto de extremidade assinado for um erro relacionado à configuração que não pode ser corrigido com novas tentativas (por exemplo, se o ponto de extremidade for excluído), o EventGrid armazena o evento como mensagem morta ou removerá o evento se o armazenamento de mensagens mortas não estiver configurado.

A seguinte tabela descreve os tipos de pontos de extremidade e erros para os quais não acontecem novas tentativas:

| Tipo de Ponto de Extremidade | Códigos do Erro |
| --------------| -----------|
| Recursos do Azure | 400 Solicitação Inválida, 413 Entidade de Solicitação Muito Grande, 403 Proibido | 
| webhook | 400 Solicitação Inválida, 413 Entidade de Solicitação Muito Grande, 403 Proibido, 404 Não Localizado, 401 Não Autorizado |
 
> [!NOTE]
> Se o Armazenamento de Mensagens Mortas não estiver configurado para um ponto de extremidade, os eventos serão removidos quando os erros acima ocorrerem. Considere configurar o Armazenamento de Mensagens Mortas se você não quiser que esses tipos de eventos sejam removidos.

Se o erro retornado pelo ponto de extremidade assinado não estiver na lista acima, o EventGrid executará a nova tentativa usando as políticas descritas abaixo:

A Grade de Eventos aguarda 30 segundos por uma resposta depois de entregar uma mensagem. Após 30 segundos, se o ponto de extremidade não tiver respondido, a mensagem será enfileirada para uma nova tentativa. A Grade de Eventos usa uma política de repetição de retirada exponencial para a entrega de eventos. A Grade de Eventos repete a entrega usando a seguinte agenda com base no melhor esforço:

- 10 segundos
- 30 segundos
- 1 minuto
- 5 minutos
- 10 minutos
- 30 minutos
- 1 hora
- 3 horas
- 6 horas
- A cada 12 horas a 24 horas


Se o ponto de extremidade responder em três minutos, a Grade de Eventos tentará remover o evento da fila de nova tentativa em uma base de melhor esforço, mas as duplicatas ainda poderão ser recebidas.

A Grade de Eventos adiciona uma pequena aleatoriedade a todas as etapas de repetição e pode ignorar oportunamente determinadas tentativas se um ponto de extremidade estiver não íntegro de modo consistente, inativo por um longo período ou parecer sobrecarregado.

Para o comportamento determinístico, defina as tentativas de entrega máxima e a vida útil do evento nas [políticas de repetição de assinatura](manage-event-delivery.md).

Por padrão, a Grade de Eventos expira todos os eventos que não são entregues em 24 horas. Você pode [personalizar a política de repetição](manage-event-delivery.md) ao criar uma assinatura de evento. Forneça o número máximo de tentativas de entrega (o padrão é 30) e a vida útil do evento (o padrão é 1440 minutos).

## <a name="delayed-delivery"></a>Entrega atrasada

Como um ponto de extremidade apresenta falhas de entrega, a Grade de Eventos começará a atrasar a entrega e a nova tentativa de eventos nesse ponto de extremidade. Por exemplo, se os dez primeiros eventos publicados em um ponto de extremidade falharem, a Grade de Eventos presumirá que o ponto de extremidade está apresentando problemas e atrasará todas as novas tentativas subsequentes *e novas* entregas por algum tempo, em alguns casos, por até várias horas.

A finalidade funcional da entrega atrasada é proteger os pontos de extremidade não íntegros e o sistema de Grade de Eventos. Sem retirada e atraso de entrega para pontos de extremidade não íntegros, a política de repetição da Grade de Eventos e as capacidades de volume podem facilmente sobrecarregar um sistema.

## <a name="dead-letter-events"></a>Eventos de mensagens mortas
Quando a Grade de Eventos não puder entregar um evento dentro de um determinado período ou depois de tentar entregar o evento um determinado número de vezes, ela poderá enviar o evento não entregue a uma conta de armazenamento. Esse processo é conhecido como **armazenamento de mensagens mortas**. A Grade de Eventos armazena mensagens mortas de um evento quando **uma das condições a seguir** é atendida. 

- O evento não é entregue dentro do período de **vida útil**. 
- O **número de tentativas** de entrega do evento excedeu o limite.

Se qualquer uma das condições for atendida, o evento será removido ou armazenado como mensagem morta.  Por padrão, a Grade de Eventos não ativa o armazenamento de mensagens mortas. Para habilitá-lo, você deve especificar uma conta de armazenamento para reter eventos que não foram entregues ao criar a assinatura do evento. Você aciona eventos dessa conta de armazenamento para resolver as entregas.

A Grade de Eventos enviará um evento ao local de mensagens mortas quando ela tiver tentado todas as suas tentativas de repetição. Se a Grade de Eventos receber um código de resposta 400 (Solicitação Inválida) ou 413 (Entidade de Solicitação Muito Grande), ela agendará imediatamente o evento para o armazenamento de mensagens mortas. Esses códigos de resposta indicam que a entrega do evento nunca terá êxito.

O término da vida útil é verificado APENAS na próxima tentativa de entrega agendada. Portanto, mesmo se a vida útil expirar antes da próxima tentativa de entrega agendada, a expiração do evento será verificada somente no momento da próxima entrega e o armazenamento de mensagens mortas será realizado em seguida. 

Há um atraso de cinco minutos entre a última tentativa de entregar de um evento e quando ela é entregue para o local de inatividade. Esse atraso tem como objetivo reduzir o número de operações de Armazenamento de Blobs. Se o local de mensagens mortas não estiver disponível por quatro horas, o evento será descartado.

Antes de configurar o local de mensagens mortas, você deve ter uma conta de armazenamento com um contêiner. É possível fornecer o ponto de extremidade para esse contêiner ao criar a assinatura do evento. O ponto de extremidade está no formato de: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Talvez você queira ser notificado quando um evento tiver sido enviado para a localização das mensagens mortas. Para usar a Grade de Eventos para responder a eventos não entregues, [crie uma assinatura de evento](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) para o armazenamento de blob de mensagens mortas. Toda vez que seu armazenamento de blob de mensagens mortas recebe um evento não entregue, a Grade de Eventos notifica o manipulador. O manipulador responde com ações que você deseja executar para reconciliar eventos não entregues. Para ver um exemplo de como configurar uma localização de mensagens mortas e políticas de repetição, confira [Mensagens mortas e políticas de repetição](manage-event-delivery.md).

## <a name="delivery-event-formats"></a>Formatos de evento de entrega
Esta seção fornece exemplos de eventos e eventos de mensagens mortas em formatos de esquema de entrega diferentes (esquema de Grade de Eventos, esquema do CloudEvents 1.0 e esquema personalizado). Para obter mais informações sobre esses formatos, confira os artigos [Esquema da Grade de Eventos](event-schema.md) e [Esquema do Cloud Events 1.0](cloud-event-schema.md). 

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

### <a name="cloudevents-10-schema"></a>Esquema do CloudEvents 1.0

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

A Grade de Eventos considera **apenas** os códigos de resposta HTTP a seguir como entregas bem-sucedidas. Todos os outros códigos de status são considerados entregas com falha e serão repetidos ou armazenados como mensagens mortas, conforme apropriado. Após receber um código de status bem-sucedido, a Grade de Eventos considera a entrega concluída.

- 200 OK
- 201 Criado
- 202 Aceito
- 203 Informações Não Autoritativas
- 204 Sem Conteúdo

### <a name="failure-codes"></a>Códigos de falha

Todos os outros códigos que não estão no conjunto acima (200 a 204) são considerados falhas e serão repetidos (se necessário). Alguns têm políticas de repetição específicas ligadas a eles descritas abaixo e todos os outros seguem o modelo de retirada exponencial padrão. É importante ter em mente que, devido à natureza altamente paralelizada da arquitetura da Grade de Eventos, o comportamento de repetição é não determinístico. 

| Código de status | Tentar comportamento novamente |
| ------------|----------------|
| 400 Solicitação Inválida | Não tentar novamente |
| 401 Não Autorizado | Tentar novamente após cinco minutos ou mais para os Pontos de Extremidade de Recursos do Azure |
| 403 Proibido | Não tentar novamente |
| 404 Não Encontrado | Tentar novamente após cinco minutos ou mais para os Pontos de Extremidade de Recursos do Azure |
| 408 Tempo Limite da Solicitação | Tentar novamente após dois minutos ou mais |
| Solicitação 413 entidade muito grande | Não tentar novamente |
| 503 Serviço Indisponível | Tentar novamente após 30 segundos ou mais |
| Todos os outros | Tentar novamente após dez segundos ou mais |

## <a name="custom-delivery-properties"></a>Propriedades de entrega personalizadas
As assinaturas de evento permitem que você configure cabeçalhos HTTP que estão incluídos nos eventos entregues. Essa funcionalidade permite que você defina cabeçalhos personalizados que são exigidos por um destino. Você pode configurar até dez cabeçalhos ao criar uma assinatura de evento. Cada valor de cabeçalho não deve ser maior que 4.096 (4K) bytes. Você pode definir os cabeçalhos personalizados nos eventos que são entregues aos seguintes destinos:

- Webhooks
- Tópicos e filas do Barramento de Serviço do Azure
- Hubs de eventos do Azure
- Conexões híbridas de retransmissão

Para obter mais informações, confira [Propriedades de entrega personalizadas](delivery-properties.md). 

## <a name="next-steps"></a>Próximas etapas

* Para exibir o status de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para personalizar as opções de entrega de eventos, confira [Dead letter and retry policies](manage-event-delivery.md) (Políticas de mensagens mortas e repetição).
