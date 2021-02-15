---
title: Agendar execução do indexador
titleSuffix: Azure Cognitive Search
description: Agende indexadores do Azure Pesquisa Cognitiva para indexar conteúdo periodicamente ou em horários específicos.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097969"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Como agendar indexadores no Azure Pesquisa Cognitiva

Um indexador normalmente é executado uma vez, imediatamente após sua criação. Posteriormente, você poderá executá-lo novamente sob demanda usando portal do Azure, [executar o indexador (REST)](/rest/api/searchservice/run-indexer)ou um SDK do Azure. Como alternativa, você também pode configurar um indexador para ser executado em um agendamento. Algumas situações em que o agendamento do indexador é útil incluem:

* Os dados de origem serão alterados ao longo do tempo e você deseja que o indexador de pesquisa processe automaticamente a diferença.

* Os dados de origem são muito grandes e você deseja espalhar o processamento do indexador ao longo do tempo. Os trabalhos do indexador estão sujeitos a um tempo máximo de execução de 24 horas para fontes de dados regulares e 2 horas para indexadores com habilidades. Se a indexação não puder ser concluída dentro do intervalo máximo, você poderá configurar uma agenda que seja executada a cada 2 horas. Os indexadores podem selecionar automaticamente onde saíram, como evidenciado por uma marca d' água alta interna que marca onde a indexação foi encerrada pela última vez. A execução de um indexador em uma agenda de 2 horas recorrente permite processar um conjunto de dados muito grande (muitos milhões de documentos) Além do intervalo permitido para um único trabalho. Para obter mais informações sobre como indexar grandes volumes de dados, consulte [como indexar grandes conjuntos de dados no Azure pesquisa cognitiva](search-howto-large-index.md).

* Um índice de pesquisa será preenchido de várias fontes de dados e você deseja que os indexadores sejam executados em momentos diferentes para reduzir os conflitos.

Visualmente, um agendamento pode ser semelhante ao seguinte: a partir de 1º de Janeiro e a execução a cada 50 minutos.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> O Agendador é um recurso interno do Azure Pesquisa Cognitiva. Não há suporte para agendadores externos.

## <a name="schedule-property"></a>Propriedade de agendamento

Uma agenda faz parte da definição do indexador. Se a propriedade **Schedule** for omitida, o indexador será executado apenas uma vez logo após sua criação. Se você adicionar uma propriedade de **agenda** , você especificará duas partes.

| Propriedade | Descrição |
|----------|-------------|
|**Intervalo** | necessária A quantidade de tempo entre o início de duas execuções de indexador consecutivas. O menor intervalo permitido é de 5 minutos e o mais longo é de 1440 minutos (24 horas). Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. <br/><br/>Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.|
| **Hora de início (UTC)** | adicional Indica quando as execuções agendadas devem começar. Se omitido, a hora UTC atual será usada. Esse tempo pode estar no passado, caso em que a primeira execução é agendada como se o indexador fosse executado continuamente desde o **iníciotime** original.<br/><br/>Exemplos: `2021-01-01T00:00:00Z` começando à meia-noite em 1º de Janeiro, `2021-01-05T22:28:00Z` começando às 10:28 p.m. em 5 de Janeiro.|

## <a name="scheduling-behavior"></a>Comportamento do agendamento

Só é possível ocorrer uma execução de um indexador por vez. Se um indexador já estiver em execução quando a próxima execução for agendada, essa execução será adiada até a próxima hora agendada.

Vamos considerar um exemplo para tornar isso mais concreto. Suponha que configuramos uma agenda do indexador com um **intervalo** de hora em horário e uma **hora de início** de 1º de junho de 2019 às 8:00:00 am UTC. Veja o que poderia acontecer quando uma execução de indexador demorar mais do que uma hora:

* A primeira execução do indexador começa em 1º de junho de 2019 às 8:00 AM UTC. Vamos supor que essa execução demore 20 minutos (ou menos de uma hora).
* A segunda execução começa em 1º de junho de 2019 9:00 AM UTC. Suponha que essa execução leva 70 minutos – mais de uma hora – e não será concluída até 10:10 AM UTC.
* A terceira execução está agendada para iniciar às 10:00 AM UTC, mas, nesse momento, a execução anterior ainda está em execução. Essa execução agendada é ignorada. A próxima execução do indexador não será iniciada até 11:00 AM UTC.

> [!NOTE]
> Se um indexador for definido como um determinado agendamento, mas falhar repetidamente no mesmo documento a cada vez, o indexador começará a ser executado em um intervalo menos frequente (até o intervalo máximo de pelo menos uma vez a cada 24 horas) até que ele faça o andamento novamente. Se você acreditar que corrigiu qualquer problema subjacente, poderá executar o indexador manualmente e, se a indexação for bem sucedido, o indexador retornará ao seu agendamento regular.

## <a name="schedule-using-rest"></a>Agendar usando REST

Especifique a propriedade de **agendamento** ao criar ou atualizar o indexador.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Agendar usando o .NET

O exemplo C# a seguir cria um indexador de banco de dados SQL do Azure, usando uma fonte e um índice predefinidos e define sua agenda para ser executada uma vez por dia, iniciando agora:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

O agendamento é definido usando a classe [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) , quando você cria ou atualiza um indexador usando o [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). O construtor **IndexingSchedule** requer um parâmetro de **intervalo** especificado usando um objeto **TimeSpan** . Conforme observado anteriormente, o menor valor de intervalo permitido é de 5 minutos e o maior é de 24 horas. O segundo parâmetro **StartTime** , especificado como um objeto **DateTimeOffset** , é opcional.

## <a name="next-steps"></a>Próximas etapas

Para indexadores que são executados em um agendamento, você pode monitorar operações recuperando o status do serviço de pesquisa ou obter informações detalhadas habilitando o log de diagnóstico.

* [Monitorar o status do indexador de pesquisa](search-howto-monitor-indexers.md)
* [Coletar e analisar dados de log](search-monitor-logs.md)