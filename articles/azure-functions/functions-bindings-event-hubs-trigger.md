---
title: Gatilho de hubs de eventos do Azure para Azure Functions
description: Aprenda a usar o gatilho de hubs de eventos do Azure no Azure Functions.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608897"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Gatilho de hubs de eventos do Azure para Azure Functions

Este artigo explica como trabalhar com o gatilho de [hubs de eventos do Azure](../event-hubs/event-hubs-about.md) para Azure functions. O Azure Functions dá suporte a associações de gatilho e [saída](functions-bindings-event-hubs-output.md) para hubs de eventos.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](functions-bindings-event-hubs.md).

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>configurações de host.json

O [host.jsno](functions-host-json.md#eventhub) arquivo contém configurações que controlam o comportamento do gatilho do hub de eventos. Consulte a [host.jsna seção Configurações](functions-bindings-event-hubs.md#hostjson-settings) para obter detalhes sobre as configurações disponíveis.

## <a name="next-steps"></a>Próximas etapas

- [Gravar eventos em um fluxo de eventos (Associação de saída)](./functions-bindings-event-hubs-output.md)
