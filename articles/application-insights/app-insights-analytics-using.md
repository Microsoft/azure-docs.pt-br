---
title: "Usando o Analytics – a ferramenta de pesquisa avançada do Azure Application Insights | Microsoft Docs"
description: "Usando a Análise, a ferramenta de pesquisa e diagnóstico avançada do Application Insights. "
services: application-insights
documentationcenter: 
author: danhadari
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: 49edbaad0af2eeef4b8c348b9fd34e37ba7e80e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-analytics-in-application-insights"></a>Usando Análise no Application Insights
O [Analytics](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta do Log Analytics.

* **[Assista ao vídeo introdutório](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Faça um test drive do Analytics com nossos dados simulados](https://analytics.applicationinsights.io/demo)** se seu aplicativo ainda não estiver enviando dados para o Application Insights.

## <a name="open-analytics"></a>Abrir Análise
Do recurso de página inicial do seu aplicativo no Application Insights, clique em Análise.

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-insights-analytics-using/001.png)

O tutorial embutido oferece algumas ideias sobre o que você pode fazer.

Há um [tour mais extenso aqui](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Consultar sua telemetria
### <a name="write-a-query"></a>Escreva uma consulta
![Exibição de esquema](./media/app-insights-analytics-using/150.png)

Comece com os nomes de uma das tabelas listadas à esquerda (ou os operadores [range](https://docs.loganalytics.io/queryLanguage/query_language_rangeoperator.html) ou [union](https://docs.loganalytics.io/queryLanguage/query_language_unionoperator.html)). Use `|` para criar um pipeline de [operadores](https://docs.loganalytics.io/learn/cheatsheets/useful_operators.html). 

O IntelliSense mostrará os operadores e os elementos de expressão que você pode usar. Clique no ícone de informações (ou pressione CTRL + espaço) para obter uma descrição mais detalhada e exemplos de como usar cada elemento.

Confira a [tour da linguagem do Analytics](app-insights-analytics-tour.md) e a [referência da linguagem](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Executar uma consulta
![Executando uma consulta](./media/app-insights-analytics-using/130.png)

1. Você pode usar quebras de linha simples em uma consulta.
2. Coloque o cursor na ou no final da consulta que você deseja executar.
3. Verifique o intervalo de tempo da consulta. (Você pode alterá-lo ou substituí-lo, incluindo sua própria cláusula [`where...timestamp...`](https://docs.loganalytics.io/concepts/concepts_datatypes_timespan.html) em sua consulta.)
3. Clique em Ir para executar a consulta.
4. Não coloque linhas em branco em sua consulta. Você pode manter várias consultas separadas em uma guia de consulta separando-as com linhas em branco. Somente a consulta que possui o cursor é executada.

### <a name="save-a-query"></a>Salvar uma consulta
![Salvando uma consulta](./media/app-insights-analytics-using/140.png)

1. Salve o arquivo de consulta atual.
2. Abra um arquivo de consulta salva.
3. Crie um novo arquivo de consulta.

## <a name="see-the-details"></a>Confierir os detalhes
Expanda qualquer linha nos resultados para ver a lista completa das propriedades. Você pode expandir qualquer propriedade que seja um valor estruturado; por exemplo, dimensões personalizadas ou a pilha de listagem em uma exceção.

![Expandindo uma linha](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Organizar os resultados
Você pode classificar, filtrar, paginar e agrupar os resultados retornados da sua consulta.

> [!NOTE]
> A classificação, o agrupamento e a filtragem no navegador não executam a consulta novamente. Eles apenas reorganizam os resultados que foram retornados por sua última consulta. 
> 
> Para executar essas tarefas no servidor antes que os resultados sejam retornados, escreva sua consulta com os operadores [sort](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html), [summarize](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html) e [where](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html).
> 
> 

Selecione as colunas que você gostaria de ver, arraste os cabeçalhos de coluna para reorganizá-las e redimensione as colunas arrastando suas bordas.

![Organizando colunas](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Classificar e filtrar itens
Classifique os resultados clicando no cabeçalho de uma coluna. Clique novamente para classificar de outra forma e clique uma terceira vez para reverter à ordem original retornada pela consulta.

Use o ícone de filtro para restringir sua pesquisa.

![Classificar e filtrar colunas](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Agrupar itens
Para classificar por mais de uma coluna, use o agrupamento. Primeiro habilite-o e arraste cabeçalhos de coluna para o espaço acima da tabela.

![Agrupar](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Faltando alguns resultados?

Se você acha que não está vendo todos os resultados esperados, há alguns motivos possíveis.

* **Filtro de intervalo de tempo**. Por padrão, você verá apenas os resultados das últimas 24 horas. Há um filtro automático que limita o número de resultados que são recuperados das tabelas de origem. 

    No entanto, você pode alterar o filtro de intervalo de tempo usando o menu suspenso.

    Ou você pode substituir o intervalo automático incluindo sua própria [`where  ... timestamp ...` cláusula](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) em sua consulta. Por exemplo:

    `requests | where timestamp > ago('2d')`

* **Limite de resultados**. Há um limite de 10 mil linhas nos resultados retornados do portal. Será exibido um aviso se você ultrapassar o limite. Se isso acontecer, os resultados na tabela de classificação não mostrarão sempre todos os primeiros ou últimos resultados reais. 

    É recomendável evitar atingir o limite. Use o filtro de intervalo de tempo ou operadores como:

  * [top 100 by timestamp](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) 
  * [take 100](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html)
  * [summarize ](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html) 
  * [where timestamp > ago(3d)](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html)

(Quer de 10 mil linhas? Considere usar [Exportação contínua](app-insights-export-telemetry.md) em vez disso. A análise foi projetada para análise, em vez de recuperar dados brutos.)

## <a name="diagrams"></a>Diagramas
Selecione o tipo de diagrama que você deseja:

![Selecionar um tipo de diagrama](./media/app-insights-analytics-using/230.png)

Se você tiver várias colunas dos tipos corretos, você poderá escolher os eixos x e y, e uma coluna de dimensões pelas quais dividir os resultados.

Por padrão, os resultados são exibidos inicialmente como uma tabela e você seleciona o diagrama manualmente. Mas você pode usar a [diretiva de renderização](https://docs.loganalytics.io/queryLanguage/query_language_renderoperator.html) ao final de uma consulta para selecionar um diagrama.

### <a name="analytics-diagnostics"></a>Diagnóstico de análise


Em um gráfico de tempo, se houver uma alteração ou pico repentino em seus dados, você poderá ver um ponto em destaque na linha. Isso indica que o Diagnóstico de Análise identificou uma combinação de propriedades que filtra a alteração repentina. Clique no ponto para obter mais detalhes sobre o filtro e para ver a versão filtrada. Isso pode ajudar a identificar o que causou a alteração. 

[Saiba mais sobre o Diagnóstico de Análise](app-insights-analytics-diagnostics.md)


![Diagnóstico de análise](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Fixar no painel
Você pode fixar um diagrama ou tabela em um dos seus [painéis compartilhados](app-insights-dashboards.md) , basta clicar no marcador. 

![Clicar no marcador](./media/app-insights-analytics-using/pin-01.png)

Isso significa que, quando você monta um painel para ajudar a monitorar o desempenho ou o uso de seus serviços Web, pode incluir análise bastante complexa juntamente com outras métricas. 

Você poderá fixar uma tabela no painel, se ele tiver quatro ou menos colunas. Somente as primeiras sete linhas são exibidas.

### <a name="dashboard-refresh"></a>Atualização do painel
O gráfico fixado no painel é atualizado de forma automática ao executar novamente a consulta aproximadamente a cada hora. Você também pode clicar no botão Atualizar.

### <a name="automatic-simplifications"></a>Simplificações automáticas

Certas simplificações são aplicadas a um gráfico quando ele é fixado em um painel.

**Restrição de tempo:** As consultas são limitadas automaticamente aos últimos 14 dias. O efeito é o mesmo que se sua consulta incluísse `where timestamp > ago(14d)`.

**Restrição de contagem de compartimentos:** Se você exibir um gráfico que tenha muitos compartimentos distintos (normalmente um gráfico de barras), os compartimentos menos ocupados são automaticamente agrupados em um único compartimento do tipo "outros". Por exemplo, esta consulta:

    requests | summarize count_search = count() by client_CountryOrRegion

tem esta aparência no Analytics:

![Gráfico de cauda longa](./media/app-insights-analytics-using/pin-07.png)

mas quando você a fixa a um painel, ela fica assim:

![Gráfico com compartimentos limitados](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exportar para o Excel
Depois de executar uma consulta, você pode baixar um arquivo .csv. Clique em **Exportar, Excel**.

## <a name="export-to-power-bi"></a>Exportar para o Power BI
Coloque o cursor em uma consulta e escolha **Exportar, Power BI**.

![Exportar do Analytics para o Power BI](./media/app-insights-analytics-using/240.png)

Execute a consulta no Power BI. Você pode configurá-lo para atualizar em uma agenda.

Com o Power BI, você pode criar painéis que reúnem dados de uma grande variedade de fontes.

[Saiba mais sobre como exportar para o Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Link profundo

Obtenha um link em **Exportar, Compartilhar link** que você possa enviar a outro usuário. Desde que o usuário tenha [acesso ao seu grupo de recursos](app-insights-resources-roles-access-control.md), a consulta será aberta na interface do usuário do Analytics.

(No link, o texto da consulta aparece após "?q=", gzip compactado e codificado em base 64. Você pode escrever código para gerar links profundos que fornece aos usuários. No entanto, a maneira recomendada para executar o Analytics com código é usando a [API REST](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automação

Use a [API REST de Acesso a Dados](https://dev.applicationinsights.io/) para executar consultas do Analytics. [Por exemplo](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (usando o PowerShell):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Diferentemente da interface do usuário do Analytics, a API REST não adiciona automaticamente nenhuma limitação de carimbo de data/hora às suas consultas. Lembre-se de adicionar sua própria cláusula where para evitar receber respostas enormes.



## <a name="import-data"></a>Importar dados

Você pode importar dados de um arquivo CSV. Normalmente, importam-se dados estáticos que você pode associar a tabelas de sua telemetria. 

Por exemplo, se usuários autenticados são identificados em sua telemetria por um alias ou ID ofuscado, você pode importar uma tabela que mapeie os aliases para nomes reais. Ao realizar uma junção na telemetria de solicitação, você pode identificar os usuários por seus nomes reais nos relatórios de análise.

### <a name="define-your-data-schema"></a>Definir o esquema de dados

1. Clique em **Configurações** (na parte superior esquerda) e, em seguida, **Fontes de dados**. 
2. Adicione uma fonte de dados de acordo com as instruções a seguir. Será solicitado que você forneça uma amostra dos dados, que devem incluir pelo menos dez linhas. Em seguida, você corrige o esquema.

Isso define uma fonte de dados que você pode usar para importar as tabelas individuais.

### <a name="import-a-table"></a>Importar uma tabela

1. Abra sua definição de fonte de dados da lista.
2. Clique em "Carregar" e siga as instruções para carregar a tabela. Isso envolve uma chamada a uma API REST e, portanto é fácil de automatizar. 

A tabela agora está disponível para uso em consultas de análise. Ele aparecerá na análise 

### <a name="use-the-table"></a>Use a tabela

Vamos supor que a definição de fonte de dados é chamada `usermap` e que ela possui dois campos, `realName` e `user_AuthenticatedId`. A tabela `requests` também tem um campo chamado `user_AuthenticatedId`, portanto, é fácil uni-los:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
A tabela resultante de solicitações tem uma coluna adicional, `realName`.

### <a name="import-from-logstash"></a>Importar do LogStash

Se você usar [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), você poderá usar a análise para consultar seus logs. Use o [plug-in que redireciona os dados para análise](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

