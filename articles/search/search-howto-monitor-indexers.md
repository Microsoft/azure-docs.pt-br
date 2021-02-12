---
title: Monitorar o status e os resultados do indexador
titleSuffix: Azure Cognitive Search
description: Monitore o status, o progresso e os resultados dos indexadores do Azure Pesquisa Cognitiva no portal do Azure, usando a API REST ou o SDK do .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063224"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Como monitorar o status e os resultados do indexador Pesquisa Cognitiva do Azure

Você pode monitorar o processamento do indexador na portal do Azure ou programaticamente por meio de chamadas REST ou de um SDK do Azure. Além do status sobre o próprio indexador, você pode examinar os horários de início e término e erros detalhados e avisos de uma execução específica.

## <a name="monitor-using-azure-portal"></a>Monitorar usando o portal do Azure

Você pode ver o status atual de todos os seus indexadores na página de visão geral do serviço de pesquisa. As páginas do portal são atualizadas a cada poucos minutos, portanto, você não verá evidências de um novo indexador ser executado imediatamente.

   ![Lista de indexadores](media/search-monitor-indexers/indexers-list.png "Lista de indexadores")

| Status | Descrição |
|--------|-------------|
| **Em Andamento** | Indica a execução ativa. O portal relatará informações parciais. À medida que a indexação progride, você pode observar que o valor do **docs Succeeded** cresce em resposta. Os indexadores que processam grandes volumes de dados podem levar muito tempo para serem executados. Por exemplo, indexadores que manipulam milhões de documentos de origem podem ser executados por 24 horas e, em seguida, reiniciam quase imediatamente. O status de indexadores de alto volume pode sempre dizer **em andamento** no Portal. Mesmo quando um indexador estiver em execução, os detalhes estarão disponíveis sobre o progresso em andamento e as execuções anteriores. |
| **Êxito** | Indica que a execução foi bem-sucedida. Uma execução de indexador pode ser bem-sucedida mesmo que documentos individuais tenham erros, se o número de erros for menor que a configuração de **itens de falha máximo** do indexador. |
| **Falha** | O número de erros excedeu o **máximo de itens com falha** e a indexação foi interrompida. |
| **Redefinir** | O estado interno de controle de alterações do indexador foi redefinido. O indexador será executado de forma completa, atualizando todos os documentos e não apenas aqueles com carimbos de data/hora mais recentes. |

Você pode clicar em um indexador na lista para ver mais detalhes sobre as execuções atuais e recentes do indexador.

   ![Resumo do indexador e histórico de execução](media/search-monitor-indexers/indexer-summary.png "Resumo do indexador e histórico de execução")

O gráfico de **Resumo do indexador** exibe um grafo do número de documentos processados em suas execuções mais recentes.

A lista **detalhes da execução** mostra até 50 dos resultados de execução mais recentes. Clique em um resultado de execução na lista para ver as informações específicas sobre essa execução. Isso inclui os horários de início e término e quaisquer erros e avisos ocorridos.

   ![Detalhes de execução do indexador](media/search-monitor-indexers/indexer-execution.png "Detalhes de execução do indexador")

Se houver problemas específicos ao documento durante a execução, eles serão listados nos campos erros e avisos.

   ![Detalhes do indexador com erros](media/search-monitor-indexers/indexer-execution-error.png "Detalhes do indexador com erros")

Os avisos são comuns com alguns tipos de indexadores e nem sempre indicam um problema. Por exemplo indexadores que usam serviços cognitivas podem relatar avisos quando arquivos de imagem ou PDF não contêm nenhum texto para processar. 

Para obter mais informações sobre como investigar erros e avisos do indexador, consulte [Solucionando problemas comuns do indexador no Azure pesquisa cognitiva](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Monitorar usando o status obter indexador (API REST)

Você pode recuperar o status e o histórico de execução de um indexador usando o [comando obter status do indexador](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

A resposta contém o status geral do indexador, a última invocação (ou em andamento) do indexador e o histórico de invocações recentes do indexador.

```output
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
```

O histórico de execução contém até as 50 execuções mais recentes, que são classificadas em ordem cronológica inversa (mais recente primeiro).

Observe que há dois valores de status diferentes. O status de nível superior é para o indexador em si. Um status de indexador de **em execução** significa que o indexador está configurado corretamente e disponível para execução, mas não está em execução no momento.

Cada execução do indexador também tem seu próprio status que indica se a execução específica está em andamento (**em execução**) ou já concluída com o status **êxito**, **transientFailure** ou **persistentFailure** . 

Quando um indexador é redefinido para atualizar seu estado de controle de alterações, uma entrada de histórico de execução separada é adicionada com um status de **redefinição** .

Para obter mais detalhes sobre códigos de status e dados de monitoramento do indexador, consulte [obter status do indexador](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>Monitorar usando o .NET

Usando o SDK do .NET Pesquisa Cognitiva do Azure, o exemplo de C# a seguir grava informações sobre o status de um indexador e os resultados de sua execução mais recente (ou contínua) no console.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

A saída no console terá uma aparência semelhante a esta:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Observe que há dois valores de status diferentes. O status de nível superior é o status do próprio indexador. Um status de indexador de **em execução** significa que o indexador está configurado corretamente e disponível para execução, mas não está em execução no momento.

Cada execução do indexador também tem seu próprio status para se a execução específica está em andamento (**em execução**) ou já foi concluída com um status de **êxito** ou **TransientError** . 

Quando um indexador é redefinido para atualizar seu estado de controle de alterações, uma entrada de histórico separada é adicionada com um status de **redefinição** .

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre códigos de status e informações de monitoramento do indexador, consulte a seguinte referência de API:

* [GetIndexerStatus (API REST)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)