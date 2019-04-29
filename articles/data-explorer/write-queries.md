---
title: Escrever consultas para o Azure Data Explorer
description: Neste passo a passo, você aprenderá como executar consultas básicas e mais avançadas para o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: b1a7e64cf6b85b517bc027d6541d63c9be729734
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773971"
---
# <a name="write-queries-for-azure-data-explorer"></a>Escrever consultas para o Azure Data Explorer

Neste artigo, você aprenderá a usar a linguagem de consulta no Azure Data Explorer para executar consultas básicas com os operadores mais comuns. Você também poderá entrar em contato com alguns dos recursos mais avançados da linguagem.

## <a name="prerequisites"></a>Pré-requisitos

Você pode executar as consultas neste artigo em uma de duas maneiras:

- No *cluster de ajuda* do Azure Data Explorer, que configuramos para ajudar na aprendizagem.
    [Entre no cluster](https://dataexplorer.azure.com/clusters/help/databases/samples) com uma conta de email organizacional que seja membro do Azure Active Directory.

- Em seu próprio cluster, que inclui os dados de exemplo do StormEvents. Para obter mais informações, consulte [Início rápido: Criar um banco de dados e cluster do Azure Data Explorer](create-cluster-database-portal.md) e [Ingerir dados de exemplo no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Visão geral da linguagem de consulta

Uma linguagem de consulta no Azure Data Explorer é uma solicitação somente leitura para processar dados e retornar resultados. A solicitação é declarada em texto sem formatação, usando um modelo de fluxo de dados projetado para facilitar a criação, a leitura e a automatização da sintaxe. A consulta usa entidades de esquema que são organizadas em uma hierarquia semelhante à do SQL: bancos de dados, tabelas e colunas.

A consulta consiste em uma sequência de instruções de consulta, delimitadas por ponto e vírgula (`;`), com pelo menos uma instrução sendo uma instrução de expressão de tabela, que é uma instrução que produz dados organizados em uma malha de colunas e linhas em formato de tabela. As instruções de expressão de tabela da consulta produzem os resultados da consulta.

A sintaxe da instrução de expressão de tabela tem um fluxo de dados de tabela de um operador de consulta de tabela para outro, começando com a fonte de dados (por exemplo, uma tabela em um banco de dados ou um operador que produz dados) e, em seguida, passando por um conjunto de operadores de transformação de dados que são associados com o uso do delimitador de barra vertical (`|`).

Por exemplo, a consulta a seguir tem uma única instrução, que é uma instrução de expressão de tabela. A instrução inicia com uma referência a uma tabela chamada `StormEvents` (o banco de dados que hospeda essa tabela é implícito aqui e é parte das informações de conexão). Os dados (linhas) para a tabela são então filtrados pelo valor da coluna `StartTime` e, em seguida, filtrados pelo valor da coluna `State`. A consulta retorna então a contagem de linhas "sobreviventes".

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

Nesse caso, o resultado é:

|Contagem|
|-----|
|   23|
| |

Para obter mais informações, veja a [Referência da linguagem de consulta](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>Operadores mais comuns

Os operadores abordados nesta seção são os blocos de construção para se compreender consultas no Azure Data Explorer. A maioria das consultas que você escreve incluirão vários desses operadores.

Para executar consultas em um cluster de ajuda, selecione **Clique para executar a consulta** acima de cada consulta.

Para executar consultas em seu próprio cluster:

1. Copie cada consulta para o aplicativo de consulta baseado na Web e selecione a consulta ou coloque o cursor sobre a consulta.

1. Na parte superior do aplicativo, selecione **Executar**.

### <a name="count"></a>count

[**count**](https://docs.microsoft.com/azure/kusto/query/countoperator): Retorna a contagem de linhas na tabela.

A consulta a seguir retorna a contagem de linhas na tabela do StormEvents.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>take

[**take**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Retorna até o número especificado de linhas de dados.

A consulta a seguir retorna cinco linhas da tabela do StormEvents. A palavra-chave *limit* é um alias para *take*.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Não há nenhuma garantia de quais registros serão retornados, a menos que os dados de origem sejam classificados.

### <a name="project"></a>project

[**project**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Seleciona um subconjunto de colunas.

A consulta a seguir retorna um conjunto específico de colunas.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>onde

[**where**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Filtra uma tabela para o subconjunto de linhas que satisfazem a um predicado.

A consulta a seguir filtra os dados por `EventType` e `State`.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sort

[**sort**](https://docs.microsoft.com/azure/kusto/query/sortoperator): Classifica as linhas da tabela de entrada em ordem de acordo com uma ou mais colunas.

A consulta a seguir classifica os dados em ordem decrescente por `DamageProperty`.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> A ordem das operações é importante. Tente colocar `take 5` antes de `sort by`. Você obtém resultados diferentes?

### <a name="top"></a>top

[**top**](https://docs.microsoft.com/azure/kusto/query/topoperator): Retorna os primeiros registros *N* classificados pelas colunas especificadas.

A consulta a seguir retorna os mesmos resultados que aquela acima, com um operador a menos.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>extend

[**extend**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Computa colunas derivadas.

A consulta a seguir cria uma nova coluna computando um valor em cada linha.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

As expressões podem incluir todos os operadores comuns (+, -, *, /, %) e há uma variedade de funções úteis que você pode chamar.

### <a name="summarize"></a>summarize

[**summarize**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Agrega grupos de linhas.

A consulta a seguir retorna a contagem de eventos por `State`.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

O operador **summarize** agrupa linhas que têm os mesmos valores na cláusula **by** e, em seguida, usa a função de agregação (assim como **count**) para combinar cada grupo em uma única linha. Portanto, nesse caso, há uma linha para cada estado e uma coluna para a contagem de linhas nesse estado.

Há uma variedade de funções de agregação e você pode usar várias delas em um operador **summarize** para produzir várias colunas computadas. Por exemplo, você pode obter a contagem de tempestades em cada estado e o número exclusivo de tempestades por estado, então use **top** para obter os estados mais afetados por tempestades.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

O resultado de uma operação **summarize** tem:

- Cada coluna nomeada em **by**

- Uma coluna para cada expressão computada

- Uma linha para cada combinação de valores by

### <a name="render"></a>render

[**render**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Renderiza os resultados como uma saída gráfica.

A consulta a seguir exibe um gráfico de colunas.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

A consulta a seguir exibe um gráfico de tempo simples.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

A consulta a seguir conta os eventos pelo módulo de tempo de um dia (compartimentalizado em horas) e exibe um gráfico de tempo.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

A consulta a seguir compara várias séries diárias em um gráfico de tempo.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> O operador **render** é um recurso do lado do cliente, e não parte do mecanismo. Ele é integrado à linguagem de programação para facilidade de uso. O aplicativo Web dá suporte às seguintes opções: gráfico de barras, gráfico de colunas, gráfico de pizza, gráfico de tempo e gráfico de linhas. 

## <a name="scalar-operators"></a>Operadores escalares

Esta seção aborda alguns dos operadores escalares mais importantes.

### <a name="bin"></a>bin()

[**bin()**](https://docs.microsoft.com/azure/kusto/query/binfunction): Arredonda os valores até um número inteiro múltiplo de um determinado tamanho do compartimento.

A consulta a seguir calcula a contagem com um tamanho de bucket de um dia.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>case()

[**case()**](https://docs.microsoft.com/azure/kusto/query/casefunction): Avalia uma lista de predicados e retorna a primeira expressão de resultado cujo predicado seja satisfeito, ou então a última expressão **else**. Você pode usar esse operador para categorizar ou agrupar dados:

A consulta a seguir retorna uma nova coluna `deaths_bucket` e agrupa as mortes pelo número.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extract()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): Obtém uma correspondência para uma expressão regular a partir de uma cadeia de caracteres de texto.

A consulta a seguir extrai valores de atributo específicos de um rastreamento.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Essa consulta usa uma instrução **let**, que associa um nome (neste caso, `MyData`) a uma expressão. Para o restante do escopo, na qual a instrução **let** aparece (escopo global ou em um escopo de corpo de função), o nome pode ser usado para se referir a seu valor associado.

### <a name="parsejson"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Interpreta uma cadeia de caracteres como um valor JSON e retorna o valor como dinâmico. É superior ao uso da função **extractjson()** quando você precisa extrair mais de um elemento de um objeto JSON composto.

A consulta a seguir extrai os elementos JSON de uma matriz.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

A consulta a seguir extrai os elementos JSON.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

A consulta a seguir extrai os elementos JSON com um tipo de dados dinâmicos.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction): Subtrai o período fornecido da hora atual UTC.

A consulta a seguir retorna os dados das últimas 12 horas.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): Retorna o início da semana contendo a data, alterada por um deslocamento, se fornecido

A consulta a seguir retorna o início da semana com deslocamentos diferentes.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Essa consulta usa o operador **range**, que gera uma tabela de valores de coluna única. Veja também: [**startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**startofyear()**](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [**startofmonth()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek()**](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction) e [**endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>between()

[**between()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Corresponde à entrada que está dentro do intervalo inclusivo.

A consulta a seguir filtra os dados por um determinado intervalo de datas.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

A consulta a seguir filtra os dados por um determinado intervalo de datas, com a pequena variação de três dias (`3d`) da data de início.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Operadores de tabela

O Kusto tem muitos operadores de tabela, alguns dos quais são abordados em outras seções deste artigo. Aqui, nos concentraremos no **parse**. 

### <a name="parse"></a>analisar

[**parse**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Avalia uma expressão de cadeia de caracteres e analisa seu valor em uma ou mais colunas calculadas. Há três maneiras de analisar: simples (o padrão), regex e reduzida.

A consulta a seguir analisa um rastreamento e extrai os valores relevantes, usando um padrão de análise simples. A expressão (conhecida como StringConstant) é um valor de cadeia de caracteres regular e a correspondência é estrita: colunas estendidas devem corresponder aos tipos necessários.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

A consulta a seguir analisa um rastreamento e extrai os valores relevantes, usando `kind = regex`. A StringConstant pode ser uma expressão regular.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

A consulta a seguir analisa um rastreamento e extrai os valores relevantes, usando `kind = relaxed`. A StringConstant é um valor de cadeia de caracteres regular e a correspondência é ampla: colunas estendidas podem corresponder parcialmente aos tipos necessários.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Análise de série temporal

### <a name="make-series"></a>make-series

[**make-series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): agrega os grupos de linhas assim como ocorre com [summarize](https://docs.microsoft.com/azure/kusto/query/summarizeoperator), mas gera um vetor de série (temporal) por cada combinação de valores de by.

A consulta a seguir retorna um conjunto de série temporal para a contagem de eventos de tempestade diários. A consulta abrange um período de três meses para cada estado, preenchendo os compartimentos ausentes com a constante 0:

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Depois de criar um conjunto de série (temporal), você pode aplicar funções de série para detectar formas anômalas, padrões sazonais e muito mais.

A consulta a seguir extrai os primeiros três estados que tinham mais eventos em um dia específico:

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Para obter mais informações, examine a lista completa das [funções de série](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Agregações avançadas

Abordamos agregações básicas, tais como **count** e **summarize**, anteriormente neste artigo. Esta seção apresenta opções mais avançadas.

### <a name="top-nested"></a>top-nested

[**top-nested**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Produz resultados em posição superior na hierarquia, em que cada nível é uma busca detalhada baseada nos valores do nível anterior.

Esse operador é útil para cenários de visualização do painel, ou quando for necessário responder a uma pergunta semelhante à seguinte: "Encontrar os valores superiores N de K1 (usando alguma agregação); para cada um deles, encontrar quais são os valores superiores M de K2 (usando outra agregação); ..."

A consulta a seguir retorna uma tabela hierárquica com `State` no nível superior, seguido por `Sources`.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>Plug-in pivot()

[**pivot() plugin**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): Gira uma tabela, transformando os valores exclusivos de uma coluna na tabela de entrada em várias colunas na tabela de saída. O operador executa agregações nos locais em que elas são necessárias, em quaisquer valores de coluna remanescentes na saída final.

A consulta a seguir aplica um filtro e inverte as linhas em colunas.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Retorna uma estimativa do número de valores distintos de uma expressão no grupo. Use [**count()**](https://docs.microsoft.com/azure/kusto/query/countoperator) para contar todos os valores.

A consulta a seguir conta `Source` distintos por `State`.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Retorna uma estimativa do número de valores distintos da expressão para linhas para as quais o predicado é avaliado como true.

A consulta a seguir conta os valores distintos de `Source` em que `DamageProperty < 5000`.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcounthll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Calcula os resultados **dcount** de HyperLogLog (gerados por [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) ou [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

A consulta a seguir usa o algoritmo HLL para gerar a contagem.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="argmax"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Localiza uma linha no grupo que maximiza uma expressão, além de retornar o valor de outra expressão (ou * para retornar a linha inteira).

A consulta a seguir retorna a hora do último relatório de inundação em cada estado.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Retorna uma matriz dinâmica (JSON) do conjunto de valores distintos que uma expressão usa no grupo.

A consulta a seguir retorna todos os horários em que uma inundação foi relatada por cada estado e cria uma matriz com base no conjunto de valores distintos.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expand

[**mv-expand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Expande coleções com vários valores de uma coluna digitada de modo dinâmico, para que cada valor na coleção obtenha uma linha separada. Todas as outras colunas em uma linha expandida são duplicadas. É o oposto de makelist.

A consulta a seguir gera dados de exemplo criando um conjunto e, em seguida, usá-lo para demonstrar a **mv-expanda** recursos.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Retorna uma estimativa especificado para o  [**percentil da classificação mais próxima**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) da população, definido por uma expressão. A precisão depende da densidade da população na região do percentil. Pode ser usado apenas no contexto de agregação dentro de [**summarize**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

A consulta a seguir calcula percentis de duração da tempestade.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

A consulta a seguir calcula percentis de duração da tempestade por estado e normaliza os dados por compartimentos de cinco minutos (`5m`).

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Entre conjuntos de dados

Esta seção aborda os elementos que permitem que você crie consultas mais complexas, una dados em tabelas e realize consultas entre bancos de dados e clusters.

### <a name="let"></a>let

[**let**](https://docs.microsoft.com/azure/kusto/query/letstatement): Melhora a modularidade e a reutilização. A instrução **let** permite dividir uma expressão potencialmente complexa em várias partes, cada uma associada a um nome, e compor essas partes conjuntamente. Uma instrução **let** também pode ser usada para criar funções definidas pelo usuário e exibições (expressões em tabelas cujos resultados serão semelhantes a uma nova tabela). Expressões associadas por uma instrução **let** podem ser do tipo escalar, do tipo de tabela, ou então uma função definida pelo usuário (lambdas).

O exemplo a seguir cria uma variável do tipo de tabela e usa-o em uma expressão subsequente.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**join**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Mescla as linhas das duas tabelas para formar uma nova tabela, correspondendo os valores das colunas especificadas de cada tabela. O Kusto dá suporte a uma gama completa de tipos de join: **fullouter**, **inner**, **innerunique**, **leftanti**, **leftantisemi**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter**, **rightsemi**.

O exemplo a seguir une duas tabelas com uma junção interna (inner).

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> Use os operadores **where** e **project** para reduzir o número de linhas e colunas nas tabelas de entrada antes da junção. Se uma tabela sempre for menor do que a outra, use-a como o lado esquerdo (com barras verticais) da junção. As colunas para a correspondência de junção devem ter o mesmo nome. Use o operador **project** se for necessário renomear uma coluna em uma das tabelas.

### <a name="serialize"></a>serialize

[**serialize**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Serializa o conjunto de linhas, para possibilitar o uso de funções que exigem dados serializados, tais como **row_number()**.

A consulta a seguir tem êxito porque os dados são serializados.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

O conjunto de linhas também é considerado como serializado se ele é um resultado de: **sort**, **superior**, ou **intervalo** operadores, opcionalmente seguidos por **projeto**, **project-away**, **estender**, **onde**, **analisar**, **mv-expanda**, ou **levar** operadores.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Consultas entre bancos de dados e entre clusters

[Consultas entre bancos de dados e entre clusters](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): É possível consultar um banco de dados no mesmo cluster fazendo referência a ele como `database("MyDatabase").MyTable`. Você pode consultar um banco de dados em um cluster remoto fazendo referência a ele como `cluster("MyCluster").database("MyDatabase").MyTable`.

A consulta a seguir é chamada de um cluster e consulta dados do cluster `MyCluster`. Para executar essa consulta, use seu próprio nome de cluster e nome do banco de dados.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Análise do usuário

Esta seção inclui os elementos e consultas que demonstram o quanto é fácil analisar os comportamentos do usuário no Kusto.

### <a name="activitycountsmetrics-plugin"></a>Plug-in activity_counts_metrics

[**Plug-in activity_counts_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Calcula métricas de atividade úteis (valores de contagem total, valores de contagem distinta, contagem distinta de novos valores e contagem distinta agregada). As métricas são calculadas para cada janela de tempo, então elas são comparadas e agregadas com todas as janelas de tempo anteriores.

A consulta a seguir analisa a adoção do usuário, calculando as contagens de atividade diárias.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activityengagement-plugin"></a>Plug-in activity_engagement

[**Plug-in activity_engagement**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): Calcula a taxa de envolvimento de atividade com base na coluna de ID em uma janela de linha do tempo deslizante. **O plug-in activity_engagement** pode ser usado para calcular DAU, WAU e MAU (usuários ativos diários, semanais e mensais).

A consulta a seguir retorna a proporção de usuários distintos totais usando um aplicativo diariamente em relação ao total de usuários distintos usando-o semanalmente, em uma janela móvel de sete dias.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Ao calcular MAU/DAU, altere a data final e o período da janela móvel (OuterActivityWindow).

### <a name="activitymetrics-plugin"></a>Plug-in activity_metrics

[**Plug-in activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Calcula métricas úteis de atividade (valores de contagem distintos, contagem distinta de novos valores, taxa de retenção e taxa de rotatividade) com base na janela do período atual versus a janela do período anterior.

A consulta a seguir calcula a taxa de variação e de retenção para um determinado conjunto de dados.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="newactivitymetrics-plugin"></a>Plug-in new_activity_metrics

[**Plug-in new_activity_metrics**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): Calcula métricas úteis de atividade (valores de contagem distintos, contagem distinta de novos valores, taxa de retenção e taxa de rotatividade) para o coorte de novos usuários. O conceito desse plug-in é semelhante ao do [**plug-in activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), mas ele se concentra em novos usuários.

A consulta a seguir calcula uma taxa de retenção e de variação com uma janela de semana a semana para o novo corte de usuários (usuários que chegaram na primeira semana).

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="sessioncount-plugin"></a>Plug-in session_count

[**Plug-in session_count** ](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Calcula a contagem de sessões com base na ID da coluna em uma linha do tempo.

A consulta a seguir retorna a contagem de sessões. Uma sessão é considerada ativa se uma ID de usuário é exibida pelo menos uma vez em um período com 100 intervalos de tempo, enquanto a janela de retrospectiva é de 41 intervalos de tempo.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnelsequence-plugin"></a>Plug-in funnel_sequence

[**Plug-in funnel_sequence**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Calcula a contagem distinta de usuários que entraram em uma sequência de estados; mostra a distribuição dos estados anteriores e seguintes que levaram à sequência ou que foram seguidos por ela.

A consulta a seguir mostra qual evento acontece antes e depois de todos os eventos de tornado em 2007.

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnelsequencecompletion-plugin"></a>Plug-in funnel_sequence_completion

[**Plug-in funnel_sequence_completion**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Calcula o funil de etapas da sequência concluídas em diferentes períodos.

A consulta a seguir verifica o funil de conclusão da sequência: `Hail -> Tornado -> Thunderstorm -> Wind` nos tempos "gerais" de uma hora, quatro horas e um dia (`[1h, 4h, 1d]`).

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Funções

Esta seção aborda [**funções**](https://docs.microsoft.com/azure/kusto/query/functions): consultas reutilizáveis que são armazenados no servidor. Funções podem ser invocadas por consultas e por outras funções (funções recursivas não são compatíveis).

> [!NOTE]
> Você não pode criar funções no cluster de ajuda, que é somente leitura. Use seu próprio cluster de teste para essa parte.

O exemplo a seguir cria uma função que usa um nome de estado (`MyState`) como um argumento.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

O exemplo a seguir chama uma função que obtém dados do estado do Texas.

```Kusto
MyFunction ("Texas")
| summarize count()
```

O exemplo a seguir exclui a função que foi criada na primeira etapa.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Próximas etapas

[Referência da linguagem de consulta do Kusto](https://aka.ms/kustolangref)
