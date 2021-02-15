---
title: Grade de Eventos do Azure – Solucionar problemas de validação de assinatura
description: Este artigo mostra como você pode solucionar problemas de validação de assinatura.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592933"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Solucionar problemas de validações de assinatura da Grade de Eventos do Azure
Durante a criação da assinatura do evento, se você estiver vendo uma mensagem de erro como `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` , isso indica que há uma falha no handshake de validação. Para resolver esse erro, verifique os seguintes aspectos:

- Faça um HTTP POST para a URL do webhook com um corpo de solicitação [SubscriptionValidationEvent de exemplo](webhook-event-delivery.md#validation-details) usando o postmaster ou a ondulação ou ferramenta semelhante.
- Se o webhook estiver implementando o mecanismo de handshake de validação síncrona, verifique se o ValidationCode é retornado como parte da resposta.
- Se seu webhook estiver implementando o mecanismo de handshake de validação assíncrona, verifique se você é o HTTP POST está retornando 200 OK.
- Se o webhook estiver retornando `403 (Forbidden)` na resposta, verifique se o webhook está atrás de um gateway de aplicativo Azure ou firewall do aplicativo Web. Se for, você precisará desabilitar essas regras de firewall e executar um HTTP POST novamente:
    - 920300 (solicitação faltando um cabeçalho de aceitação)
    - 942430 (detecção de anomalias de caracteres SQL restritos (args): número de caracteres especiais excedido (12))
    - 920230 (várias codificações de URL detectadas)
    - 942130 (ataque de injeção de SQL: SQL tautology detectado.)
    - 931130 (possível ataque de RFI (inclusão de arquivo remoto) = link/referência fora do domínio)

> [!IMPORTANT]
> Para informações detalhadas sobre a validação de ponto de extremidade para webhooks, confira [Entrega de eventos de webhook](webhook-event-delivery.md).

As seções a seguir mostram como validar uma assinatura de evento usando o postmaster e a ondulação.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Validar a assinatura de evento da Grade de Eventos com o Postman
Confira um exemplo de como usar o Postman para validar uma assinatura de webhook de um evento da Grade de Eventos: 

![Validação de assinatura de evento da Grade de Eventos com o Postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Confira aqui o exemplo JSON **SubscriptionValidationEvent** :

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Este é um exemplo de resposta bem-sucedida:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Para saber mais sobre a validação de evento da Grade de Eventos para webhooks, confira [Validação de ponto de extremidade com eventos da Grade de Eventos](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Validar a assinatura de evento da Grade de Eventos com Curl 
Aqui está um exemplo do comando Curl para validar uma assinatura de webhook em um evento da Grade de Eventos: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Validar a assinatura de evento em nuvem com o Postman
Confira um exemplo de como usar o Postman para validar uma assinatura de webhook de um evento em nuvem: 

![Validação de assinatura de evento em nuvem com o Postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Use o método **HTTP OPTIONS** para a validação com eventos em nuvem. Para saber mais sobre a validação de eventos em nuvem para webhooks, confira [Validação de ponto de extremidade com eventos em nuvem](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="troubleshoot-event-subscription-validation"></a>Solucionar problemas de validação de assinatura de evento

## <a name="next-steps"></a>Próximas etapas
Se precisar de mais ajuda, poste seu problema no [fórum do Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/). 
