---
title: Entrega de eventos do WebHook
description: Este artigo descreve a entrega de eventos do webhook e a validação do ponto de extremidade ao usar webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e9a52d0cb3e4e880d91e1b748d97ef3041298930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87461231"
---
# <a name="webhook-event-delivery"></a>Entrega de eventos do webhook
Webhooks são uma dentre várias maneiras de receber eventos da Grade de Eventos do Azure. Quando um novo evento estiver pronto, o serviço da Grade de Eventos do Azure POSTs uma solicitação HTTP para o ponto de extremidade configurado com o evento o corpo da solicitação.

Como muitos outros serviços que dão suporte a webhooks, a Grade de Eventos do Azure exige que você comprovar a "propriedade" de seu ponto de extremidade do Webhook antes de começar a entrega de eventos para esse ponto de extremidade. Esse requisito impede que um usuário mal-intencionado inunde seu ponto de extremidade com eventos. Quando você usa qualquer um dos três serviços do Azure listado abaixo, a infraestrutura do Azure trata automaticamente essa validação:

- Aplicativos Lógicos do Azure com [Conector da Grade de Eventos](/connectors/azureeventgrid/)
- Automação do Azure por meio de [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions com [Gatilho de Grade de Eventos](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Validação de ponto de extremidade com eventos da Grade de Eventos
Se você estiver usando qualquer outro tipo de ponto de extremidade, como uma função do Azure baseada no gatilho HTTP, o código do ponto de extremidade precisará participar de um handshake de validação com o EventGrid. A Grade de Eventos dá suporte a duas maneiras de validar a assinatura.

1. **Handshake síncrono**: No momento da criação da assinatura, a Grade de Eventos posta um Evento de Validação de Assinatura para o ponto de extremidade de destino. O esquema desse evento é semelhante a qualquer outro evento da Grade de Eventos. A parte de dados desse evento inclui um `validationCode` propriedade. Seu aplicativo verifica se a solicitação de validação é para uma assinatura de evento esperada e retorna o código de validação na resposta de forma síncrona. Esse mecanismo de handshake é compatível com todas as versões da Grade de Eventos.

2. **Handshake assíncrono**: em determinados casos, você não pode retornar o ValidationCode em resposta de forma síncrona. Por exemplo, se você usar um serviço de terceiros (como [`Zapier`](https://zapier.com) ou [IFTTT](https://ifttt.com/)), não será possível responder de volta programaticamente com o código de validação.

   A partir da versão 2018-05-01-preview, a Grade de Eventos dá suporte a um handshake de validação manual. Se você estiver criando uma inscrição de evento com um SDK ou ferramenta que usa a versão da API 2018-05-01-preview ou posterior, a Grade de Eventos envia uma propriedade `validationUrl` na parte de dados do evento de validação da assinatura. Para concluir o handshake, localize essa URL nos dados do evento e faça uma solicitação GET para ele. Você pode usar um cliente REST ou o navegador da web.

   A URL fornecida é válida por **cinco minutos**. Durante esse tempo, o estado de fornecimento da assinatura do evento é `AwaitingManualAction`. Se você não concluir a validação manual em cinco minutos, o estado de provisionamento será definido como `Failed`. Você terá que criar a inscrição do evento novamente antes de iniciar a validação manual.

   Esse mecanismo de autenticação também exige que o ponto de extremidade do webhook retorne um código de status HTTP 200 para que ele saiba que o POST do evento de validação foi aceito antes que ele possa ser colocado no modo de validação manual. Em outras palavras, se o ponto de extremidade retornar 200, mas não retornar uma resposta de validação de forma síncrona, o modo será transferido para o modo de validação manual. Se houver um GET na URL de validação em cinco minutos, o handshake de validação será considerado bem-sucedido.

> [!NOTE]
> Não há suporte para o uso de certificados autoassinados para validação. Em vez disso, use um certificado assinado de uma AC (autoridade de certificação) comercial.

### <a name="validation-details"></a>Detalhes da validação

- No momento da criação/atualização da assinatura, a Grade de Eventos posta um Evento de Validação de Assinatura para o ponto de extremidade de destino.
- O evento contém um valor de cabeçalho "aeg-event-type: SubscriptionValidation".
- O corpo do evento tem o mesmo esquema que outros eventos da Grade de Eventos.
- A propriedade eventType do evento é `Microsoft.EventGrid.SubscriptionValidationEvent`.
- Os dados de propriedade do evento incluem uma propriedade `validationCode` com uma cadeia de caracteres gerada aleatoriamente. Por exemplo, "validationCode: acb13…".
- Os dados do evento também incluem uma propriedade `validationUrl` com uma URL para validar manualmente a assinatura.
- A matriz contém apenas o evento de validação. Outros eventos serão enviados em uma solicitação separada, após retornar o código de validação.
- Os SDKs do plano de dados EventGrid têm classes correspondentes para os dados de evento de validação de assinatura e a resposta de validação de assinatura.

Um SubscriptionValidationEvent de exemplo é mostrado no exemplo a seguir:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Para provar a propriedade do pronto de extremidade, retorne o código de validação na propriedade validationResponse, conforme mostrado no exemplo a seguir:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Você deve retornar um código de status de resposta HTTP 200 Okey. HTTP 202 aceito não é reconhecido como uma resposta de validação de assinatura de Grade de Eventos válida. A solicitação HTTP deve ser concluída dentro de 30 segundos. Se a operação não for concluída dentro de 30 segundos, a operação será cancelada e poderá ser tentada novamente após cinco segundos. Se todas as tentativas falharem, elas serão tratadas como um erro de handshake de validação.

Ou então, você pode validar a assinatura manualmente, enviando uma solicitação GET para a URL de validação. A inscrição do evento permanece em um estado pendente até que validada. A URL de validação usa a porta 553. Se suas regras de firewall bloquearem a porta 553, talvez seja necessário atualizar as regras para obter um handshake manual bem-sucedido.

Para obter um exemplo de lidar com o handshake de validação de assinatura, consulte uma [ amostra C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto de extremidade com CloudEvents v 1.0
Se você já estiver familiarizado com a Grade de Eventos, talvez esteja ciente do handshake de validação do ponto de extremidade da Grade de Eventos para evitar abusos. O CloudEvents v 1.0 implementa sua própria [semântica de proteção contra abuso](webhook-event-delivery.md) usando o método de opções HTTP. Saiba mais sobre isso [aqui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ao usar o esquema CloudEvents para saída, a Grade de Eventos usa com a proteção de abuso do CloudEvents v 1.0 em vez do mecanismo de evento de validação da Grade de Eventos.

## <a name="next-steps"></a>Próximas etapas
Confira o artigo a seguir para saber como solucionar problemas de validações de assinatura de evento: 

[Solucionar problemas de validações de assinatura de evento](troubleshoot-subscription-validation.md)
