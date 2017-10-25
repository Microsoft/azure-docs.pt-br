---
title: "Singletons com estado nas Funções Duráveis – Azure"
description: "Saiba como implementar um singleton com estado na extensão de Funções Duráveis do Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: d62bc24a0439aa8c11ced9d5f42917f9b6de1f24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Singletons com estado nas Funções Duráveis – Exemplo de contador

Singletons com estado são funções de orquestrador de execução longa (potencialmente eterna) que podem armazenar o estado e podem ser invocados e consultados por outras funções. Singletons com estado são semelhantes ao [Modelo de ator](https://en.wikipedia.org/wiki/Actor_model) na computação distribuída.

Embora não sejam exatamente uma implementação de "ator", as funções de orquestrador têm muitas das mesmas características de tempo de execução. Por exemplo, elas têm estado, são confiáveis, de thread único, com transparência de local e endereçáveis globalmente. Essas são características que tornam as implementações de ator reais especialmente útil, mas sem a necessidade de uma estrutura separada.

Este artigo mostra como executar o exemplo de *contador*. O exemplo demonstra um objeto singleton que dá suporte a operações de *incremento* e *decremento* e atualiza seu estado interno de acordo com elas.

## <a name="prerequisites"></a>Pré-requisitos

* Siga as instruções em [Instalar Funções Duráveis](durable-functions-install.md) para configurar o exemplo.
* Este artigo pressupõe que você já tenha passado pelo passo a passo do exemplo [Sequência Hello](durable-functions-sequence.md).

## <a name="scenario-overview"></a>Visão geral do cenário

O cenário do contador é surpreendentemente difícil de implementar usando funções sem monitoração de estado regulares. Um dos principais desafios que você tem é gerenciar a **simultaneidade**. Operações como *incremento* e *decremento* precisam ser atômicas. Caso contrário, poderia haver condições de corrida que fariam com que as operações substituíssem umas às outras.

Usar uma única VM para hospedar os dados do contador é uma opção, mas isso é caro e gerenciar a **confiabilidade** pode ser um desafio, pois uma única VM poderia ser reinicializada periodicamente. Como alternativa, você poderia usar uma plataforma distribuída com ferramentas de sincronização, como as concessões de blob, para ajudar a gerenciar a simultaneidade, mas isso introduz muita **complexidade**.

As Funções Duráveis tornam esse tipo de cenário simples de implementar, pois as instâncias de orquestração são relacionadas a uma única VM e a execução da função de orquestrador sempre é de thread único. Além disso, mas elas têm execução linda, têm estado e podem reagir a eventos externos. O código de exemplo abaixo demonstra como implementar um contador desse tipo como uma função de orquestrador de execução longa.

## <a name="the-sample-function"></a>A função de exemplo

Este artigo aborda a função **E3_Counter** no aplicativo de exemplo.

As seções a seguir explicam o código que é usado para desenvolvimento no Visual Studio. O código para desenvolvimento no portal do Azure é semelhante.

## <a name="the-counter-orchestration"></a>A orquestração do contador

Este é o código que implementa a função de orquestrador:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)]

Essa função de orquestrador faz, essencialmente, o seguinte:

1. Escuta um evento externo chamado *operation* usando [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_).
2. Faz o incremento ou decremento da variável local `counterState`, dependendo da operação solicitada.
3. Reinicia o orchestrator usando o método [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_), definindo o valor mais recente de `counterState` como a nova entrada.
4. Continua sendo executado indefinidamente ou até que uma mensagem *end* seja recebida.

Este é um exemplo de *orquestração eterna* &mdash; ou seja, uma que potencialmente nunca termina. Ela responde a mensagens enviadas pelo método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_), que pode ser chamado por qualquer função que não seja de orquestrador.

Uma característica exclusiva dessa função de orquestrador é que ela efetivamente não tem nenhum histórico: o método `ContinueAsNew` redefine o histórico depois de cada evento processado. Essa é a melhor maneira de implementar um orquestrador que tem um tempo de vida arbitrário. Usar um loop `while` pode fazer com que o histórico da função de orquestrador cresça livremente, resultando em um alto uso de memória desnecessário.

> [!NOTE]
> O método `ContinueAsNew` tem outros casos de uso além das orquestrações eternas. Para obter mais informações, consulte [Orquestrações Eternas](durable-functions-eternal-orchestrations.md).

## <a name="running-the-sample"></a>Executando o exemplo

Usando as funções disparadas por HTTP incluídas no exemplo, você pode iniciar a orquestração usando a seguinte solicitação HTTP POST. Para permitir que `counterState` comece em zero (o valor padrão para `int`), não há nenhum conteúdo nesta solicitação.

```
POST http://{host}/orchestrators/E3_Counter HTTP/1.1
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"bcf6fb5067b046fbb021b52ba7deae5a","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

A instância de **E3_Counter** inicia e, em seguida, aguarda imediatamente que um evento seja enviado a ela usando `RaiseEventAsync` ou usando o webhook HTTP POST **sendEventUrl** referenciado na resposta 202. Valores válidos de `eventName` incluem *incr*, *decr* e *end*.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
Content-Type: application/json
Content-Length: 6

"incr"
```

Você pode ver os resultados da operação "incr" examinando os logs de função no portal do Azure Functions.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

Da mesma forma, se verificar o status do orquestrador, você verá que o campo `input` foi definido com o valor atualizado (1).

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

Você pode continuar enviando novas operações para essa instância e pode observar que seu estado é atualizado adequadamente. Se quiser eliminar a instância, você poderá fazer isso enviando uma operação *end*.

> [!WARNING]
> No momento da escrita, há condições de corrida conhecidas ao chamar `ContinueAsNew` enquanto mensagens são processadas simultaneamente, como eventos externos ou solicitações de encerramento. Para obter as informações mais recentes sobre essas condições de corrida, consulte este [problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/67).

## <a name="wrapping-up"></a>Conclusão

Neste ponto, você tem uma melhor compreensão de alguns dos recursos avançados das Funções Duráveis, especialmente `WaitForExternalEvent` e `ContinueAsNew`. Essas ferramentas permitem escrever várias formas de "singletons com estado", como contadores e agregadores.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Executar o exemplo de interação humana](durable-functions-phone-verification.md)



