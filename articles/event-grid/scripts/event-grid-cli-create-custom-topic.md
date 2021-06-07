---
title: Exemplo de script da CLI do Azure – Criar um tópico personalizado | Microsoft Docs
description: Este artigo fornece um script de exemplo da CLI do Azure que mostra como criar um tópico personalizado da Grade de Eventos do Azure.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4e381310db5a21e532b262063ee33de3a23edeb2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766727"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Criar um tópico personalizado da Grade de Eventos com a CLI do Azure

Este script cria um tópico personalizado da Grade de Eventos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o seguinte comando para criar o tópico personalizado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az eventgrid topic create](/cli/azure/eventgrid/topic#az_eventgrid_topic_create) | Este script cria um tópico personalizado da Grade de Eventos. |


## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre como consultar as assinaturas, confira [Assinaturas da Grade de Eventos de Consulta](../query-event-subscriptions.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
