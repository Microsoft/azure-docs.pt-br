---
title: "Cenários de fan-out/fan-in nas Funções Duráveis – Azure"
description: "Saiba como implementar um cenário de fan-out/fan-in na extensão de Funções Duráveis do Azure Functions."
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
ms.openlocfilehash: aa7c0738120ecda8d43669725748585e1ad5a581
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Cenário de fan-out/fan-in nas Funções Duráveis – Exemplo de backup em nuvem

*Fan-out/fan-in* é o padrão de executar várias funções simultaneamente e, em seguida, executar alguma agregação dos resultados. Este artigo descreve um exemplo que usa as [Funções Duráveis](durable-functions-overview.md) para implementar um cenário de fan-out/fan-in. O exemplo é uma função durável que faz o backup de todo ou de parte do conteúdo do site de um aplicativo no Armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Siga as instruções em [Instalar Funções Duráveis](durable-functions-install.md) para configurar o exemplo.
* Este artigo pressupõe que você já tenha passado pelo passo a passo do exemplo [Sequência Hello](durable-functions-sequence.md).

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

As seções a seguir explicam a configuração e o código que são usados para desenvolvimento no portal do Azure. O código para desenvolvimento no Visual Studio é exibido no final do artigo.

## <a name="the-cloud-backup-orchestration"></a>A orquestração de backup em nuvem

A função `E2_BackupSiteContent` usa o *function.json* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Este é o código que implementa a função de orquestrador:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

Essa função de orquestrador faz, essencialmente, o seguinte:

1. Usa um valor de `rootDirectory` como um parâmetro de entrada.
2. Chama uma função para obter uma lista recursiva de arquivos em `rootDirectory`.
3. Faz várias chamadas de função paralelas para carregar cada arquivo no Armazenamento de Blobs do Azure.
4. Aguarda que todos os uploads sejam concluídos.
5. Retorna o total de bytes que foram carregados no Armazenamento de Blobs do Azure.

Observe a linha `await Task.WhenAll(tasks);`. As chamadas para a função `E2_CopyFileToBlob` *não* foram esperadas. Isso é intencional, para permitir que sejam executadas em paralelo. Quando passamos essa matriz de tarefas para `Task.WhenAll`, obtemos uma tarefa que não será concluída *até que todas as operações de cópia tenham sido concluídas*. Se você estiver familiarizado com a TPL (biblioteca de paralelismo de tarefas) no .NET, isso não é novidade para você. A diferença é que essas tarefas poderiam ser executadas simultaneamente em várias VMs e a extensão assegura que a execução de ponta a ponta seja resiliente em caso de reciclagem do processo.

Depois de aguardar `Task.WhenAll`, sabemos que todas as chamadas de função foram concluídas e retornaram valores para nós. Cada chamada para `E2_CopyFileToBlob` retorna o número de bytes carregados, de forma que calcular a contagem total de bytes é uma questão de adicionar todos esses valores retornados.

## <a name="helper-activity-functions"></a>Funções de atividade auxiliares

Funções de atividade auxiliares, assim como ocorre com os outros exemplos, são apenas funções regulares que usam a associação de gatilho `activityTrigger`. Por exemplo, o aquivo *function.json* para `E2_GetFileList` é semelhante ao seguinte:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

E esta é a implementação:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

> [!NOTE]
> Você deve estar se perguntando por que você não poderia simplesmente colocar esse código diretamente na função de orquestrador. Você poderia, mas isso violaria uma das regras fundamentais das funções de orquestrador, de que elas nunca devem executar E/S, incluindo no caso de acesso ao sistema de arquivos local.

O arquivo *function.json* para `E2_CopyFileToBlob` também é simples:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

A implementação também é bastante simples. Ela usa alguns recursos avançados das associações do Azure Functions (ou seja, o uso do parâmetro `Binder`), mas você não precisa se preocupar com esses detalhes no contexto deste passo a passo.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

A implementação carrega o arquivo no disco e transmite de forma assíncrona o conteúdo para um blob de mesmo nome. O valor retornado é o número de bytes copiados para o armazenamento, que é usado pela função de orquestrador para calcular a soma agregada.

> [!NOTE]
> Este é um exemplo perfeito de movimentação de operações de E/S para uma função `activityTrigger`. Não só o trabalho pode ser distribuído entre várias VMs diferentes, mas você também obtém os benefícios de fazer verificações pontuais do progresso. Se o processo de host for encerrado por algum motivo, você saberá quais carregamentos já foram concluídos.

## <a name="running-the-sample"></a>Executando o exemplo

Usando as funções disparadas por HTTP incluídas no exemplo, você pode iniciar a orquestração usando a seguinte solicitação HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent HTTP/1.1
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A função `HttpStart` que você está invocando funciona somente com conteúdo formatado em JSON. Por esse motivo, o cabeçalho `Content-Type: application/json` é obrigatório e o caminho do diretório é codificado como uma cadeia de caracteres JSON.

Isso disparará o orquestrador `E2_BackupSiteContent` e passará a cadeia de caracteres `D:\home\LogFiles` como um parâmetro. A resposta fornece um link para obter o status dessa operação de backup:

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

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Próximas etapas

Neste ponto, você deve ter uma maior compreensão dos principais recursos de orquestração das Funções Duráveis. Exemplos posteriores abordarão recursos e cenários mais avançados.

> [!div class="nextstepaction"]
> [Executar o exemplo de singleton com estado](durable-functions-counter.md)


