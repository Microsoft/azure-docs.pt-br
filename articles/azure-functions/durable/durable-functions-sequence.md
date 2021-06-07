---
title: Encadeamento de funções nas Funções Duráveis – Azure
description: Saiba como executar um exemplo de Funções Duráveis que executa uma sequência de funções.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: f8223b1273c2a487e15e3c10d7c6852a119e4cdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028243"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Encadeamento de funções nas Funções Duráveis – Exemplo se sequência Hello

Encadeamento de funções é o padrão de executar uma sequência de funções em uma ordem específica. Frequentemente, a saída de uma função precisa ser aplicada à entrada de outra função. Este artigo descreve a sequência de encadeamento que você cria ao concluir a Durable Functions início rápido ([C#](durable-functions-create-first-csharp.md),  [JavaScript](quickstart-js-vscode.md)ou [python](quickstart-python-vscode.md)). Para obter mais informações sobre as funções duráveis, consulte [visão geral das funções duráveis](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E1_HelloSequence`: Uma [função de orquestrador](durable-functions-bindings.md#orchestration-trigger) que chama `E1_SayHello` várias vezes em uma sequência. Ela armazena as saídas das chamadas `E1_SayHello` e registra os resultados.
* `E1_SayHello`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que precede uma cadeia de caracteres com "Olá".
* `HttpStart`: Uma função de [cliente durável](durable-functions-bindings.md#orchestration-client) disparada por http que inicia uma instância do orquestrador.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence função de orquestrador

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Todas as funções de orquestração em C# devem ter um parâmetro do tipo `DurableOrchestrationContext`, que existe no assembly `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Esse objeto de contexto permite chamar outras funções de *atividade* e passar parâmetros de entrada usando seu `CallActivityAsync` método.

O código chama `E1_SayHello` três vezes seguidas com valores de parâmetros diferentes. O valor retornado de cada chamada é adicionado à lista `outputs`, que é retornada ao final da função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> Durable Functions de JavaScript estão disponíveis apenas para o Functions 3,0 Runtime.

#### <a name="functionjson"></a>function.json

Se você usa o Visual Studio Code ou o portal do Azure para desenvolvimento, este é o conteúdo do arquivo *function.json* para a função de orquestrador. A maioria dos arquivos *function.json* do orquestrador são quase exatamente iguais a esse.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

O importante é o tipo de associação de `orchestrationTrigger`. Todas as funções de orquestrador devem usar esse tipo de gatilho.

> [!WARNING]
> Para obedecer a regra de "não fazer E/S" das funções de orquestrador, não use nenhuma associação de entrada ou saída ao usar a associação de gatilho `orchestrationTrigger`.  Se outras associações de entrada ou de saída forem necessárias, elas deverão ser usadas no contexto das funções `activityTrigger`, que são chamadas pelo orquestrador. Para obter mais informações, consulte o artigo [restrições de código de função do Orchestrator](durable-functions-code-constraints.md) .

#### <a name="indexjs"></a>index.js

Esta é a função de orquestrador:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Todas as funções de orquestração do JavaScript devem incluir o [ `durable-functions` módulo](https://www.npmjs.com/package/durable-functions). É uma biblioteca que permite que você escreva Durable Functions em JavaScript. Há três diferenças significativas entre uma função de orquestrador e outras funções JavaScript:

1. A função de orquestrador é uma [função de gerador](/scripting/javascript/advanced/iterators-and-generators-javascript).
2. A função é encapsulada em uma chamada para o método `orchestrator` do módulo `durable-functions` (aqui `df`).
3. A função deve ser síncrona. Como o método "orchestrator" lida com a chamada "context.done", a função deve simplesmente "return".

O `context` objeto contém um `df` objeto de contexto de orquestração durável que permite chamar outras funções de *atividade* e passar parâmetros de entrada usando seu `callActivity` método. O código chama `E1_SayHello` três vezes em sequência com valores de parâmetros diferentes, usando `yield` para indicar que a execução deve aguardar as chamadas de função de atividade assíncrona serem retornadas. O valor de retorno de cada chamada é adicionado à `outputs` matriz, que é retornada no final da função.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Os Durable Functions do Python estão disponíveis apenas para o Functions 3,0 Runtime.


#### <a name="functionjson"></a>function.json

Se você usa o Visual Studio Code ou o portal do Azure para desenvolvimento, este é o conteúdo do arquivo *function.json* para a função de orquestrador. A maioria dos arquivos *function.json* do orquestrador são quase exatamente iguais a esse.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/function.json)]

O importante é o tipo de associação de `orchestrationTrigger`. Todas as funções de orquestrador devem usar esse tipo de gatilho.

> [!WARNING]
> Para obedecer a regra de "não fazer E/S" das funções de orquestrador, não use nenhuma associação de entrada ou saída ao usar a associação de gatilho `orchestrationTrigger`.  Se outras associações de entrada ou de saída forem necessárias, elas deverão ser usadas no contexto das funções `activityTrigger`, que são chamadas pelo orquestrador. Para obter mais informações, consulte o artigo [restrições de código de função do Orchestrator](durable-functions-code-constraints.md) .

#### <a name="__init__py"></a>\_\_init\_\_.py

Esta é a função de orquestrador:

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/\_\_init\_\_.py)]

Todas as funções de orquestração do Python devem incluir o [ `durable-functions` pacote](https://pypi.org/project/azure-functions-durable). É uma biblioteca que permite que você escreva Durable Functions em Python. Há duas diferenças significativas entre uma função de orquestrador e outras funções do Python:

1. A função de orquestrador é uma [função de gerador](https://wiki.python.org/moin/Generators).
2. O _arquivo_ deve registrar a função de orquestrador como um orquestrador, informando `main = df.Orchestrator.create(<orchestrator function name>)` ao final do arquivo. Isso ajuda a distingui-lo de outras funções, auxiliares, declaradas no arquivo.

O `context` objeto permite que você chame outras funções de *atividade* e passe parâmetros de entrada usando seu `call_activity` método. O código chama `E1_SayHello` três vezes em sequência com valores de parâmetros diferentes, usando `yield` para indicar que a execução deve aguardar as chamadas de função de atividade assíncrona serem retornadas. O valor de retorno de cada chamada é retornado no final da função.

---

### <a name="e1_sayhello-activity-function"></a>Função de atividade de E1_SayHello

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

As atividades usam o `ActivityTrigger` atributo. Use o fornecido `IDurableActivityContext` para executar ações relacionadas à atividade, como acessar o valor de entrada usando `GetInput<T>` .

A implementação de `E1_SayHello` é uma operação de formatação de cadeia de caracteres relativamente simples.

Em vez de associar a um `IDurableActivityContext` , você pode associar diretamente ao tipo que é passado para a função de atividade. Por exemplo:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsem

O arquivo *function.json* da função de atividade `E1_SayHello` é semelhante ao de `E1_HelloSequence`, exceto por usar um tipo de associação `activityTrigger` em vez de um tipo de associação `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Todas as funções de atividade chamadas por uma função de orquestração devem usar a `activityTrigger` associação.

A implementação de `E1_SayHello` é uma operação de formatação de cadeia de caracteres relativamente simples.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Ao contrário da função de orquestração, uma função de atividade não precisa de nenhuma configuração especial. A entrada passada a ela pela função do orquestrador está localizada no objeto `context.bindings` sob o nome da ligação `activityTrigger`; nesse caso, `context.bindings.name`. O nome da associação pode ser definido como parâmetro da função exportada e acessado diretamente, que é o que o código de exemplo faz.

# <a name="python"></a>[Python](#tab/python)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsem

O arquivo *function.json* da função de atividade `E1_SayHello` é semelhante ao de `E1_HelloSequence`, exceto por usar um tipo de associação `activityTrigger` em vez de um tipo de associação `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/function.json)]

> [!NOTE]
> Todas as funções de atividade chamadas por uma função de orquestração devem usar a `activityTrigger` associação.

A implementação de `E1_SayHello` é uma operação de formatação de cadeia de caracteres relativamente simples.

#### <a name="e1_sayhello__init__py"></a>E1_SayHello/ \_ \_ init \_ \_ . py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/\_\_init\_\_.py)]

Diferentemente da função de orquestrador, uma função de atividade não precisa de nenhuma configuração especial. A entrada passada para ela pela função de orquestrador é diretamente acessível como o parâmetro para a função.

---

### <a name="httpstart-client-function"></a>Função de cliente HttpStart

Você pode iniciar uma instância da função de orquestrador usando uma função de cliente. Você usará a `HttpStart` função http disparada para iniciar instâncias do `E1_HelloSequence` .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Para interagir com orquestradores, a função deve incluir uma `DurableClient` Associação de entrada. Você usa o cliente para iniciar uma orquestração. Ele também pode ajudá-lo a retornar uma resposta HTTP contendo URLs para verificar o status da nova orquestração.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsem

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Para interagir com orquestradores, a função deve incluir uma `durableClient` Associação de entrada.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Use `df.getClient` para obter um `DurableOrchestrationClient` objeto. Você usa o cliente para iniciar uma orquestração. Ele também pode ajudá-lo a retornar uma resposta HTTP contendo URLs para verificar o status da nova orquestração.

# <a name="python"></a>[Python](#tab/python)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsem

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/function.json)]

Para interagir com orquestradores, a função deve incluir uma `durableClient` Associação de entrada.

#### <a name="httpstart__init__py"></a>HttpStart/ \_ \_ init \_ \_ . py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/\_\_init\_\_.py)]

Use o `DurableOrchestrationClient` construtor para obter um cliente Durable functions. Você usa o cliente para iniciar uma orquestração. Ele também pode ajudá-lo a retornar uma resposta HTTP contendo URLs para verificar o status da nova orquestração.

---

## <a name="run-the-sample"></a>Execute o exemplo

Para executar a `E1_HelloSequence` orquestração, envie a seguinte solicitação HTTP post para a `HttpStart` função.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> O snippet de código HTTP anterior pressupõe que exista uma entrada no arquivo `host.json` que remove o prefixo `api/` padrão de todas as URLs de funções de gatilho HTTP. Você pode encontrar a marcação para essa configuração no arquivo `host.json` nos exemplos.

Por exemplo, se você estiver executando a amostra em um aplicativo da função chamado "myfunctionapp", substitua "{host}" por "myfunctionapp.azurewebsites.net".

O resultado é uma resposta HTTP 202, como esta (resumido para fins de brevidade):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Neste ponto, a orquestração é enfileirada e começa a ser executada imediatamente. A URL no cabeçalho `Location` pode ser usada para verificar o status da execução.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

O resultado é o status da orquestração. Ela é executada e concluída rapidamente e você a verá com estado *Concluído*, com uma resposta semelhante esta (resumida para fins de brevidade):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Como você pode ver, o `runtimeStatus` da instância é *Concluído* e o `output` contém o resultado serializado em JSON da execução da função de orquestrador.

> [!NOTE]
> Você pode implementar uma lógica inicial semelhante a outros tipos de gatilho, como `queueTrigger`, `eventHubTrigger` ou `timerTrigger`.

Examine os logs de execução da função. A `E1_HelloSequence` função foi iniciada e concluída várias vezes devido ao comportamento de reprodução descrito no tópico de [confiabilidade da orquestração](durable-functions-orchestrations.md#reliability) . Por outro lado, houve apenas três execuções de `E1_SayHello`, uma vez que as execuções dessas funções não são repetidas.

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstrou uma orquestração de encadeamento de função simples. O próximo exemplo mostra como implementar o padrão de fan-out/fan-in.

> [!div class="nextstepaction"]
> [Executar o exemplo de fan-out/fan-in](durable-functions-cloud-backup.md)
