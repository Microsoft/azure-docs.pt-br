---
title: Dimensionar funções de Machine Learning no Azure Stream Analytics
description: Este artigo descreve como dimensionar trabalhos do Stream Analytics que usam funções de Machine Learning, configurando as unidades de particionamento e transmissão.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3d478c2421066c8347622f9064c479bb8255b112
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621741"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Dimensionar seu trabalho de Stream Analytics com funções do Azure Machine Learning Studio

Este artigo aborda como dimensionar trabalhos do Azure Stream Analytics que usam funções do Azure Machine Learning de maneira eficiente. Para saber mais sobre como dimensionar trabalhos do Stream Analytics em geral, confira o artigo [Dimensionar os trabalhos do Stream Analytics](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função do Azure Machine Learning no Stream Analytics?

Uma função do Machine Learning no Stream Analytics pode ser usada como uma chamada de função normal na linguagem de consulta do Stream Analytics. Nos bastidores, no entanto, essas chamadas de função são, na verdade, as solicitações de serviço da Web do Azure Machine Learning.

Você pode melhorar a taxa de transferência de solicitações de serviço web de Machine Learning, "envio em lote" várias linhas juntas na mesma chamada de API de serviço web. Esse agrupamento é chamado de um lote simplificado. Para obter mais informações, consulte [serviços Web do Azure Machine Learning Studio](../machine-learning/studio/consume-web-services.md). Suporte para o estúdio de Azure Machine Learning no Stream Analytics está em visualização.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar seu trabalho do Stream Analytics com funções de Machine Learning

Há dois parâmetros para configurar a função de aprendizado de máquina usada pelo seu trabalho do Stream Analytics:

* Tamanho de lote de chamadas de função do Machine Learning.
* O número de unidades de Streaming (SUs) provisionadas para o trabalho de Stream Analytics.

Para determinar os valores apropriados para o SUs, decida se deseja otimizar a latência do trabalho do Stream Analytics ou a taxa de transferência de cada SU. As SUs podem ser adicionadas sempre a um trabalho para aumentar a taxa de transferência de uma consulta de Stream Analytics bem particionada. SUs adicionais aumentem o custo da execução do trabalho.

Determinar a latência *tolerância* para seu trabalho do Stream Analytics. Aumentar o tamanho do lote aumentará a latência das suas solicitações de serviço do Azure Machine Learning e a latência do trabalho do Stream Analytics.

Aumentar o tamanho do lote permite que o trabalho do Stream Analytics processe **mais eventos** com o **mesmo número** de aprendizado de máquina de solicitações de serviço web. O aumento de latência de serviço web de Machine Learning é geralmente sub-linear ao aumento do tamanho do lote. 

É importante considerar o tamanho de lote mais econômico e eficiente para um serviço web de Machine Learning em qualquer situação. O tamanho de lote padrão para solicitações de serviço web é 1000. Você pode alterar esse tamanho padrão usando o [API REST do Stream Analytics](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST do Stream Analytics") ou o [cliente PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Depois de decidir sobre um tamanho de lote, você pode definir o número de unidades de streaming (SUs), com base no número de eventos que a função precisa processar por segundo. Para obter mais informações sobre o unidades de streaming, consulte Dimensionar trabalhos do [Trabalhos de escala do Stream Analytics](stream-analytics-scale-jobs.md).

Cada 6 SUs recebem 20 conexões simultâneas para o serviço web de Machine Learning. No entanto, o trabalho com 1 UA e 3 SUS recebem 20 conexões simultâneas.  

Se seu aplicativo gera 200.000 eventos por segundo e o tamanho do lote é 1000, a latência de serviço web resultante é 200 ms. Essa taxa significa que cada conexão pode fazer cinco solicitações para o serviço web de Machine Learning por segundo. Com 20 conexões, o trabalho de Stream Analytics poderá processar 20.000 eventos em 200 ms e 100.000 eventos em um segundo.

Para processar 200.000 eventos por segundo, o trabalho do Stream Analytics precisa de 40 conexões simultâneas, provenientes de 12 SUs. O diagrama a seguir ilustra as solicitações do trabalho do Stream Analytics para o ponto de extremidade de serviço Web do Machine Learning – a cada 6 SUs há, no máximo, 20 conexões simultâneas com o serviço Web do Machine Learning.

![Dimensionar o Stream Analytics com as funções de Machine Learning, exemplo de trabalho dois](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Dimensionar o Stream Analytics com as funções do Machine Learning, exemplo de trabalho dois")

Em geral, sendo ***B*** o tamanho do lote e ***L*** a latência do serviço Web com o tamanho do lote B em milissegundos, a produtividade de um trabalho do Stream Analytics com ***N*** SUs será:

![Fórmula para Dimensionar o Stream Analytics com as funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Fórmula para Dimensionar o Stream Analytics com as funções do Machine Learning")

Você também pode configurar o 'máximo de chamadas simultâneas' no serviço da web de Machine Learning. É recomendável definir esse parâmetro para o valor máximo (no momento, 200).

Para saber mais sobre essa configuração, confira o [artigo sobre dimensionamento de serviços Web do Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemplo – análise de sentimento
O exemplo a seguir inclui um trabalho do Stream Analytics com a função do Machine Learning de análise de sentimento, conforme descrito no [Tutorial de integração do Machine Learning do Stream Analytics](stream-analytics-machine-learning-integration-tutorial.md).

A consulta é uma consulta simples e totalmente particionada, seguida pela função **sentimento**, conforme mostrado abaixo:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vamos examinar a configuração necessária para criar um trabalho de Stream Analytics, qual análise de sentimento faz de tweets a uma taxa de 10.000 tweets por segundo.

Usar 1 SU, poderia esse trabalho do Stream Analytics lida com o tráfego? O trabalho pode manter a entrada usando o tamanho de lote padrão de 1000. A latência de padrão de serviço web Machine Learning de análise de sentimento (com um tamanho de lote padrão de 1000) cria não mais do que um segundo de latência.

A latência **geral** ou ponta a ponta do trabalho do Stream Analytics normalmente seria de alguns segundos. Dê uma olhada mais detalhada nesse trabalho do Stream Analytics, *especialmente* nas chamadas de função do Machine Learning. Com um tamanho de lote de 1000, uma taxa de transferência de 10.000 eventos leva aproximadamente 10 solicitações ao serviço web. Mesmo com uma AU, há conexões simultâneas suficientes para acomodar esse tráfego de entrada.

Se a taxa de eventos de entrada aumentar em 100x, o trabalho do Stream Analytics precisará processar 1.000.000 de tweets por segundo. Há duas opções para realizar o aumento da escala:

1. Aumente o tamanho do lote.
2. Partição do fluxo de entrada para processar os eventos em paralelo.

Com a primeira opção, a **latência** do trabalho aumenta.

Com a segunda opção, você precisará provisionar mais SUs para ter mais simultâneas solicitações de serviço web de Machine Learning. Este número maior de SUs, aumenta o trabalho **custo**.

Vamos examinar a colocação em escala usando as seguintes medidas de latência para cada tamanho de lote:

| Latency | Tamanho do lote |
| --- | --- |
| 200 ms | lotes com 1000 eventos ou abaixo |
| 250 ms | lotes com 5000 eventos |
| 300 ms | lotes com 10.000 eventos |
| 500 ms | lotes com 25.000 eventos |

1. Usando a primeira opção (**não** Provisionando mais SUs). O tamanho do lote pode ser aumentado para **25.000**. Aumentar o tamanho do lote dessa maneira permitirá que o trabalho processasse 1.000.000 de eventos com 20 conexões simultâneas com o serviço web de Machine Learning (com uma latência de 500 ms por chamada). Portanto, a latência adicional do trabalho do Stream Analytics causada pelas solicitações de função de sentimento aumentaria de **200 ms** para **500 ms** em comparação com as solicitações de serviço Web do Machine Learning. No entanto, tamanho do lote **não é possível** ser aumentado infinitamente, pois os serviços web Machine Learning exige que o tamanho da carga de uma solicitação seja 4 MB ou menos e web de tempo limite de solicitações de serviço após 100 segundos de operação.
1. Usando a segunda opção, o tamanho do lote é deixado em 1000, com latência de serviço Web de 200 ms, cada 20 conexões simultâneas com o serviço Web seriam capazes de processar 1000 * 20 * 5 eventos = 100.000 por segundo. Então, para processar 1.000.000 eventos por segundo, o trabalho precisaria de 60 SUs. Comparando com a primeira opção, o trabalho do Stream Analytics faria mais solicitações em lote do serviço Web, gerando um aumento do custo.

Veja abaixo uma tabela sobre a produtividade do trabalho do Stream Analytics para SUs e tamanhos de lote diferentes (em número de eventos por segundo).

| tamanho do lote (latência de AM) | 500 (200 ms) | 1\.000 (200 ms) | 5\.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **3 SUs** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **6 SUs** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **12 SUs** |5\.000 |10.000 |40.000 |60.000 |100.000 |
| **18 SUs** |7\.500 |15.000 |60.000 |90.000 |150.000 |
| **24 SUs** |10.000 |20.000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25.000 |50.000 |200,000 |300.000 |500,000 |

Agora, você já deve ter uma boa compreensão de como as funções do Machine Learning funcionam no Stream Analytics. É provável que você também entenda que os jobs do Stream Analytics "extraem" dados de fontes de dados e cada "pull" retorna um lote de eventos para o trabalho do Stream Analytics processar. Como esse modelo de obtenção afeta as solicitações de serviço Web do Machine Learning?

Normalmente, o tamanho do lote que definimos para as funções de Aprendizado da Máquina não será exatamente divisível pelo número de eventos retornados por cada tarefa "pull" do Stream Analytics. Quando isso ocorre, o serviço Web do Machine Learning é chamado com lotes "parciais". Usando lotes parciais evita a incorrer em sobrecarga de latência do trabalho adicional em eventos de união pull de pull.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitoramentos relacionadas à função
Na área de Monitoramento de um trabalho do Stream Analytics, foram adicionadas três métricas relacionadas à função. Eles são **solicitações de função**, **eventos de função** e **solicitações de função com falha**, conforme mostrado na imagem abaixo.

![Métricas para Dimensionar o Stream Analytics com as funções de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Métricas para Dimensionar o Stream Analytics com as funções do Machine Learning")

Estas são as definições:

**SOLICITAÇÕES DE FUNÇÃO**: O número de solicitações de função.

**EVENTOS DE FUNÇÃO**: O número de eventos nas solicitações de função.

**SOLICITAÇÕES DE FUNÇÃO COM FALHA**: O número de solicitações de função com falha.

## <a name="key-takeaways"></a>Principais observações

Para dimensionar um trabalho de Stream Analytics com funções de Machine Learning, considere os seguintes fatores:

1. A taxa de eventos de entrada.
2. A latência tolerada para o trabalho de Stream Analytics em execução (e, portanto, o tamanho do lote das solicitações de serviço da web Machine Learning).
3. As SUs provisionado de análise de Stream e o número de solicitações de serviço web do Machine Learning (relacionadas à função custos adicionais).

Uma consulta do Stream Analytics totalmente particionada foi usada como exemplo. Se uma consulta mais complexa for necessária, o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) é um excelente recurso para obter ajuda adicional da equipe do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Stream Analytics, confira:

* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
