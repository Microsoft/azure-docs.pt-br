---
title: CLI do Azure – assinar o grupo de recursos e filtrar por recurso
description: Este artigo fornece um script de exemplo da CLI do Azure que mostra como assinar eventos da Grade de Eventos para um recurso e um filtro para um recurso.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a4343f4fb7791459cc3dc6e7db34433d8b7b60f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766633"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-azure-cli"></a>Assinar eventos de um grupo de recursos e filtrar por um recurso com a CLI do Azure

Este script cria uma assinatura de Grade de Eventos para os eventos para um grupo de recursos. Ele usa um filtro para obter apenas os eventos de um recurso específico do grupo de recursos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

O script de exemplo de visualização requer a extensão de Grade de Eventos. Para instalar, execute `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Exemplo de script - estável

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events/filter-events.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Exemplo de script - extensão de visualização

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events-preview/filter-events-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o seguinte comando para criar a assinatura do evento. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Criar uma assinatura na Grade de Eventos. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - versão de extensão | Criar uma assinatura na Grade de Eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre como consultar as assinaturas, confira [Assinaturas da Grade de Eventos de Consulta](../query-event-subscriptions.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
