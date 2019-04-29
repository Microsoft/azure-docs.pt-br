---
title: Cenários de fan-out/fan-in nas Funções Duráveis – Azure
description: Saiba como implementar um cenário de fan-out/fan-in na extensão de Funções Duráveis do Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0bef5f1b64ec9f322070ba5c36cab138c7327da2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741263"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Cenário de fan-out/fan-in nas Funções Duráveis – Exemplo de backup em nuvem

*Fan-out/fan-in* é o padrão de executar várias funções simultaneamente e, em seguida, executar alguma agregação dos resultados. Este artigo descreve um exemplo que usa as [Funções Duráveis](durable-functions-overview.md) para implementar um cenário de fan-out/fan-in. O exemplo é uma função durável que faz o backup de todo ou de parte do conteúdo do site de um aplicativo no Armazenamento do Azure.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Visão geral do cenário

Neste exemplo, as funções carregam todos os arquivos em um diretório especificado, recursivamente, no armazenamento de blobs. Elas também contam o número total de bytes que foram carregados.

É possível escrever uma única função que cuida de tudo. O principal problema que você teria seria a **escalabilidade**. Uma única função só pode ser executada em uma única VM, de modo que a taxa de transferência seria limitada à taxa de transferência dessa VM. Outro problema é a **confiabilidade**. Se houver um falha no meio do caminho ou se o processo inteiro levar mais de 5 minutos, o backup poderá falhar com um estado parcialmente concluído. Em seguida, ele precisaria ser reiniciado.

Uma abordagem mais robusta seria escrever duas funções regulares: um enumeraria os arquivos e adicionaria os nomes dos arquivo a uma fila e a outra leria da fila e carregaria os arquivos no armazenamento de blobs. Isso é melhor em termos de taxa de transferência e confiabilidade, mas requer que você provisione e gerencie uma fila. E, mais importante, uma complexidade significativa é introduzida em termos de **gerenciamento de estado** e **coordenação** se você quiser fazer qualquer outra coisa, como relatar o número total de bytes carregados.

Uma abordagem com as Funções Duráveis fornece todos os benefícios mencionados, com pouquíssima sobrecarga.

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

As seções a seguir explicam a configuração e o código utilizados para o script C#. O código para desenvolvimento no Visual Studio é exibido no final do artigo.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A orquestração de backup de nuvem (código de exemplo do Visual Studio Code e do portal do Azure)

A função `E2_BackupSiteContent` usa o *function.json* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Este é o código que implementa a função de orquestrador:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Essa função de orquestrador faz, essencialmente, o seguinte:

1. Usa um valor de `rootDirectory` como um parâmetro de entrada.
2. Chama uma função para obter uma lista recursiva de arquivos em `rootDirectory`.
3. Faz várias chamadas de função paralelas para carregar cada arquivo no Armazenamento de Blobs do Azure.
4. Aguarda que todos os uploads sejam concluídos.
5. Retorna o total de bytes que foram carregados no Armazenamento de Blobs do Azure.

Observe as linhas `await Task.WhenAll(tasks);` (C#) e `yield context.df.Task.all(tasks);` (JavaScript). Todas as chamadas individuais para a função `E2_CopyFileToBlob` *não* eram aguardadas. Isso é intencional, para permitir que sejam executadas em paralelo. Quando passamos essa matriz de tarefas para `Task.WhenAll` (C#) ou `context.df.Task.all` (JavaScript), obtemos uma tarefa que não será concluída *até que todas as operações de cópia tenham sido concluídas*. Se você está familiarizado com a TPL (biblioteca de paralelismo de tarefas) no .NET ou [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) em JavaScript, isso não é novidade para você. A diferença é que essas tarefas poderiam ser executadas simultaneamente em várias VMs e a extensão Funções Duráveis assegura que a execução de ponta a ponta seja resiliente em caso de reciclagem do processo.

> [!NOTE]
> Embora as tarefas sejam conceitualmente semelhantes a promessas JavaScript, funções de orquestrador devem usar `context.df.Task.all` e `context.df.Task.any` em vez de `Promise.all` e `Promise.race` para gerenciar a paralelização de tarefa.

Depois de aguardar `Task.WhenAll` (ou resultante de `context.df.Task.all`), sabemos que todas as chamadas de função foram concluídas e retornaram valores para nós. Cada chamada para `E2_CopyFileToBlob` retorna o número de bytes carregados, de forma que calcular a contagem total de bytes é uma questão de adicionar todos esses valores retornados.

## <a name="helper-activity-functions"></a>Funções de atividade auxiliares

Funções de atividade auxiliares, assim como ocorre com os outros exemplos, são apenas funções regulares que usam a associação de gatilho `activityTrigger`. Por exemplo, o aquivo *function.json* para `E2_GetFileList` é semelhante ao seguinte:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

E esta é a implementação:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A implementação em JavaScript de `E2_GetFileList` usa o módulo `readdirp` para recursivamente ler a estrutura de diretório.

> [!NOTE]
> Você deve estar se perguntando por que você não poderia simplesmente colocar esse código diretamente na função de orquestrador. Você poderia, mas isso violaria uma das regras fundamentais das funções de orquestrador, de que elas nunca devem executar E/S, incluindo no caso de acesso ao sistema de arquivos local.

O arquivo *function.json* para `E2_CopyFileToBlob` também é simples:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

A implementação de C# também é bastante simples. Ela usa alguns recursos avançados das associações do Azure Functions (ou seja, o uso do parâmetro `Binder`), mas você não precisa se preocupar com esses detalhes no contexto deste passo a passo.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

A implementação de JavaScript não tem acesso ao recurso `Binder` do Azure Functions, assim a [SDK de Armazenamento do Azure para o Nó](https://github.com/Azure/azure-storage-node) entra em seu lugar.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

A implementação carrega o arquivo no disco e transmite de forma assíncrona o conteúdo para um blob de mesmo nome no contêiner "backups". O valor retornado é o número de bytes copiados para o armazenamento, que é usado pela função de orquestrador para calcular a soma agregada.

> [!NOTE]
> Este é um exemplo perfeito de movimentação de operações de E/S para uma função `activityTrigger`. Não só o trabalho pode ser distribuído entre várias VMs diferentes, mas você também obtém os benefícios de fazer verificações pontuais do progresso. Se o processo de host for encerrado por algum motivo, você saberá quais carregamentos já foram concluídos.

## <a name="run-the-sample"></a>Execute o exemplo

Você pode iniciar a orquestração enviando a solicitação HTTP POST a seguir.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A função `HttpStart` que você está invocando funciona somente com conteúdo formatado em JSON. Por esse motivo, o cabeçalho `Content-Type: application/json` é obrigatório e o caminho do diretório é codificado como uma cadeia de caracteres JSON. Além disso, o fragmento de HTTP assume que existe uma entrada no arquivo `host.json`que remove o prefixo padrão`api/` de todas as URLs de funções do acionador HTTP. Você pode encontrar a marcação para essa configuração no arquivo `host.json` nos exemplos.

Esta solicitação HTTP dispara o orquestrador `E2_BackupSiteContent` e passa a cadeia de caracteres `D:\home\LogFiles` como um parâmetro. A resposta fornece um link para obter o status da operação de backup:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Dependendo de quantos arquivos de log você tiver em seu aplicativo de funções, essa operação pode levar vários minutos para ser concluída. Você pode obter o status mais recente consultando a URL no cabeçalho `Location` da resposta HTTP 202 anterior.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

Nesse caso, a função ainda está sendo executada. É possível ver a entrada que foi salva no estado do orquestrador e a hora da última atualização. Você pode continuar usando os valores de cabeçalho `Location` para sondar a conclusão. Quando o status for "Concluído", você verá um valor de resposta HTTP semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Agora, você pode ver que a orquestração foi concluída e aproximadamente quanto tempo ela levou para ser concluída. Você também verá um valor para o campo `output`, o que indica que cerca de 450 KB de logs foram carregados.

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Esta é a orquestração como um único arquivo em C# em um projeto do Visual Studio:

> [!NOTE]
> Você precisará instalar o `Microsoft.Azure.WebJobs.Extensions.Storage` pacote do Nuget para executar o código de exemplo abaixo.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Próximas etapas

Este exemplo mostra como implementar o padrão de fan-out/fan-in. O próximo exemplo mostra como implementar o padrão de monitor usando [temporizadores variáveis](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Executar o exemplo de monitor](durable-functions-monitor.md)
