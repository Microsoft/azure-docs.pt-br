---
title: Como monitorar o status do indexador e resultados - Azure Search
description: Monitore o status, progresso e resultados de indexadores de Azure Search no portal do Azure, usando a API REST ou o SDK do .NET.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486279"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Como monitorar os resultados e status do indexador de Azure Search

O Azure Search fornece o status e informações sobre as execuções atuais e históricas de cada indexador de monitoramento.

Monitoramento de indexador é útil quando você deseja:

* Execute a acompanhar o progresso de um indexador durante uma em andamento.
* Examine os resultados da execução de indexador em andamento ou anterior.
* Indexador de nível superior de identificar erros e erros ou avisos sobre documentos individuais que está sendo indexados.

## <a name="find-indexer-status-and-history-details"></a>Localizar detalhes de status e o histórico de indexador

Você pode acessar informações de monitoramento do indexador de várias maneiras, incluindo:

* No [Portal do Azure](#portal)
* Usando o [API REST](#restapi)
* Usando o [do SDK do .NET](#dotnetsdk)

Informações de monitoramento de indexador disponível inclui todos os itens a seguir (embora os formatos são diferentes de dados com base no método de acesso usado):

* Informações de status sobre o próprio indexador
* Informações sobre a mais recente execução do indexador, incluindo seu status, iniciar e horários de término e erros e avisos detalhados.
* Uma lista de execuções do indexador históricos e seus status, resultados, erros e avisos.

Indexadores que processam grandes volumes de dados podem demorar muito para ser executado. Por exemplo, indexadores que lidar com milhões de documentos de origem podem executado durante 24 horas e, em seguida, reiniciar quase imediatamente. O status de alto volume indexadores sempre pode dizer **em andamento** no portal. Mesmo quando um indexador é executado, detalhes estão disponíveis sobre o progresso em andamento e as execuções anteriores.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>Indexadores de monitor no portal

Você pode ver o status atual de seu indexadores em todos os **indexadores** lista na página de visão geral do serviço de pesquisa.

   ![Lista de indexadores](media/search-monitor-indexers/indexers-list.png "lista de indexadores")

Quando um indexador está em execução, o status na lista mostra **em andamento**e o **Docs bem-sucedida** valor mostra o número de documentos processados até o momento. Pode levar alguns minutos para que o portal para atualizar valores de status do indexador e contagens de documentos.

Um indexador cuja execução mais recente foi bem-sucedida mostra **sucesso**. Execução de um indexador pode ser bem-sucedida mesmo que documentos individuais têm erros, se o número de erros for menor que o indexador **máximo de itens com falha** configuração.

Se a execução mais recente encerrado com um erro, o status mostra **Failed**. Um status de **redefinir** significa que o estado de controle de alterações do indexador foi redefinida.

Clique em um indexador na lista para ver mais detalhes sobre o indexador atuais e recentes é executado.

   ![Histórico de execução e o resumo do indexador](media/search-monitor-indexers/indexer-summary.png "histórico de execução e o resumo do indexador")

O **resumo de indexador** gráfico exibe um gráfico do número de documentos processados em suas execuções mais recentes.

O **detalhes de execução** lista mostra até 50 dos resultados da execução mais recentes.

Clique em um resultado de execução na lista para ver os detalhes sobre o que são executados. Isso inclui seu início e término e erros e avisos que ocorreram.

   ![Detalhes de execução do indexador](media/search-monitor-indexers/indexer-execution.png "detalhes de execução do indexador")

Se houver problemas específicos ao documento durante a execução, eles serão listados nos campos de erros e avisos.

   ![Detalhes de indexador com erros](media/search-monitor-indexers/indexer-execution-error.png "detalhes de indexador com erros")

Avisos comuns com alguns tipos de indexadores e nem sempre indica um problema. Por exemplo indexadores que usam os serviços cognitivos podem relatar avisos quando a imagem ou arquivos PDF não contêm qualquer texto para processar.

Para obter mais informações sobre como investigar o indexador erros e avisos, consulte [solução de problemas comuns do indexador no Azure Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>Indexadores de monitor usando a API REST

Você pode recuperar o histórico de execução e do status de um indexador usando a [comando obter Status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A resposta contém o status geral do indexador, a última invocação (ou em andamento) do indexador e o histórico de invocações recentes do indexador.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Histórico de execução contém até as 50 execuções mais recentes, que são classificadas em ordem cronológica inversa (mais recentes primeiro).

Observe que há dois valores de status diferente. O status de nível superior é para o próprio indexador. Um status do indexador de **executando** significa que o indexador está configurado corretamente e disponível para ser executado, mas não que ele da atualmente em execução.

Cada execução do indexador também tem seu próprio status que indica se essa execução específica está em andamento (**em execução**), ou já foi concluída com um **sucesso**, **transientFailure**, ou **persistentFailure** status. 

Quando um indexador é redefinido para atualizar seu estado de controle de alterações, uma entrada de histórico de execução separado é adicionada com um **redefinir** status.

Para obter mais detalhes sobre códigos de status e os dados de monitoramento do indexador, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>Indexadores de monitor usando o SDK do .NET

Você pode definir a agenda para um indexador usando o SDK do .NET do Azure Search. Para fazer isso, inclua o **agendamento** propriedade ao criar ou atualizar um indexador.

O seguinte C# exemplo grava informações sobre o status do indexador e os resultados de suas mais recentes (ou em andamento) executados no console.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

A saída do console será algo parecido com isso:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Observe que há dois valores de status diferente. O status de nível superior é o status do indexador em si. Um status do indexador de **executando** significa que o indexador está configurado corretamente e está disponível para execução, mas não que ele está em execução atualmente.

Cada execução do indexador também tem seu próprio status para se essa execução específica está em andamento (**em execução**), ou já foi concluída com um **sucesso** ou **TransientError** status. 

Quando um indexador é redefinido para atualizar seu estado de controle de alterações, uma entrada de histórico separada é adicionada com um **redefinir** status.

Para obter mais detalhes sobre códigos de status e informações de monitoramento do indexador, consulte [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) na API REST.

Detalhes sobre erros específicos do documento ou avisos podem ser recuperados, enumerando as listas `IndexerExecutionResult.Errors` e `IndexerExecutionResult.Warnings`.

Para obter mais informações sobre as classes do SDK do .NET usado para monitorar os indexadores, consulte [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) e [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
