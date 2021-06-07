---
title: Saídas de Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis para Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 12/9/2020
ms.openlocfilehash: 3ce4f673657561e196520466b569d0cf83d75a8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019339"
---
# <a name="outputs-from-azure-stream-analytics"></a>Saídas de Azure Stream Analytics

Um trabalho do Azure Stream Analytics consiste em uma entrada, uma consulta e uma saída. Há vários tipos de saída para os quais você pode enviar dados transformados. Este artigo lista as saídas de Stream Analytics com suporte. Quando você criar sua consulta do Stream Analytics, consulte o nome de saída usando a [cláusula INTO](/stream-analytics-query/into-azure-stream-analytics). Você pode usar uma única saída por trabalho ou várias saídas por trabalho de streaming (se precisar delas) adicionando várias cláusulas INTO à consulta.

Para criar, editar e testar as saídas de trabalho do Stream Analytics, você pode usar o [Porta do Azure](stream-analytics-quick-create-portal.md#configure-job-output), o [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), o [.NET API](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations), o [REST API](/rest/api/streamanalytics/) e o [Visual Studio](stream-analytics-quick-create-vs.md).

Alguns tipos de saídas dão suporte a [particionamento](#partitioning) e os [tamanhos de lote de saída](#output-batch-size) variam para otimizar a taxa de transferência. A tabela a seguir mostra os recursos que têm suporte para cada tipo de saída:

| Tipo de saída | Particionamento | Segurança | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Sim|Usuário do Active Directory do Azure </br> , Identidade gerenciada|
|[Banco de Dados SQL do Azure](sql-database-output.md)|Sim, opcional.|Autenticação de usuário do SQL, </br> Identidade Gerenciada (versão prévia)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Sim|Autenticação de usuário do SQL, </br> Identidade Gerenciada (versão prévia)|
|[Armazenamento de BLOBs e Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|Sim|Chave de acesso, </br> Identidade Gerenciada (versão prévia)|
|[Hubs de eventos do Azure](event-hubs-output.md)|Sim, é necessário definir a coluna de chave de partição na configuração de saída.|Chave de acesso, </br> Identidade Gerenciada (versão prévia)|
|[Power BI](power-bi-output.md)|Não|Azure Active Directory usuário, </br> Identidade Gerenciada|
|[Armazenamento de Tabelas do Azure](table-storage-output.md)|Sim|Chave de conta|
|[Filas do Barramento de Serviço do Azure](service-bus-queues-output.md)|Sim|Chave de acesso|
|[Tópicos do barramento de serviço do Azure](service-bus-topics-output.md)|Sim|Chave de acesso|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Sim|Chave de acesso|
|[Azure Functions](azure-functions-output.md)|Sim|Chave de acesso|

## <a name="partitioning"></a>Particionamento

Stream Analytics dá suporte a partições para todas as saídas, exceto para Power BI. Para obter mais informações sobre chaves de partição e o número de gravadores de saída, consulte o artigo para o tipo de saída específico em que você está interessado. Todos os artigos de saída são vinculados na seção anterior.  

Além disso, para um ajuste mais avançado das partições, o número de gravadores de saída pode ser controlado usando uma `INTO <partition count>` cláusula (consulte [into](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) em sua consulta, o que pode ser útil para atingir uma topologia de trabalho desejada. Se o adaptador de saída não estiver particionado, a falta de dados em uma partição de entrada causará um atraso até o período de chegada atrasado. Nesses casos, a saída é mesclada em um único gravador, o que pode causar gargalos no pipeline. Para saber mais sobre a política de chegada tardia, consulte [Considerações sobre a ordem de evento do Azure Stream Analytics](./stream-analytics-time-handling.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída

Todas as saídas dão suporte ao envio em lote, mas apenas alguns tamanhos de lote de suporte são explicitamente. O Azure Stream Analytics usa lotes de tamanho variável para processar eventos e gravar em saídas. Normalmente, o mecanismo do Stream Analytics não grava uma mensagem por vez e usa lotes para maior eficiência. Quando a taxa de eventos de entrada e saída estiver alta, o Stream Analytics usa lotes maiores. Quando a taxa de saída é baixa, ele usa lotes menores para manter a latência baixa.

## <a name="parquet-output-batching-window-properties"></a>Propriedades da janela de envio em lote de saída do parquet

Ao usar a implantação de modelo do Azure Resource Manager ou a API REST, as duas propriedades da janela de envio em lote são:

1. *timeWindow*

   O tempo máximo de espera por lote. O valor deve ser uma cadeia de caracteres de TimeSpan. Por exemplo, "00:02:00" por dois minutos. Após esse período, o lote será gravado na saída mesmo se o requisito de linhas mínimas não for atendido. O valor padrão é 1 minuto e o máximo permitido é de 2 horas. Se a saída de blobs tiver a frequência de padrão do caminho, o tempo de espera não poderá ser maior que o intervalo de tempo da partição.

2. *sizeWindow*

   O número mínimo de linhas por lote. Para parquet, todo lote cria um novo arquivo. O valor padrão atual é de 2.000 linhas, e o máximo permitido é de 10.000 linhas.

Essas propriedades da janela de envio em lote são suportadas apenas pela versão **de API 2017-04-01-Preview**. Abaixo está um exemplo da carga JSON para uma chamada à API REST:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>
> [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
