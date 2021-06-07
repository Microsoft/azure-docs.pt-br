---
title: Publicar evento para tópico personalizado de Grade de Eventos do Azure
description: Este artigo descreve como publicar um evento para um tópico personalizado. Ele mostra o formato dos dados de postagem e evento.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ed126487938e524264c94544903460854ffc4d41
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681593"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Publicar para tópico personalizado para Grade de Eventos do Azure

Este artigo descreve como publicar um evento para um tópico personalizado. Ele mostra o formato dos dados de postagem e evento. O [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) só se aplica às publicações que correspondem ao formato esperado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Ponto de extremidade

Ao enviar o HTTP POST para um tópico personalizado, use o formato URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Por exemplo, um URI válido é: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Para obter o ponto de extremidade para um tópico personalizado com CLI do Azure, use:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Para obter o ponto de extremidade para um tópico personalizado com Azure PowerShell, use:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>parâmetro

Na solicitação, inclua um valor de cabeçalho chamado `aeg-sas-key` que contém uma chave para autenticação.

Por exemplo, um valor de cabeçalho válido é `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Para obter a chave para um tópico personalizado com CLI do Azure, use:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Para obter a chave para um tópico personalizado com PowerShell, use:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dados de evento

Para tópicos personalizados, os dados de nível superior contêm os mesmos campos do que os eventos definidos pelo recurso padrão. Uma dessas propriedades é uma propriedade de dados que contém propriedades exclusivas para o tópico personalizado. Como publicador de eventos, você deve determinar as propriedades para esse objeto de dados. Use o esquema a seguir:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Para obter uma descrição dessas propriedades, consulte [esquema de evento de Grade de Eventos do Azure](event-schema.md). Ao postar eventos em um tópico da grade de eventos, a matriz pode ter um tamanho total de até 1 MB. O tamanho máximo permitido para um evento também é de 1 MB. Eventos acima de 64 KB são cobrados em incrementos de 64 KB. 

Por exemplo, um esquema de dados de evento válido é:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Resposta

Após a postagem para o ponto de extremidade do tópico, você receberá uma resposta. A resposta é um código de resposta HTTP padrão. Algumas respostas comuns são:

|Result  |Resposta  |
|---------|---------|
|Sucesso  | 200 OK  |
|Os dados de evento têm formato incorreto | 400 Solicitação Inválida |
|Chave de acesso inválida | 401 Não Autorizado |
|Ponto de extremidade incorreto | 404 Não Encontrado |
|Matriz ou evento excede os limites de tamanho | O conteúdo 413 é muito grande |

Para erros, o corpo da mensagem tem o seguinte formato:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para saber mais sobre a chave de autenticação, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
