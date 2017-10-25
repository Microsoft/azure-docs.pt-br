---
title: Um tour pelo Analytics no Azure Application Insights | Microsoft Docs
description: "Exemplos curtos de todas as principais consultas na Análise, a ferramenta de pesquisa avançada do Application Insights."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/06/2017
ms.author: bwren
ms.openlocfilehash: ccea92dda99c3b76cbb7d37b20ce810b210a8217
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Um tour pela Análise no Application Insights
O [Analytics](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta do Log Analytics.

* **[Assista ao vídeo introdutório](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Faça um test drive do Analytics com nossos dados simulados](https://analytics.applicationinsights.io/demo)** se seu aplicativo ainda não estiver enviando dados para o Application Insights.
* **[Roteiro dos usuários do SQL](https://aka.ms/sql-analytics)** converte as linguagens mais comuns.

Vamos analisar algumas consultas básicas para começar.

## <a name="connect-to-your-application-insights-data"></a>Conectar-se aos dados do Application Insights
Abra o Analytics na [folha de visão geral](app-insights-dashboards.md) de seu aplicativo no Application Insights:

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsioquerylanguagequerylanguagetakeoperatorhtml-show-me-n-rows"></a>[Take](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html): mostre-me n linhas
Os pontos de dados que registram em log operações de usuário (geralmente solicitações HTTP recebidas pelo seu aplicativo Web) são armazenados em uma tabela chamada `requests`. Cada linha é um ponto de dados de telemetria recebido do SDK do Application Insights em seu aplicativo.

Vamos começar examinando algumas linhas de exemplo da tabela:

![results](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Coloque o cursor em algum lugar na instrução antes de clicar em Ir. Você pode dividir uma instrução em mais de uma linha, mas não coloque linhas em branco em uma instrução. As linhas em branco são uma maneira conveniente de manter várias consultas separadas na janela.
>
>

Escolha colunas, arraste-as, agrupe por colunas e filtre:

![Clique na seleção de coluna no canto superior direito dos resultados](./media/app-insights-analytics-tour/030.png)

Expanda algum item para ver os detalhes:

![Escolha Tabela e use Configurar Colunas](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Clique no cabeçalho de uma coluna para reordenar os resultados disponíveis no navegador da Web. Mas lembre-se de que, para um conjunto de resultados grande, o número de linhas baixadas para o navegador é limitado. Portanto, classificar dessa maneira não mostra sempre a você os reais itens maiores ou menores. Para classificar itens de forma confiável, use o `top` ou o operador `sort`.
>
>

## <a name="query-across-applications"></a>Consulta entre aplicativos
Se você quiser combinar dados de vários aplicativos do Application Insights, use a palavra-chave **aplicativo** para especificar o aplicativo junto com o nome da tabela.  Essa consulta combina as solicitações de dois aplicativos diferentes usando o comando **union**.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsioquerylanguagequerylanguagetopoperatorhtml-and-sorthttpsdocsloganalyticsioquerylanguagequerylanguagesortoperatorhtml"></a>[Superior](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) e [classificação](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)
`take` é útil para obter um exemplo rápido de um resultado, mas mostra linhas da tabela sem uma ordem específica. Para obter uma exibição ordenada, use `top` (para obter um exemplo) ou `sort` (na tabela inteira).

Mostre-me as primeiras n linhas, ordenadas por uma coluna específica:

```AIQL

    requests | top 10 by timestamp desc
```

* *Sintaxe:* a maioria dos operadores têm parâmetros de palavra-chave, como `by`.
* `desc` = ordem decrescente, `asc` = ordem crescente.

![](./media/app-insights-analytics-tour/260.png)

`top...` é uma maneira mais eficaz de dizer `sort ... | take...`. Poderíamos ter escrito:

```AIQL

    requests | sort by timestamp desc | take 10
```

O resultado seria o mesmo, mas ele seria executado um pouco mais lentamente. (Também é possível escrever `order`, que é um alias de `sort`.)

Os cabeçalhos de coluna no modo de exibição de tabela também podem ser usados para classificar os resultados na tela. Mas, obviamente, se você usou `take` ou `top` para recuperar apenas parte de uma tabela, apenas os registros que foram recuperados serão reordenados.

## <a name="wherehttpsdocsloganalyticsioquerylanguagequerylanguagewhereoperatorhtml-filtering-on-a-condition"></a>[Where](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html): filtragem de uma condição

Vamos ver apenas as solicitações que retornaram um código de resultado específico:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

O operador `where` usa uma expressão booliana. Eis alguns pontos importantes sobre eles:

* `and`, `or`: Operadores booleanos
* `==`, `<>`, `!=`: igual a e diferente de
* `=~`, `!~`: cadeia de caracteres que não diferencia maiúsculas de minúsculas "igual a" e "diferente de". Há muitos outros operadores de comparação de cadeia de caracteres.

<!---Read all about [scalar expressions]().--->

### <a name="getting-the-right-type"></a>Obtenção do tipo correto
Solicitações malsucedidas de localização:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Hora

Por padrão, as consultas são restritas às últimas 24 horas. Mas você pode alterar esse intervalo:

![](./media/app-insights-analytics-tour/change-time-range.png)

Substitua o intervalo de tempo escrevendo qualquer consulta que mencione `timestamp` em uma cláusula where. Por exemplo:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

O recurso de intervalo de tempo é equivalente a uma cláusula 'where' inserida após cada menção de uma das tabelas de origem.

`ago(3d)` significa 'três dias atrás'. Outras unidades de tempo incluem horas (`2h`, `2.5h`), minutos (`25m`) e segundos (`10s`).

Outros exemplos:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[Referência de datas e horas](https://docs.loganalytics.io/concepts/concepts_datatypes_datetime.html).


## <a name="projecthttpsdocsloganalyticsioquerylanguagequerylanguageprojectoperatorhtml-select-rename-and-compute-columns"></a>[Projeto](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html): selecionar, renomear e computar colunas
Use [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) para selecionar apenas as colunas desejadas:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Você também pode renomear e definir novas colunas:

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* Os nomes de coluna poderão incluir espaços ou símbolos se eles estiverem entre colchetes, desta forma: `['...']` ou `["..."]`
* `%` é o operador de módulo normal.
* `1d` (que é o dígito um e um “d”) é um literal de timespan que significa um dia. Aqui estão mais alguns literais de timespan: `12h`, `30m`, `10s` e `0.01s`.
* `floor` (alias `bin`) arredonda um valor até o múltiplo mais próximo do valor de base fornecido. De modo que `floor(aTime, 1s)` arredonda um tempo até o segundo mais próximo.

As expressões podem incluir todos os operadores comuns (`+`, `-`, ...) e há uma variedade de funções úteis.

## <a name="extend"></a>Extend
Se quiser apenas adicionar colunas às existentes, use [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

O uso de [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html) será menos detalhado do que o de [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) se você quiser manter todas as colunas existentes.

### <a name="convert-to-local-time"></a>Converter em hora local

Os carimbos de data e hora são sempre em UTC. Portanto, se estiver na costa do Pacífico nos EUA e for inverno, você terá algo semelhante a isto:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizehttpsdocsloganalyticsioquerylanguagequerylanguagesummarizeoperatorhtml-aggregate-groups-of-rows"></a>[Resumir](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html): agregar grupos de linhas
`Summarize` aplica uma *função de agregação* especificada em grupos de linhas.

Por exemplo, o tempo que o seu aplicativo Web leva para responder a uma solicitação é informado no campo `duration`. Vamos ver o tempo médio de resposta para todas as solicitações:

![](./media/app-insights-analytics-tour/410.png)

Ou podemos separar o resultado em solicitações de nomes diferentes:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` coleta os pontos de dados no fluxo dos grupos para os quais a cláusula `by` é igualmente avaliada. Cada valor na expressão `by` (cada nome de operação no exemplo acima) resulta em uma linha na tabela de resultados.

Ou podemos agrupar os resultados por hora do dia:

![](./media/app-insights-analytics-tour/430.png)

Observe como estamos usando a função `bin` (também conhecida como `floor`). Se usássemos apenas `by timestamp`, cada linha de entrada acabaria em seu próprio pequeno grupo. Para qualquer escalar contínuo com horas ou números, é necessário dividir o intervalo contínuo em um número gerenciável de valores distintos. `bin`, que é simplesmente a conhecida função de arredondamento para baixo `floor`, é a maneira mais fácil de fazer isso.

Podemos usar a mesma técnica para reduzir intervalos de cadeias de caracteres:

![](./media/app-insights-analytics-tour/440.png)

Observe que você pode usar `name=` para definir o nome de uma coluna de resultado, seja nas expressões de agregação, seja na cláusula by.

## <a name="counting-sampled-data"></a>Contando dados de amostra
`sum(itemCount)` é a agregação recomendada para contar eventos. Em muitos casos, itemCount==1, de modo que a função simplesmente conta até o número de linhas no grupo. Contudo, quando a [amostragem](app-insights-sampling.md) estiver em operação, apenas uma fração dos eventos originais será retida como pontos de dados no Application Insights, de modo que para cada ponto de dados que você visualizar, haverá `itemCount` eventos.

Por exemplo, se a amostragem descartar 75% dos eventos originais, itemCount == 4 nos registros retidos - ou seja, para cada registro retido, houve quatro registros originais.

A amostragem adaptável fará com que itemCount seja maior durante períodos em que seu aplicativo estiver sendo muito usado.

Portanto, a soma de itemCount dá uma boa estimativa do número original de eventos.

![](./media/app-insights-analytics-tour/510.png)

Também há uma agregação `count()` (e uma operação de contagem), para casos em que você realmente quiser contar o número de linhas em um grupo.

Há uma série de [funções de agregação](https://docs.loganalytics.io/learn/tutorials/aggregations.html).

## <a name="charting-the-results"></a>Colocando os resultados em gráficos
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Por padrão, os resultados são exibidos como uma tabela:

![](./media/app-insights-analytics-tour/225.png)

Podemos fazer melhor do que a exibição de tabela. Vamos examinar os resultados no modo de exibição de gráfico com a opção de barra vertical:

![Clique em Gráfico, escolha o gráfico de barras Vertical e atribua os eixos x e y](./media/app-insights-analytics-tour/230.png)

Observe que, embora não tenhamos classificado os resultados por tempo (como você pode ver na exibição de tabela), a exibição de gráfico sempre mostra datetimes na ordem correta.


## <a name="timecharts"></a>Timecharts
Mostra quantos eventos há a cada hora:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Selecione a opção de exibição Gráfico:

![timechart](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Várias séries
Várias expressões na cláusula `summarize` criam várias colunas.

Várias expressões na cláusula `by` criam várias linhas, uma para cada combinação de valores.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tabela de solicitações por hora e local](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Segmentar um gráfico por dimensões
Se você criar um gráfico de uma tabela que tenha uma coluna de cadeia de caracteres e uma coluna numérica, a cadeia de caracteres pode ser usada para dividir os dados numéricos em uma série de pontos separada. Se houver mais de uma coluna de cadeia de caracteres, você poderá escolher qual coluna usar como o discriminador.

![Segmentar um gráfico de análise](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Taxa de devolução

Converta um booliano em uma cadeia de caracteres para usá-la como um discriminador:

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Exibir várias métricas
Se você criar um gráfico de uma tabela com mais de uma coluna numérica, além de carimbo de data/hora, será possível exibir qualquer combinação desses itens.

![Segmentar um gráfico de análise](./media/app-insights-analytics-tour/110.png)

Você deve selecionar **Não Dividir** antes de selecionar várias colunas numéricas. Não é possível dividir por uma coluna de cadeia de caracteres ao mesmo tempo que exibe mais de uma coluna numérica.

## <a name="daily-average-cycle"></a>Ciclo médio diário
Como o uso varia na média diária?

Conte solicitações pelo módulo de tempo de um dia, compartimentalizado por horas:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Gráfico de linhas de horas em um dia normal](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Observe que, no momento, temos que converter durações de tempo em datas/horas para exibir em um gráfico de linha.
>
>

## <a name="compare-multiple-daily-series"></a>Comparar várias séries diárias
Como o uso varia de acordo com a hora do dia em diferentes países?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![Divisão por client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Plotar uma distribuição
Quantas sessões existem de comprimentos diferentes?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

A última linha é necessária para a conversão em datetime. Atualmente, o eixo x de um gráfico só será exibido como um escalar se ele for um datetime.

A cláusula `where` exclui sessões únicas (sessionDuration==0) e define o comprimento do eixo x.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsioquerylanguagequerylanguagepercentilesaggfunctionhtml"></a>[Percentis](https://docs.loganalytics.io/queryLanguage/query_language_percentiles_aggfunction.html)
Quais intervalos de durações abordam diferentes porcentagens de sessões?

Use a consulta acima, mas substitua a última linha:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Também removemos o limite máximo na cláusula where para obter números corretos, incluindo todas as sessões com mais de uma solicitação:

![result](./media/app-insights-analytics-tour/180.png)

O que nos mostra que:

* 5% das sessões têm uma duração de menos de 3 minutos e 34 segundos;
* 50% das sessões duram menos de 36 minutos;
* 5% das sessões duram mais de 7 dias

Para obter uma análise separada para cada país, temos apenas que trazer a coluna client_CountryOrRegion para ambos os operadores summarize:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Ingressar
Temos acesso a várias tabelas, incluindo solicitações e exceções.

Para encontrar as exceções relacionadas a uma solicitação que retornou uma resposta com falha, podemos unir as tabelas em `session_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


É uma prática recomendável usar `project` para selecionar apenas as colunas necessárias antes de executar a junção.
Renomeamos a coluna de carimbo de data/hora nas mesmas cláusulas.

## <a name="lethttpsdocsloganalyticsioquerylanguagequerylanguageletstatementhtml-assign-a-result-to-a-variable"></a>[Let](https://docs.loganalytics.io/queryLanguage/query_language_letstatement.html): atribuir um resultado a uma variável

Use `let` para separar as partes da expressão anterior. Os resultados não mudam:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> No cliente do Analytics, não coloque linhas em branco entre as partes da consulta. Execute tudo.
>

Use `toscalar` para converter uma única célula de tabela em um valor:

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Funções

Use *Let* para definir uma função:

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Acessando objetos aninhados
Os objetos aninhados podem ser acessados facilmente. Por exemplo, no fluxo de exceções, você verá objetos estruturados como este:

![result](./media/app-insights-analytics-tour/520.png)

Você pode mesclá-lo escolhendo as propriedades que te interessam:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Observe que você precisa converter o resultado no tipo apropriado.


## <a name="custom-properties-and-measurements"></a>Medidas e propriedades personalizadas
Se o seu aplicativo anexar [dimensões personalizadas (propriedades) e medidas personalizadas](app-insights-api-custom-events-metrics.md#properties) a eventos, você os verá nos objetos `customDimensions` e `customMeasurements`.

Por exemplo, se o seu aplicativo inclui:

```C#

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Para extrair esses valores na Análise:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type

```

Para verificar se uma dimensão personalizada é de um tipo específico:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

## <a name="dashboards"></a>Painéis
Você pode fixar os resultados em um painel para reunir todos os seus gráficos e suas tabelas mais importantes.

* [Painel compartilhado do Azure](app-insights-dashboards.md#share-dashboards): clique no ícone de pino. Antes de fazer isso, você deve ter um painel compartilhado. No portal do Azure, abra ou crie um painel e clique em Compartilhar.
* [Painel do Power BI](app-insights-export-power-bi.md): clique em Exportar, Consulta do Power BI. Uma vantagem dessa alternativa é que você pode exibir sua consulta juntamente com outros resultados de uma ampla variedade de fontes.

## <a name="combine-with-imported-data"></a>Combinar com dados importados

Os relatórios do Analytics parecem ótimos no painel, mas, às vezes, você pode querer colocar os dados em um formato mais fácil de entender. Por exemplo, suponha que os usuários autenticados sejam identificados na telemetria por um alias. Você gostaria de mostrar os nomes reais nos resultados. Para fazer isso, você precisará de um arquivo CSV que mapeie os aliases para os nomes reais.

Você pode importar um arquivo de dados e usá-lo assim como qualquer uma das tabelas padrão (solicitações, exceções, etc.). É possível consultá-la sozinha ou uni-la a outras tabelas. Por exemplo, se houver uma tabela chamada usermap e ela tiver as colunas `realName` e `userId`, você poderá usá-la para converter o campo `user_AuthenticatedId` na telemetria de solicitação:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Para importar uma tabela, na folha Esquema, em **Outras Fontes de Dados**, siga as instruções para adicionar uma nova fonte de dados carregando uma amostra dos seus dados. Em seguida, você poderá usar essa definição para carregar as tabelas.

O recurso de importação está em visualização, por isso você verá inicialmente um link “Fale conosco” em “Outras fontes de dados”. Use-o para se inscrever no programa de visualização e o link será substituído por um botão “Adicionar nova fonte de dados”.


## <a name="tables"></a>Tabelas
O fluxo de telemetria recebido de seu aplicativo é acessível por meio de várias tabelas. O esquema de propriedades disponível para cada tabela está visível na parte esquerda da janela.

### <a name="requests-table"></a>Tabela de solicitações
Conte as solicitações HTTP para seu aplicativo Web e segmento por nome de página:

![Conte solicitações segmentadas por nome](./media/app-insights-analytics-tour/analytics-count-requests.png)

Descubra as solicitações que mais falham:

![Conte solicitações segmentadas por nome](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Tabela de eventos personalizada
Se usar [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) para enviar seus próprios eventos, você poderá lê-los nessa tabela.

Vejamos um exemplo em que o código do aplicativo contém estas linhas:

```C#

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Exiba a frequência destes eventos:

![Taxa de exibição de eventos personalizados](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Extrair medidas e dimensões de eventos:

![Taxa de exibição de eventos personalizados](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Tabela de métricas personalizada
Se estiver usando [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) para enviar seus próprios valores de métricas, você encontrará os resultados no fluxo **customMetrics**. Por exemplo:  

![Métricas personalizadas na análise do Application Insights](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> No [Metrics Explorer](app-insights-metrics-explorer.md), todas as medidas personalizadas anexadas a qualquer tipo de telemetria aparecem juntas na folha de métricas, juntamente com métricas enviadas usando `TrackMetric()`. No Analytics, no entanto, as medidas personalizadas ainda estão conectadas a qualquer tipo de telemetria no qual foram realizadas, eventos ou solicitações, entre outros, enquanto as métricas enviadas pelo TrackMetric são exibidas em seu próprio fluxo.
>
>

### <a name="performance-counters-table"></a>Tabela de contadores de desempenho
Os [contadores de desempenho](app-insights-performance-counters.md) mostram métricas básicas do sistema para seu aplicativo, tais como CPU, memória e uso de rede. Você pode configurar o SDK para enviar outros contadores, incluindo seus próprios contadores personalizados.

O esquema **performanceCounters** expõe o nome `category`, `counter` e o nome `instance` de cada contador de desempenho. Nomes de instância do contador só são aplicáveis a alguns contadores de desempenho e geralmente indicam o nome do processo ao qual a contagem está relacionada. Na telemetria de cada aplicativo, você verá apenas os contadores para aquele aplicativo. Por exemplo, para ver quais contadores estão disponíveis:

![Contadores de desempenho na análise do Application Insights](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Para obter um gráfico de memória disponível do período selecionado:

![Gráfico de tempo da memória na análise do Application Insights](./media/app-insights-analytics-tour/analytics-available-memory.png)

Como outras telemetrias, o **performanceCounters** também tem uma coluna `cloud_RoleInstance` que indica a identidade do computador host no qual seu aplicativo está sendo executado. Por exemplo, para comparar o desempenho do seu aplicativo em diferentes computadores:

![Desempenho segmentado por instância de função na análise do Application Insights](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Tabela de exceções
As [exceções relatadas pelo seu aplicativo](app-insights-asp-net-exceptions.md) estão disponíveis nessa tabela.

Para localizar a solicitação HTTP que seu aplicativo estava manipulando quando a exceção foi gerada, ingresse em operation_Id:

![Ingressar em exceções com solicitações em operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Tabela de intervalos do navegador
`browserTimings` mostra os dados de carregamento de página coletados nos navegadores de seus usuários.

[Configure seu aplicativo para a telemetria do lado do cliente](app-insights-javascript.md) para ver essas métricas.

O esquema inclui [métricas que indicam os comprimentos de diferentes etapas do processo de carregamento de página](app-insights-javascript.md#page-load-performance). (Elas não indicam o tempo que os usuários levam para ler uma página.)  

Mostrar a popularidade de diferentes páginas e o tempo de carregamento para cada página:

![Tempos de carregamento de página no Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Tabela de resultados de disponibilidade
`availabilityResults` mostra os resultados de seus [testes da Web](app-insights-monitor-web-app-availability.md). Cada execução dos testes de cada local de teste é relatada separadamente.

![Tempos de carregamento de página no Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabela de dependências
Contém os resultados de chamadas que seu aplicativo fez para bancos de dados e APIs REST e outras chamadas para TrackDependency(). Também inclui chamadas AJAX feitas no navegador.

Chamadas AJAX no navegador:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Chamadas de dependência do servidor:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Os resultados de dependência no lado do servidor sempre mostrarão `success==False` se o Agente do Application Insights não estiver instalado. No entanto, os outros dados estarão corretos.

### <a name="traces-table"></a>Tabela de rastreamentos
Contém a telemetria enviada pelo seu aplicativo usando TrackTrace() ou [outras estruturas de registro](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Vídeo 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Consultas avançadas:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Próximas etapas
* [Referência de linguagem de análise](app-insights-analytics-reference.md)
* [Roteiro dos usuários do SQL](https://aka.ms/sql-analytics) converte as linguagens mais comuns.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
