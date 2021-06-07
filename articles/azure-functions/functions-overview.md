---
title: Visão geral do Azure Functions
description: Saiba como o Azure Functions pode ajudar a criar aplicativos sem servidor robustos.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperf-fy21q2
ms.openlocfilehash: 41b627259d84539c868f95eb3cf33db5dbdab52c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97862400"
---
# <a name="introduction-to-azure-functions"></a>Introdução ao Azure Functions

O Azure Functions é uma solução sem servidor que permite que você escreva menos código, mantenha menos infraestrutura e economize nos custos. Em vez de se preocupar com a implantação e manutenção de servidores, a infraestrutura de nuvem fornece todos os recursos atualizados necessários para manter seus aplicativos em execução.

Você se concentra nas partes do código mais importantes para você e o Azure Functions lida com o restante.<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

Muitas vezes, criamos sistemas para reagir a uma série de eventos críticos. Se você estiver criando uma API Web, respondendo a alterações no banco de dados, processando fluxos IoT ou até mesmo gerenciando filas de mensagens, cada aplicativo precisa ter uma forma de executar algum código à medida que esses eventos ocorrem.

Para atender a essa necessidade, o Azure Functions fornece "computação sob demanda" de duas maneiras significativas.

Primeiro, o Azure Functions permite que você implemente a lógica do seu sistema em blocos de código prontamente disponíveis. Esses blocos de código são denominados "funções". Funções diferentes poderão ser executadas sempre que você precisar responder a eventos críticos.

Em segundo lugar, à medida que as solicitações aumentam, o Azure Functions atende à demanda com tantos recursos e instâncias de função quanto necessário, mas somente enquanto for necessário. Conforme as solicitações caírem, todos os recursos adicionais e instâncias de aplicativo cairão automaticamente.

De onde vêm todos os recursos de computação? O Azure Functions [fornece o máximo ou o mínimo necessário de recursos de computação](./functions-scale.md) para atender à demanda do seu aplicativo.

Fornecer recursos de computação sob demanda é o cerne da [computação sem servidor](https://azure.microsoft.com/solutions/serverless/) no Azure Functions.

## <a name="scenarios"></a>Cenários

Em muitos casos, uma função [integra-se a uma matriz de serviços de nuvem](./functions-triggers-bindings.md) para fornecer implementações ricas em recursos.

Veja a seguir um conjunto comum, _mas não exaustivo_, de cenários para o Azure Functions.

| Se desejar... | então… |
| --- | --- |
| **Criar uma API da Web** | Implemente um ponto de extremidade para seus aplicativos Web usando o [gatilho HTTP](./functions-bindings-http-webhook.md) |
| **Processar uploads de arquivo** | Execute o código quando um arquivo for carregado ou alterado no [armazenamento de blobs](./functions-bindings-storage-blob.md) |
| **Criar um fluxo de trabalho sem servidor** | Encadeie uma série de funções usando [funções duráveis](./durable/durable-functions-overview.md) |
| **Responder a alterações no banco de dados** | Execute a lógica personalizada quando um documento for criado ou atualizado no [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Executar tarefas agendadas** | Execute o código em [horários definidos](./functions-bindings-timer.md) |
| **Criar sistemas de fila de mensagens confiáveis** | Processe filas de mensagens usando o [Armazenamento de Filas](./functions-bindings-storage-queue.md), o [Barramento de Serviço](./functions-bindings-service-bus.md) ou os [Hubs de Eventos](./functions-bindings-event-hubs.md) |
| **Analisar fluxos de dados de IoT** | Colete e processe [dados de dispositivos IoT](./functions-bindings-event-iot.md) |
| **Processar dados em tempo real** | Use o [Functions e SignalR](./functions-bindings-signalr-service.md) para responder aos dados no momento |

Ao criar suas funções, você tem as seguintes opções e recursos disponíveis:

- **Usar a linguagem de sua preferência**: escreva funções em [C#, Java, JavaScript, PowerShell ou Python](./supported-languages.md) ou use um [manipulador personalizado](./functions-custom-handlers.md) para usar praticamente qualquer outra linguagem.

- **Automatizar a implantação**: desde uma abordagem baseada em ferramentas até o uso de pipelines externos, há uma [infinidade de opções de implantação](./functions-deployment-technologies.md) disponível.

- **Solucionar problemas de uma função**: use [ferramentas de monitoramento](./functions-monitoring.md) e [estratégias de teste](./functions-test-a-function.md) para obter informações sobre seus aplicativos.

- **Opções de preços flexíveis**: com o plano de [Consumo](./pricing.md), você só pagará enquanto suas funções estiverem em execução, enquanto os planos [Premium](./pricing.md) e do [Serviço de Aplicativo](./pricing.md) oferecerem recursos para necessidades especializadas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Comece a usar por meio de lições, exemplos e tutoriais interativos](./functions-get-started.md)
