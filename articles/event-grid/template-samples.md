---
title: Exemplos de modelo do Azure Resource Manager – Grade de Eventos | Microsoft Docs
description: Este artigo fornece uma lista de amostras de modelo do Azure Resource Manager para a Grade de Eventos do Azure no GitHub.
ms.topic: sample
ms.date: 07/07/2020
ms.openlocfilehash: 910012adf2dc930e6f1a26f1a7fc41f5ed0580c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86119048"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelos do Azure Resource Manager para Grade de Eventos

Para obter a sintaxe JSON e as propriedades a serem usadas em um modelo, confira [Tipos de recurso de Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). A tabela a seguir contém links para modelos do Azure Resource Manager para Grade de Eventos.

## <a name="event-grid-subscriptions"></a>Assinaturas da Grade de Eventos
- [Tópico personalizado e assinatura com o ponto de extremidade do webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid): implanta um tópico personalizado da Grade de Eventos. Cria uma assinatura para esse tópico personalizado que usa um ponto de extremidade do WebHook. 
- [Assinatura de tópico personalizado com o ponto de extremidade do Hub de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler): cria uma assinatura da Grade de Eventos para um tópico personalizado. A assinatura usa um Hub de Evento para o ponto de extremidade. 
- [Assinatura do Azure ou assinatura de grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook): inscreve-se nos eventos de um grupo de recursos ou de uma assinatura do Azure. O grupo de recursos que você especificar como o de destino durante a implantação é a origem de eventos. A assinatura usa um WebHook para o ponto de extremidade. 
- [Conta de armazenamento de blobs e assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage): implanta uma conta de armazenamento de blobs do Azure e inscreve-se nos eventos dessa conta de armazenamento. 

## <a name="next-steps"></a>Próximas etapas
Confira as seguintes amostras:

- [Exemplos do PowerShell](powershell-samples.md)
- [Exemplos de CLI](cli-samples.md)
