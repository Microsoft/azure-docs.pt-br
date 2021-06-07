---
title: Cenários de fan-out/fan-in nas Funções Duráveis – Azure
description: Saiba como implementar um cenário de fan-out/fan-in na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 91128033696af6a56488db7991987f1e384b719e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027632"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Cenário de fan-out/fan-in nas Funções Duráveis – Exemplo de backup em nuvem

*Fan-out/fan-in* é o padrão de executar várias funções simultaneamente e, em seguida, executar alguma agregação dos resultados. Este artigo descreve um exemplo que usa as [Funções Duráveis](durable-functions-overview.md) para implementar um cenário de fan-out/fan-in. O exemplo é uma função durável que faz o backup de todo ou de parte do conteúdo do site de um aplicativo no Armazenamento do Azure.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Visão geral do cenário

Neste exemplo, as funções carregam todos os arquivos em um diretório especificado, recursivamente, no armazenamento de blobs. Elas também contam o número total de bytes que foram carregados.

É possível escrever uma única função que cuida de tudo. O principal problema que você teria seria a **escalabilidade**. Uma única execução de função só pode ser executada em uma única máquina virtual, portanto, a taxa de transferência será limitada pela taxa de transferência dessa única VM. Outro problema é a **confiabilidade**. Se houver uma falha percorrendo ou se o processo inteiro levar mais de 5 minutos, o backup poderá falhar em um estado parcialmente concluído. Em seguida, ele precisaria ser reiniciado.

Uma abordagem mais robusta seria escrever duas funções regulares: um enumeraria os arquivos e adicionaria os nomes dos arquivo a uma fila e a outra leria da fila e carregaria os arquivos no armazenamento de blobs. Essa abordagem é melhor em termos de produtividade e confiabilidade, mas requer que você provisione e gerencie uma fila. E, mais importante, uma complexidade significativa é introduzida em termos de **gerenciamento de estado** e **coordenação** se você quiser fazer qualquer outra coisa, como relatar o número total de bytes carregados.

Uma abordagem com as Funções Duráveis fornece todos os benefícios mencionados, com pouquíssima sobrecarga.

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E2_BackupSiteContent`: Uma [função de orquestrador](durable-functions-bindings.md#orchestration-trigger) que chama `E2_GetFileList` para obter uma lista de arquivos para fazer backup e, em seguida, chama `E2_CopyFileToBlob` para fazer backup de cada arquivo.
* `E2_GetFileList`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que retorna uma lista de arquivos em um diretório.
* `E2_CopyFileToBlob`: Uma função de atividade que faz backup de um único arquivo para o armazenamento de BLOBs do Azure.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent função de orquestrador

Essa função de orquestrador faz, essencialmente, o seguinte:

1. Usa um valor de `rootDirectory` como um parâmetro de entrada.
2. Chama uma função para obter uma lista recursiva de arquivos em `rootDirectory`.
3. Faz várias chamadas de função paralelas para carregar cada arquivo no Armazenamento de Blobs do Azure.
4. Aguarda que todos os uploads sejam concluídos.
5. Retorna o total de bytes que foram carregados no Armazenamento de Blobs do Azure.

# <a name="c"></a>[C#](#tab/csharp)

Este é o código que implementa a função de orquestrador:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Observe a linha `await Task.WhenAll(tasks);`. Todas as chamadas individuais para a `E2_CopyFileToBlob` função *não* foram aguardadas, o que permite que elas sejam executadas em paralelo. Quando passamos essa matriz de tarefas para `Task.WhenAll`, obtemos uma tarefa que não será concluída *até que todas as operações de cópia tenham sido concluídas*. Se você estiver familiarizado com a TPL (biblioteca de paralelismo de tarefas) no .NET, isso não é novidade para você. A diferença é que essas tarefas podem ser executadas em várias máquinas virtuais simultaneamente, e a extensão de Durable Functions garante que a execução de ponta a ponta seja resiliente à reciclagem do processo.

Depois de aguardar `Task.WhenAll`, sabemos que todas as chamadas de função foram concluídas e retornaram valores para nós. Cada chamada para `E2_CopyFileToBlob` retorna o número de bytes carregados, de forma que calcular a contagem total de bytes é uma questão de adicionar todos esses valores retornados.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A função usa o *function.jspadrão no* para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Este é o código que implementa a função de orquestrador:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Observe a linha `yield context.df.Task.all(tasks);`. Todas as chamadas individuais para a `E2_CopyFileToBlob` função *não* foram geradas, o que permite que elas sejam executadas em paralelo. Quando passamos essa matriz de tarefas para `context.df.Task.all`, obtemos uma tarefa que não será concluída *até que todas as operações de cópia tenham sido concluídas*. Se você estiver familiarizado com [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) o JavaScript, isso não é novidade para você. A diferença é que essas tarefas podem ser executadas em várias máquinas virtuais simultaneamente, e a extensão de Durable Functions garante que a execução de ponta a ponta seja resiliente à reciclagem do processo.

> [!NOTE]
> Embora as tarefas sejam conceitualmente semelhantes a promessas JavaScript, funções de orquestrador devem usar `context.df.Task.all` e `context.df.Task.any` em vez de `Promise.all` e `Promise.race` para gerenciar a paralelização de tarefa.

Após a concessão de `context.df.Task.all` , sabemos que todas as chamadas de função foram concluídas e retornaram valores de volta para nós. Cada chamada para `E2_CopyFileToBlob` retorna o número de bytes carregados, de forma que calcular a contagem total de bytes é uma questão de adicionar todos esses valores retornados.

# <a name="python"></a>[Python](#tab/python)

A função usa o *function.jspadrão no* para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/function.json)]

Este é o código que implementa a função de orquestrador:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/\_\_init\_\_.py)]

Observe a linha `yield context.task_all(tasks);`. Todas as chamadas individuais para a `E2_CopyFileToBlob` função *não* foram geradas, o que permite que elas sejam executadas em paralelo. Quando passamos essa matriz de tarefas para `context.task_all`, obtemos uma tarefa que não será concluída *até que todas as operações de cópia tenham sido concluídas*. Se você estiver familiarizado com [`asyncio.gather`](https://docs.python.org/3/library/asyncio-task.html#asyncio.gather) o em Python, isso não é novidade para você. A diferença é que essas tarefas podem ser executadas em várias máquinas virtuais simultaneamente, e a extensão de Durable Functions garante que a execução de ponta a ponta seja resiliente à reciclagem do processo.

> [!NOTE]
> Embora as tarefas sejam conceitualmente semelhantes ao Python awaitables, as funções de orquestrador devem usar, `yield` bem como as `context.task_all` `context.task_any` APIs e para gerenciar a paralelização de tarefas.

Após a concessão de `context.task_all` , sabemos que todas as chamadas de função foram concluídas e retornaram valores de volta para nós. Cada chamada para `E2_CopyFileToBlob` retorna o número de bytes carregados, para que possamos calcular a contagem total de bytes somando todos os valores retornados juntos.

---

### <a name="helper-activity-functions"></a>Funções de atividade auxiliares

Funções de atividade auxiliares, assim como ocorre com os outros exemplos, são apenas funções regulares que usam a associação de gatilho `activityTrigger`.

#### <a name="e2_getfilelist-activity-function"></a>Função de atividade de E2_GetFileList

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O *function.jsno* arquivo para `E2_GetFileList` é semelhante ao seguinte:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

E esta é a implementação:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A função usa o `readdirp` módulo (versão 2. x) para ler recursivamente a estrutura do diretório.

# <a name="python"></a>[Python](#tab/python)

O *function.jsno* arquivo para `E2_GetFileList` é semelhante ao seguinte:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/function.json)]

E esta é a implementação:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/\_\_init\_\_.py)]

---

> [!NOTE]
> Você deve estar se perguntando por que você não poderia simplesmente colocar esse código diretamente na função de orquestrador. Você poderia, mas isso violaria uma das regras fundamentais das funções de orquestrador, de que elas nunca devem executar E/S, incluindo no caso de acesso ao sistema de arquivos local. Para obter mais informações, consulte [restrições de código de função do Orchestrator](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>Função de atividade de E2_CopyFileToBlob

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Será necessário instalar o `Microsoft.Azure.WebJobs.Extensions.Storage` pacote NuGet para executar o código de exemplo.

A função usa alguns recursos avançados de associações de Azure Functions (ou seja, o uso do [ `Binder` parâmetro](../functions-dotnet-class-library.md#binding-at-runtime)), mas você não precisa se preocupar com esses detalhes para fins deste passo a passos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O arquivo *function.json* para `E2_CopyFileToBlob` também é simples:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

A implementação do JavaScript usa o [SDK do armazenamento do Azure para o nó](https://github.com/Azure/azure-storage-node) para carregar os arquivos no armazenamento de BLOBs do Azure.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

# <a name="python"></a>[Python](#tab/python)

O arquivo *function.json* para `E2_CopyFileToBlob` também é simples:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/function.json)]

A implementação do Python usa o [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python) para carregar os arquivos no armazenamento de BLOBs do Azure.

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/\_\_init\_\_.py)]

---

A implementação carrega o arquivo no disco e transmite de forma assíncrona o conteúdo para um blob de mesmo nome no contêiner "backups". O valor retornado é o número de bytes copiados para o armazenamento, que é usado pela função de orquestrador para calcular a soma agregada.

> [!NOTE]
> Este é um exemplo perfeito de movimentação de operações de E/S para uma função `activityTrigger`. O trabalho não só pode ser distribuído em vários computadores diferentes, mas você também obtém os benefícios de ponto de verificação do progresso. Se o processo de host for encerrado por algum motivo, você saberá quais carregamentos já foram concluídos.

## <a name="run-the-sample"></a>Execute o exemplo

Você pode iniciar a orquestração, no Windows, enviando a seguinte solicitação HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

Como alternativa, em um Aplicativo de funções do Linux (o Python atualmente só é executado no Linux para o serviço de aplicativo), você pode iniciar a orquestração da seguinte maneira:

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"/home/site/wwwroot"
```

> [!NOTE]
> A função `HttpStart` que você está invocando funciona somente com conteúdo formatado em JSON. Por esse motivo, o cabeçalho `Content-Type: application/json` é obrigatório e o caminho do diretório é codificado como uma cadeia de caracteres JSON. Além disso, o fragmento de HTTP assume que existe uma entrada no arquivo `host.json`que remove o prefixo padrão`api/` de todas as URLs de funções do acionador HTTP. Você pode encontrar a marcação para essa configuração no arquivo `host.json` nos exemplos.

Esta solicitação HTTP dispara o orquestrador `E2_BackupSiteContent` e passa a cadeia de caracteres `D:\home\LogFiles` como um parâmetro. A resposta fornece um link para obter o status da operação de backup:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Dependendo de quantos arquivos de log você tiver em seu aplicativo de funções, essa operação pode levar vários minutos para ser concluída. Você pode obter o status mais recente consultando a URL no cabeçalho `Location` da resposta HTTP 202 anterior.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

Nesse caso, a função ainda está sendo executada. É possível ver a entrada que foi salva no estado do orquestrador e a hora da última atualização. Você pode continuar usando os valores de cabeçalho `Location` para sondar a conclusão. Quando o status for "Concluído", você verá um valor de resposta HTTP semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Agora, você pode ver que a orquestração foi concluída e aproximadamente quanto tempo ela levou para ser concluída. Você também verá um valor para o campo `output`, o que indica que cerca de 450 KB de logs foram carregados.

## <a name="next-steps"></a>Próximas etapas

Este exemplo mostra como implementar o padrão de fan-out/fan-in. O próximo exemplo mostra como implementar o padrão de monitor usando [temporizadores variáveis](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Executar o exemplo de monitor](durable-functions-monitor.md)