---
title: "Dimensionando o trabalho com o Azure Stream Analytics e as funções do Azure Machine Learning | Microsoft Docs"
description: "Saiba como dimensionar corretamente os trabalhos do Stream Analytics (particionamento, quantidade de SU e muito mais) ao usar funções do Azure Machine Learning."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 47ce7c5e-1de1-41ca-9a26-b5ecce814743
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 1e1c3724462a4d2a67eab3ef42867d2aeb5d3fa1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Dimensionar seu trabalho do Stream Analytics com funções do Azure Machine Learning
Geralmente, é fácil configurar um trabalho do Stream Analytics e executar alguns dados de exemplo nele. O que podemos fazer quando precisamos executar o mesmo trabalho com um volume de dados maior? Precisamos entender como configurar o trabalho do Stream Analytics para que ele possa ser escalado. Neste documento, nos concentraremos nos aspectos especiais do dimensionamento de trabalhos do Stream Analytics com funções do Machine Learning. Para saber mais sobre como dimensionar trabalhos do Stream Analytics em geral, confira o artigo [Dimensionar os trabalhos do Stream Analytics](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>O que é uma função do Azure Machine Learning no Stream Analytics?
Uma função do Machine Learning no Stream Analytics pode ser usada como uma chamada de função normal na linguagem de consulta do Stream Analytics. No entanto, nos bastidores, as chamadas de função são, na verdade, solicitações de Serviço Web do Azure Machine Learning. Os serviços Web do Machine Learning oferecem suporte à inclusão de várias linhas em "lotes", o que é chamado de lote simplificado, na mesma chamada à API de serviço Web, a fim de melhorar a produtividade geral. Confira os artigos a seguir para obter mais detalhes. [Funções do Azure Machine Learning no Stream Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) e [Serviços Web do Azure Machine Learning](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar seu trabalho do Stream Analytics com funções de Machine Learning
Ao configurar uma função do Machine Learning para um trabalho do Stream Analytics, há dois parâmetros a serem considerados, o tamanho do lote das chamadas de função do Machine Learning e as SUs (unidades de streaming) provisionadas para o trabalho do Stream Analytics. Para determinar seus valores apropriados, é necessário tomar uma decisão primeiro entre a latência e a produtividade, ou seja, latência do trabalho do Stream Analytics e a produtividade de cada SU. As SUs podem ser adicionadas sempre a um trabalho a fim de aumentar a produtividade de uma consulta do Stream Analytics bem particionada, embora SUs adicionais aumentem o custo da execução do trabalho.

Portanto, é importante determinar a *tolerância* de latência da execução de um trabalho do Stream Analytics. A latência adicional proveniente da execução de solicitações de serviço do Machine Learning do Azure aumenta naturalmente com o tamanho do lote, que vai compor a latência do trabalho do Stream Analytics. Por outro lado, o aumento do tamanho do lote permite que o trabalho do Stream Analytics processe *mais eventos com o *mesmo número* de solicitações de serviço Web do Machine Learning. Geralmente, o aumento da latência do serviço Web do Machine Learning é sub-linear ao aumento do tamanho do lote. Portanto, é importante considerar o tamanho de lote mais econômico e eficiente para um serviço Web do Machine Learning em qualquer situação. O tamanho de lote padrão para as solicitações de serviço Web é 1.000 e pode ser modificado usando a [API REST do Stream Analytics](https://msdn.microsoft.com/library/mt653706.aspx "API REST do Stream Analytics") ou o [Cliente PowerShell para Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md "Cliente PowerShell para Stream Analytics").

Depois de determinar um tamanho de lote, o número de SUs (unidades de streaming) pode ser determinada com base no número de eventos que a função precisa processar por segundo. Para obter mais informações sobre o unidades de streaming, consulte Dimensionar trabalhos do [Trabalhos de escala do Stream Analytics](stream-analytics-scale-jobs.md).

Em geral, há 20 conexões simultâneas com o serviço Web do Machine Learning para cada seis SUs, com exceção dos trabalhos de uma SU e os trabalhos de três SUs que recebem 20 conexões simultâneas também.  Por exemplo, se a taxa de dados de entrada for 200.000 eventos por segundo, e o tamanho do lote for deixado com o valor padrão de 1000, a latência de serviço Web resultante com um lote simplificado de 1000 eventos será de 200 ms. Isso significa que cada conexão pode fazer cinco solicitações para o serviço Web do Machine Learning em um segundo. Com 20 conexões, o trabalho do Stream Analytics poderá processar 20.000 eventos em 200 ms e, portanto, 100.000 eventos em um segundo. Então, para processar 200.000 eventos por segundo, o trabalho do Stream Analytics precisará de 40 conexões simultâneas, provenientes de 12 SUs. O diagrama a seguir ilustra as solicitações do trabalho do Stream Analytics para o ponto de extremidade de serviço Web do Machine Learning – a cada 6 SUs há, no máximo, 20 conexões simultâneas com o serviço Web do Machine Learning.

![Dimensionar o Stream Analytics com as funções de Machine Learning, exemplo de trabalho dois](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Dimensionar o Stream Analytics com as funções do Machine Learning, exemplo de trabalho dois")

Em geral, sendo ***B*** o tamanho do lote e ***L*** a latência do serviço Web com o tamanho do lote B em milissegundos, a produtividade de um trabalho do Stream Analytics com ***N*** SUs será:

![Fórmula para Dimensionar o Stream Analytics com as funções do Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Fórmula para Dimensionar o Stream Analytics com as funções do Machine Learning")

Uma consideração adicional pode ser o número “máximo de chamadas simultâneas” no lado do serviço Web do Machine Learning. Recomendamos a definição como o valor máximo (no momento, 200).

Para saber mais sobre essa configuração, confira o [artigo sobre dimensionamento de serviços Web do Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemplo – análise de sentimento
O exemplo a seguir inclui um trabalho do Stream Analytics com a função do Machine Learning de análise de sentimento, conforme descrito no [Tutorial de integração do Machine Learning do Stream Analytics](stream-analytics-machine-learning-integration-tutorial.md).

A consulta é simples e totalmente particionada, seguida pela função **sentiment** , conforme mostrado abaixo:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Considere o seguinte cenário; com uma produtividade de 10.000 tweets por segundo, um trabalho do Stream Analytics deve ser criado para executar análise de sentimento dos tweets (eventos). Com uma SU, esse trabalho do Stream Analytics poderá lidar com o tráfego? Com o tamanho de lote padrão de 1000, o trabalho deve ser capaz de acompanhar a entrada. Além disso, a função adicional do Machine Learning não deve gerar mais do que um segundo de latência, que é a latência geral padrão do serviço Web do Machine Learning de análise de sentimento (com um tamanho de lote padrão de 1000). A latência **geral** ou ponta a ponta do trabalho do Stream Analytics normalmente seria de alguns segundos. Dê uma olhada mais detalhada nesse trabalho do Stream Analytics, *especialmente* nas chamadas de função do Machine Learning. Com o tamanho de lote de 1000, uma produtividade de 10.000 eventos precisa de aproximadamente 10 solicitações ao serviço Web. Mesmo com uma SU, há conexões simultâneas suficientes para acomodar esse tráfego de entrada.

Mas, e se a taxa de eventos de entrada aumentar em 100x, e agora o trabalho do Stream Analytics precisar processar 1.000.000 tweets por segundo? Há duas opções:

1. Aumentar o tamanho do lote, ou
2. Particionar o fluxo de entrada a fim de processar os eventos em paralelo

Com a primeira opção, a **latência** do trabalho aumenta.

Com a segunda opção, seria necessário provisionar mais SUs e, portanto, gerar mais solicitações simultâneas do serviço Web do Machine Learning. Isso significa que o **custo** do trabalho aumenta.

Vamos supor que a latência do serviço Web do Machine Learning de análise de sentimento seja de 200 ms para lotes com 1000 eventos ou menos, 250 ms para lotes com 5000 eventos, 300 ms para lotes com 10.000 eventos ou 500 ms para lotes com 25.000 eventos.

1. Usando a primeira opção (**não** provisionando mais SUs), o tamanho do lote pode ser aumentado para **25.000**. Isso, por sua vez, permitiria que o trabalho processasse 1.000.000 de eventos com 20 conexões simultâneas para o serviço Web do Machine Learning (com uma latência de 500 ms por chamada). Portanto, a latência adicional do trabalho do Stream Analytics causada pelas solicitações de função de sentimento aumentaria de **200 ms** para **500 ms** em comparação com as solicitações de serviço Web do Machine Learning. No entanto, **não é possível** aumentar o tamanho do lote infinitamente, pois os serviços Web do Machine Learning exige que o tamanho da carga de uma solicitação seja de 4 MB ou menos e o tempo limite das solicitações de serviço Web esgota após 100 segundos de operação.
2. Usando a segunda opção, o tamanho do lote é deixado como 1000, com latência de serviço Web de 200 ms, e cada 20 conexões simultâneas com o serviço Web seriam capazes de processar 1000 * 20 * 5 eventos = 100.000 por segundo. Então, para processar 1.000.000 eventos por segundo, o trabalho precisaria de 60 SUs. Comparando com a primeira opção, o trabalho do Stream Analytics faria mais solicitações em lote do serviço Web, gerando um aumento do custo.

Veja abaixo uma tabela sobre a produtividade do trabalho do Stream Analytics para SUs e tamanhos de lote diferentes (em número de eventos por segundo).

| tamanho do lote (latência de AM) | 500 (200 ms) | 1.000 (200 ms) | 5.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2.500 |5.000 |20.000 |30.000 |50.000 |
| **3 SUs** |2.500 |5.000 |20.000 |30.000 |50.000 |
| **6 SUs** |2.500 |5.000 |20.000 |30.000 |50.000 |
| **12 SUs** |5.000 |10.000 |40.000 |60.000 |100.000 |
| **18 SUs** |7.500 |15.000 |60.000 |90.000 |150.000 |
| **24 SUs** |10.000 |20.000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25.000 |50.000 |200.000 |300.000 |500.000 |

Agora, você já deve ter uma boa compreensão de como as funções do Machine Learning funcionam no Stream Analytics. Provavelmente você também compreende que os trabalhos do Stream Analytics "obtêm" dados de fontes de dados, e que cada "obtenção" retorna um lote de eventos para o trabalho do Stream Analytics processar. Como esse modelo de obtenção afeta as solicitações de serviço Web do Machine Learning?

Normalmente, o tamanho do lote que definimos para as funções do Machine Learning não pode ser dividido exatamente pelo número de eventos retornados por cada “obtenção” do trabalho do Stream Analytics. Quando isso ocorrer, o serviço Web do Machine Learning será chamado com lotes "parciais". Isso é feito para não causar sobrecarga adicional de latência de trabalho em eventos acumulados a cada pull.

## <a name="new-function-related-monitoring-metrics"></a>Novas métricas de monitoramentos relacionadas à função
Na área de Monitoramento de um trabalho do Stream Analytics, foram adicionadas três métricas relacionadas à função. São elas: SOLICITAÇÕES DE FUNÇÃO, EVENTOS DE FUNÇÃO E SOLICITAÇÕES DE FUNÇÃO COM FALHA, conforme mostra a imagem abaixo.

![Métricas para Dimensionar o Stream Analytics com as funções de Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Métricas para Dimensionar o Stream Analytics com as funções do Machine Learning")

Estas são as definições:

**SOLICITAÇÕES DE FUNÇÃO**: o número de solicitações de função.

**EVENTOS DE FUNÇÃO**: o número de eventos nas solicitações de função.

**SOLICITAÇÕES DE FUNÇÃO COM FALHA**: o número de solicitações de função com falha.

## <a name="key-takeaways"></a>Principais observações
Para resumir os principais pontos, para dimensionar um trabalho do Stream Analytics com funções do Machine Learning, considere os itens a seguir:

1. A taxa de eventos de entrada
2. A latência tolerada para execução do trabalho do Stream Analytics (e, portanto, o tamanho do lote das solicitações de serviço Web do Machine Learning)
3. As SUs do Stream Analytics provisionadas e o número de solicitações de serviço Web do Machine Learning (os custos adicionais relacionados à função)

Uma consulta do Stream Analytics totalmente particionada foi usada como exemplo. Caso seja necessária uma consulta mais complexa, o [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) é um excelente recurso para obter mais ajuda da equipe do Stream Analytics.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Stream Analytics, confira:

* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
