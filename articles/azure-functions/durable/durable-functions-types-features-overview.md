---
title: Tipos de função no Azure Durable Functions
description: Saiba mais sobre os tipos de funções e funções que dão suporte à comunicação de função a função em uma orquestração de Durable Functions no Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84697700"
---
# <a name="durable-functions-types-and-features"></a>Tipos de Durable Functions e recursos

As Durable Functions são uma extensão do [Azure Functions](../functions-overview.md). Você pode usar Durable Functions para orquestração com estado da execução da função. Um aplicativo de funções durável é uma solução composta por diferentes funções do Azure. As funções podem reproduzir funções diferentes em uma orquestração de função durável. 

Atualmente, há quatro tipos de função duráveis em Azure Functions: Activity, Orchestrator, Entity e Client. O restante desta seção apresenta mais detalhes sobre os tipos de funções envolvidas em uma orquestração.

## <a name="orchestrator-functions"></a>Funções de orquestrador

As funções de orquestrador descrevem como as ações são executadas e a ordem na qual as ações são executadas. As funções de orquestrador descrevem a orquestração no código (C# ou JavaScript), conforme mostrado em [padrões de aplicativo Durable Functions](durable-functions-overview.md#application-patterns). Uma orquestração pode ter muitos tipos diferentes de ações, incluindo [funções de atividade](#activity-functions), [suborquestrações](durable-functions-orchestrations.md#sub-orchestrations), [aguardando eventos externos](durable-functions-orchestrations.md#external-events), [http](durable-functions-http-features.md)e [temporizadores](durable-functions-orchestrations.md#durable-timers). As funções de orquestrador também podem interagir com [funções de entidade](#entity-functions).

> [!NOTE]
> As funções de orquestrador são escritas usando código comum, mas há requisitos estritos de como escrever o código. Especificamente, o código de função do orquestrador deve ser *determinístico*. A falha ao seguir esses requisitos de determinantes pode fazer com que as funções de orquestrador falhem ao executar corretamente. Informações detalhadas sobre esses requisitos e como contornar isso podem ser encontradas no tópico [restrições de código](durable-functions-code-constraints.md) .

Para obter informações mais detalhadas sobre as funções de orquestrador e seus recursos, consulte o artigo [orquestrações duráveis](durable-functions-orchestrations.md) .

## <a name="activity-functions"></a>Funções de atividade

As funções de atividade são a unidade básica de trabalho em uma orquestração de função durável. Funções de atividade são as funções e tarefas que são orquestradas no processo. Por exemplo, você pode criar uma função de orquestrador para processar um pedido. As tarefas envolvem a verificação do inventário, o carregamento do cliente e a criação de uma remessa. Cada tarefa seria uma função de atividade separada. Essas funções de atividade podem ser executadas em série, em paralelo ou em alguma combinação de ambos.

Ao contrário das funções de orquestrador, as funções de atividade não são restritas no tipo de trabalho que você pode fazer neles. As funções de atividade são usadas frequentemente para fazer chamadas de rede ou executar operações de uso intensivo de CPU. Uma função de atividade também pode retornar dados de volta para a função de orquestrador. A estrutura de tarefa durável garante que cada função de atividade chamada será executada *pelo menos uma vez* durante a execução de uma orquestração.

> [!NOTE]
> Como as funções de atividade garantem apenas *pelo menos uma vez a* execução, recomendamos que você torne a lógica da função de atividade *idempotente* sempre que possível.

Use um [gatilho de atividade](durable-functions-bindings.md#activity-trigger) para definir uma função de atividade. As funções do .NET recebem um `DurableActivityContext` como parâmetro. Você também pode associar o gatilho a qualquer outro objeto serializável em JSON para transmitir entradas para a função. No JavaScript, você pode acessar uma entrada por meio da `<activity trigger binding name>` propriedade no [ `context.bindings` objeto](../functions-reference-node.md#bindings). As funções de atividade só podem ter um único valor passado a elas. Para passar vários valores, você deve usar tuplas, matrizes ou tipos complexos.

> [!NOTE]
> Você pode disparar uma função de atividade somente de uma função de orquestrador.

## <a name="entity-functions"></a>Funções de entidade

As funções de entidade definem operações para ler e atualizar pequenas partes de estado. Geralmente, nos referimos a essas entidades com estado como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, o *gatilho de entidade*. Eles também podem ser invocados de funções de cliente ou de funções de orquestrador. Ao contrário das funções de orquestrador, as funções de entidade não têm nenhuma restrição de código específica. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

> [!NOTE]
> As funções de entidade e a funcionalidade relacionada estão disponíveis apenas nas Durable Functions 2.0 e superior.

Para obter mais informações sobre funções de entidade, consulte o artigo sobre [entidades duráveis](durable-functions-entities.md) .

## <a name="client-functions"></a>Funções do cliente

As funções de orquestrador são disparadas por uma [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger) e as funções de entidade são disparadas por uma [Associação de gatilho de entidade](durable-functions-bindings.md#entity-trigger). Ambos os gatilhos funcionam reagindo a mensagens enfileiradas em um [Hub de tarefas](durable-functions-task-hubs.md). A principal maneira de fornecer essas mensagens é usando uma [Associação de cliente do Orchestrator](durable-functions-bindings.md#orchestration-client) ou uma [Associação de cliente de entidade](durable-functions-bindings.md#entity-client) de dentro de uma função de *cliente*. Qualquer função que não seja de orquestrador pode ser uma *função de cliente*. Por exemplo, você pode disparar o orquestrador de uma função disparada por HTTP, uma função disparada pelo hub de eventos do Azure, etc. O que torna uma função uma *função de cliente* é seu uso da Associação de saída de cliente durável.

> [!NOTE]
> Ao contrário de outros tipos de função, o orquestrador e as funções de entidade não podem ser disparados diretamente usando os botões no portal do Azure. Se você quiser testar uma função de orquestrador ou entidade no portal do Azure, deverá executar uma função de *cliente* que inicia uma função de orquestrador ou entidade como parte de sua implementação. Para a experiência de teste mais simples, é recomendável uma função de *gatilho manual* .

Além de disparar o Orchestrator ou as funções de entidade, a ligação de *cliente durável* pode ser usada para interagir com orquestrações e entidades em execução. Por exemplo, orquestrações podem ser consultadas, encerradas e podem ter eventos gerados. Para obter mais informações sobre como gerenciar orquestrações e entidades, consulte o artigo [Gerenciamento de instâncias](durable-functions-instance-management.md) .

## <a name="next-steps"></a>Próximas etapas

Para começar, crie sua primeira função durável em [C#](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Leia mais sobre orquestrações de Durable Functions](durable-functions-orchestrations.md)