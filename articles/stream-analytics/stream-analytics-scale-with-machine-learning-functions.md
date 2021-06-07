---
title: Dimensionar funções Machine Learning Studio (clássicas) no Azure Stream Analytics
description: Este artigo descreve como dimensionar Stream Analytics trabalhos que usam funções Machine Learning Studio (clássicas), configurando o particionamento e as unidades de fluxo.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 1ee1411aba7724d76ed8626de9b8b038d02339dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574247"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Dimensionar seu trabalho do Stream Analytics com funções do Azure Machine Learning Studio (clássico)

> [!TIP]
> É altamente recomendado usar [UDFs do Azure Machine Learning](machine-learning-udf.md) em vez de UDFs do Azure Machine Learning Studio (clássico) para melhorar o desempenho e a confiabilidade.

Este artigo discute como dimensionar com eficiência Azure Stream Analytics trabalhos que usam funções Azure Machine Learning Studio (clássicas). Para saber mais sobre como dimensionar trabalhos do Stream Analytics em geral, confira o artigo [Dimensionar os trabalhos do Stream Analytics](stream-analytics-scale-jobs.md).

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>O que é uma função de estúdio (clássico) no Stream Analytics?

Uma função Machine Learning Studio (clássica) no Stream Analytics pode ser usada como uma chamada de função regular na linguagem de consulta Stream Analytics. Nos bastidores, no entanto, essas chamadas de função são, na verdade, as solicitações de serviço Web do Studio (clássico).

Você pode melhorar a taxa de transferência de solicitações de serviço Web do estúdio (clássico) por meio de "envio em lote" de várias linhas na mesma chamada à API do serviço Web. Esse agrupamento é chamado de minilote. Para saber mais, confira [Serviços Web do Machine Learning Studio (clássico)](../machine-learning/classic/consume-web-services.md). Suporte para Studio (clássico) no Stream Analytics.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Configurar um trabalho de Stream Analytics com as funções do Studio (clássico)

Há dois parâmetros para configurar a função Studio (clássico) usada pelo seu trabalho de Stream Analytics:

* Tamanho do lote das chamadas de função do estúdio (clássico).
* O número de unidades de streaming (SUs) provisionadas para o trabalho do Stream Analytics.

Para determinar os valores apropriados para as SUs, decida se deseja otimizar a latência do trabalho do Stream Analytics ou a taxa de transferência de cada SU. As SUs podem ser sempre adicionadas a um trabalho a fim de aumentar a taxa de transferência de uma consulta do Stream Analytics bem particionada. SUs adicionais aumentam o custo da execução do trabalho.

Determine a *tolerância* de latência do seu trabalho do Stream Analytics. Aumentar o tamanho do lote aumentará a latência de suas solicitações do estúdio (clássico) e a latência do trabalho de Stream Analytics.

Aumentar o tamanho do lote permite que o trabalho de Stream Analytics processe **mais eventos** com o **mesmo número** de solicitações de serviço da Web do estúdio (clássico). O aumento da latência de serviço Web de estúdio (clássico) geralmente é sublinear ao aumento do tamanho do lote. 

É importante considerar o tamanho de lote mais econômico para um serviço Web de estúdio (clássico) em qualquer situação em questão. O tamanho de lote padrão para solicitações de serviço Web é 1000. Você pode alterar esse tamanho padrão usando a [API REST do Stream Analytics](/previous-versions/azure/mt653706(v=azure.100) "API REST do Stream Analytics") ou o [cliente do PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Depois de decidir o tamanho de lote, defina o número de unidades de streamings (SUs) com base no número de eventos que a função precisa processar por segundo. Para obter mais informações sobre o unidades de streaming, consulte Dimensionar trabalhos do [Trabalhos de escala do Stream Analytics](stream-analytics-scale-jobs.md).

Cada 6 SUs Obtém 20 conexões simultâneas com o serviço Web Studio (clássico). No entanto, um trabalho de SU e 3 trabalhos de SU recebem 20 conexões simultâneas.  

Se o seu aplicativo gera 200.000 eventos por segundo, e o tamanho do lote é 1000, a latência do serviço Web resultante é de 200 ms. Essa taxa significa que cada conexão pode fazer cinco solicitações para o serviço Web Studio (clássico) a cada segundo. Com 20 conexões, o trabalho do Stream Analytics pode processar 20.000 eventos em 200 ms e 100.000 eventos em um segundo.

Para processar 200.000 eventos por segundo, o trabalho do Stream Analytics precisará de 40 conexões simultâneas, provenientes de 12 SUs. O diagrama a seguir ilustra as solicitações do trabalho de Stream Analytics para o ponto de extremidade do serviço Web Studio (clássico) – cada 6 SUs tem 20 conexões simultâneas com o serviço Web Studio (clássico) no máximo.

![Dimensionar Stream Analytics com funções do Studio (clássico) dois exemplos de trabalho](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Dimensionar Stream Analytics com funções do Studio (clássico) dois exemplos de trabalho")

Em geral, ***B** _ para tamanho do lote, _*_L_*_ para a latência do serviço Web no tamanho do lote B em milissegundos, a taxa de transferência de um trabalho de Stream Analytics com _ *_N_** SUS é:

![Dimensionar Stream Analytics com fórmula de funções do estúdio (clássico)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Dimensionar Stream Analytics com fórmula de funções do estúdio (clássico)")

Você também pode configurar o ' máximo de chamadas simultâneas ' no serviço Web Studio (clássico). É recomendado definir esse parâmetro como o valor máximo (200 no momento).

Para obter mais informações sobre essa configuração, examine o [artigo de dimensionamento dos serviços Web Machine Learning Studio (clássico)](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Exemplo – análise de sentimento
O exemplo a seguir inclui um trabalho Stream Analytics com a função de análise de sentimentos (clássico), conforme descrito no [tutorial de integração do Stream Analytics Machine Learning Studio (clássico)](stream-analytics-machine-learning-integration-tutorial.md).

A consulta é uma consulta simples e totalmente particionada, seguida pela função **sentimento**, conforme mostrado abaixo:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vamos examinar a configuração necessária para criar um trabalho do Stream Analytics, que faz a análise de sentimento dos tweets a uma taxa de 10.000 tweets por segundo.

Com uma SU, esse trabalho do Stream Analytics processará o tráfego? O trabalho pode manter a entrada usando o tamanho de lote padrão de 1000. A latência padrão do serviço Web do resentiment Analysis Studio (clássico) (com um tamanho de lote padrão de 1000) não cria mais do que um segundo de latência.

A latência **geral** ou de ponta a ponta do trabalho do Stream Analytics normalmente seria de alguns segundos. Dê uma olhada mais detalhada nesse Stream Analytics trabalho, *especialmente* as chamadas de função do estúdio (clássico). Com um tamanho de lote de 1000, uma taxa de transferência de 10.000 eventos precisa de aproximadamente 10 solicitações ao serviço Web. Mesmo com uma AU, há conexões simultâneas suficientes para acomodar esse tráfego de entrada.

Se a taxa de eventos de entrada aumentar em 100x, o trabalho do Stream Analytics precisará processar 1.000.000 de tweets por segundo. Há duas opções para realizar o aumento da escala:

1. Aumente o tamanho do lote.
2. Particione o fluxo de entrada para processar os eventos simultaneamente.

Com a primeira opção, a **latência** do trabalho aumenta.

Com a segunda opção, você precisará provisionar mais SUs para ter mais solicitações simultâneas de serviço Web do Studio (clássico). Esse número maior de SUs aumenta o **custo** do trabalho.

Vamos examinar a colocação em escala usando as medidas de latência a seguir para cada tamanho de lote:

| Latency | Tamanho do lote |
| --- | --- |
| 200 ms | Lotes de 1.000 eventos ou menos |
| 250 ms | Lotes de 5.000 eventos |
| 300 ms | Lotes de 10.000 eventos |
| 500 ms | Lotes de 25.000 eventos |

1. Usando a primeira opção (**não** Provisionando mais SUs). O tamanho do lote pode ser aumentado para **25.000**. O aumento do tamanho do lote dessa maneira permitirá que o trabalho processe eventos 1 milhão com 20 conexões simultâneas com o serviço Web Studio (clássico) (com uma latência de 500 MS por chamada). Portanto, a latência adicional do trabalho de Stream Analytics devido às solicitações de função de sentimentos em relação às solicitações de serviço Web Studio (clássico) será aumentada de **200 MS** para **500 MS**. No entanto, o tamanho do lote **não pode** ser aumentado infinitamente, pois os serviços Web do estúdio (clássico) exigem que o tamanho da carga de uma solicitação seja de 4 MB ou menor, e o tempo limite das solicitações de serviço web após 100 segundos de operação.
1. Usando a segunda opção, o tamanho do lote é deixado em 1000, com latência de serviço Web de 200 ms, cada 20 conexões simultâneas com o serviço Web seriam capazes de processar 1000 * 20 * 5 eventos = 100.000 por segundo. Então, para processar 1.000.000 eventos por segundo, o trabalho precisaria de 60 SUs. Comparando com a primeira opção, o trabalho do Stream Analytics faria mais solicitações em lote do serviço Web, gerando um aumento do custo.

Veja abaixo uma tabela sobre a produtividade do trabalho do Stream Analytics para SUs e tamanhos de lote diferentes (em número de eventos por segundo).

| tamanho do lote (latência de AM) | 500 (200 ms) | 1\.000 (200 ms) | 5\.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2\.500 |5\.000 |20,000 |30,000 |50.000 |
| **3 SUs** |2\.500 |5\.000 |20,000 |30,000 |50.000 |
| **6 SUs** |2\.500 |5\.000 |20,000 |30,000 |50.000 |
| **12 SUs** |5\.000 |10.000 |40.000 |60.000 |100.000 |
| **18 SUs** |7\.500 |15,000 |60.000 |90.000 |150.000 |
| **24 SUs** |10.000 |20,000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25.000 |50.000 |200.000 |300.000 |500.000 |

Agora, você já deve ter uma boa compreensão de como as funções do Studio (clássico) no Stream Analytics funcionam. É provável que você também entenda que os jobs do Stream Analytics "extraem" dados de fontes de dados e cada "pull" retorna um lote de eventos para o trabalho do Stream Analytics processar. Como esse modelo de pull afeta as solicitações do serviço Web Studio (clássico)?

Normalmente, o tamanho do lote que definimos para as funções do Studio (clássico) não será exatamente divisível pelo número de eventos retornados por cada Stream Analytics trabalho "pull". Quando isso ocorre, o serviço Web Studio (clássico) é chamado com lotes "parciais". Usar lotes parciais evita a sobrecarga adicional de latência de trabalho em eventos acumulados a cada pull.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitoramentos relacionadas à função
Na área de Monitoramento de um trabalho do Stream Analytics, foram adicionadas três métricas relacionadas à função. São elas **FUNCTION REQUESTS**, **FUNCTION EVENTS** e **FAILED FUNCTION REQUESTS**, como mostrado no gráfico a seguir.

![Dimensionar Stream Analytics com as métricas de funções do estúdio (clássico)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Dimensionar Stream Analytics com as métricas de funções do estúdio (clássico)")

Estas são as definições:

**SOLICITAÇÕES DE FUNÇÃO**: O número de solicitações de função.

**EVENTOS DE FUNÇÃO**: O número de eventos nas solicitações de função.

**SOLICITAÇÕES DE FUNÇÃO COM FALHA**: O número de solicitações de função com falha.

## <a name="key-takeaways"></a>Principais observações

Para dimensionar um trabalho de Stream Analytics com funções do Studio (clássico), considere os seguintes fatores:

1. A taxa de eventos de entrada.
2. A latência tolerada para o trabalho de Stream Analytics em execução (e, portanto, o tamanho do lote das solicitações do serviço Web Studio (clássico)).
3. O SUs provisionado Stream Analytics e o número de solicitações de serviço da Web do Studio (clássico) (os custos adicionais relacionados à função).

Uma consulta do Stream Analytics totalmente particionada foi usada como exemplo. Se uma consulta mais complexa for necessária, a [página de perguntas e respostas da Microsoft para o Azure Stream Analytics](/answers/topics/azure-stream-analytics.html) é um excelente recurso para se obter ajuda adicional da equipe do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Stream Analytics, confira:

* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)
