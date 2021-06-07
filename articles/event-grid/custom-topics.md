---
title: Tópicos personalizados na grade de eventos do Azure
description: Descreve os tópicos personalizados na grade de eventos do Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86113778"
---
# <a name="custom-topics-in-azure-event-grid"></a>Tópicos personalizados na grade de eventos do Azure
Um tópico da grade de eventos fornece um ponto de extremidade onde a origem envia eventos. O editor cria o tópico de grade de eventos e decide se uma origem do evento precisa de um tópico ou mais de um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para reagir a determinados tipos de evento, os assinantes decidem quais tópicos assinar.

Os **Tópicos personalizados** são aplicativos e tópicos de terceiros. Ao criar ou receber acesso a um tópico personalizado, você verá o tópico personalizado em sua assinatura. 

Ao projetar o seu aplicativo, você terá flexibilidade ao decidir sobre quantos tópicos criar. Para soluções grandes, crie um **tópico personalizado** para **cada categoria de eventos relacionados**. Por exemplo, considere um aplicativo que envia eventos relacionados à modificação de contas de usuário e ordens de processamento. É improvável que qualquer manipulador de eventos queira ambas as categorias de eventos. Crie dois tópicos personalizados e permita que os manipuladores de eventos assinem o que for interessante para eles. Para soluções pequenas, você pode preferir enviar todos os eventos para um único tópico. Assinantes de evento podem filtrar par os tipos de evento que desejam.

## <a name="event-schema"></a>Esquema do evento
Para obter uma visão geral detalhada do esquema de evento, consulte [esquema de evento da grade de eventos do Azure](event-schema.md). Para tópicos personalizados, o editor de eventos determina o objeto de **dados** . Os dados de nível superior devem ter os mesmos campos do que os eventos definidos pelo recurso padrão.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

As seções a seguir fornecem links para tutoriais para criar tópicos personalizados usando os modelos portal do Azure, CLI, PowerShell e Azure Resource Manager (ARM). 


## <a name="azure-portal-tutorials"></a>Tutoriais de portal do Azure
|Título  |Descrição  |
|---------|---------|
| [Início Rápido: como criar e encaminhar eventos personalizados com o portal do Azure](custom-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos personalizados. |
| [Início Rápido: como encaminhar eventos personalizados para o Armazenamento de Filas do Azure](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um Armazenamento de filas. |
| [Como publicar para tópico personalizado](post-to-custom-topic.md) | Mostra como publicar um evento para um tópico personalizado. |


## <a name="azure-cli-tutorials"></a>Tutoriais de CLI do Azure
|Título  |Descrição  |
|---------|---------|
| [Início Rápido: como criar e encaminhar eventos personalizados com a CLI do Azure](custom-event-quickstart.md) | Mostra como usar a CLI do Azure para enviar eventos personalizados. |
| [CLI do Azure: criar um tópico personalizado da Grade de Eventos](./scripts/event-grid-cli-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script recupera o ponto de extremidade e uma chave.|
| [CLI do Azure: assinar eventos para um tópico personalizado](./scripts/event-grid-cli-subscribe-custom-topic.md)|Script de exemplo que cria uma assinatura para um tópico personalizado. Envia eventos para um WebHook.|

## <a name="azure-powershell-tutorials"></a>Tutoriais de Azure PowerShell
|Título  |Descrição  |
|---------|---------|
| [Início Rápido: como criar e encaminhar eventos personalizados com o Azure PowerShell](custom-event-quickstart-powershell.md) | Mostra como usar o Azure PowerShell para enviar eventos personalizados. |
| [PowerShell: criar um tópico de Grade de Eventos personalizado](./scripts/event-grid-powershell-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script recupera o ponto de extremidade e uma chave.|
| [PowerShell: assinar eventos para um tópico personalizado](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Script de exemplo que cria uma assinatura para um tópico personalizado. Envia eventos para um WebHook.|

## <a name="arm-template-tutorials"></a>Tutoriais do modelo ARM
|Título  |Descrição  |
|---------|---------|
| [Modelo do Gerenciador de Recursos: tópico personalizado e ponto de extremidade de WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Um modelo do Gerenciador de Recursos que cria um tópico personalizado e uma assinatura para esse tópico personalizado. Envia eventos para um WebHook. |
| [Modelo do Gerenciador de Recursos: tópico personalizado e ponto de extremidade de Hubs de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo do Gerenciador de Recursos que cria uma assinatura para um tópico personalizado. Envia eventos para os Hubs de Eventos do Azure. |

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 

- [Tópicos do sistema](system-topics.md)
- [Domínios](event-domains.md)