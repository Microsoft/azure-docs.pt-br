---
title: Gatilho do Hub IoT do Azure para Azure Functions
description: Saiba como responder a eventos enviados a um fluxo de eventos do Hub IoT no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612279"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Gatilho do Hub IoT do Azure para Azure Functions

Este artigo explica como trabalhar com associações de Azure Functions para o Hub IoT. O suporte ao Hub IoT é baseado na [Associação de hubs de eventos do Azure](functions-bindings-event-hubs.md).

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Embora os exemplos de código a seguir usem a API do hub de eventos, a sintaxe fornecida é aplicável a funções do Hub IoT.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>Propriedades de host.json

O [host.jsno](functions-host-json.md#eventhub) arquivo contém configurações que controlam o comportamento do gatilho do hub de eventos. Consulte a [host.jsna seção Configurações](functions-bindings-event-iot.md#hostjson-settings) para obter detalhes sobre as configurações disponíveis.

## <a name="next-steps"></a>Próximas etapas

- [Gravar eventos em um fluxo de eventos (Associação de saída)](./functions-bindings-event-iot-output.md)
