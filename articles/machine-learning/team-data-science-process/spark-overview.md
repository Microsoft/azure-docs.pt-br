---
title: "Visão geral da Ciência de dados usando Spark no Azure HDInsight | Microsoft Docs"
description: "O kit de ferramentas Spark MLlib traz recursos consideráveis de modelagem de aprendizado de máquina para o ambiente distribuído do HDInsight."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a4e1de99-a554-4240-9647-2c6d669593c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
ms.openlocfilehash: d9964ace6b59fa65f0f5d4caff28a4291047c8a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Visão geral da ciência de dados usando Spark no Azure HDInsight
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Este conjunto de tópicos mostra como usar o HDInsight Spark para concluir tarefas comuns de ciência de dados, tais como ingestão de dados, engenharia de recursos, modelagem e avaliação de modelo. Os dados usados são uma amostra do conjunto de dados de corridas e tarifas de táxi em Nova York de 2013. Os modelos criados incluem a regressão logística e linear, florestas aleatórias e árvores aumentadas gradientes. Os tópicos também mostram como armazenar esses modelos no Armazenamento de Blobs do Azure (WASB) e como pontuar e avaliar seu desempenho preditivo. Os tópicos mais avançados abordam como os modelos podem ser treinados usando a validação cruzada e a limpeza de hiperparâmetro. Este tópico de visão geral também referencia os tópicos que descrevem como configurar um cluster Spark necessário para concluir as etapas no passo a passos fornecido. 

## <a name="spark-and-mllib"></a>Spark e MLlib
[Spark](http://spark.apache.org/) é uma estrutura de processamento paralelo de software livre que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos analíticos de Big Data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos usados em cálculos de gráfico e aprendizado de máquina. [MLlib](http://spark.apache.org/mllib/) é a biblioteca de aprendizado de máquina escalonável do Spark que oferece recursos de modelagem de algoritmo para esse ambiente distribuído. 

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/hdinsight-apache-spark-overview.md) é a oferta do Spark de software livre hospedada no Azure. Ele também inclui suporte para **notebooks do Jupyter PySpark** no cluster Spark, que podem executar consultas Spark SQL interativas para transformar, filtrar e visualizar dados armazenados em Blobs do Azure (WASB). PySpark é a API do Python para o Spark. Os trechos de código que fornecem as soluções e mostram as plotagens relevantes para visualizar os dados aqui são executados em notebooks do Jupyter instalados nos clusters Spark. As etapas de modelagem nesses tópicos contêm código que mostra como treinar, avaliar, salvar e consumir cada tipo de modelo. 

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Instalação: Clusters do Spark e Notebooks Jupyter
As etapas de configuração e o código são fornecidos neste passo a passo para usar um HDInsight Spark 1.6. Porém, notebooks Jupyter são fornecidos tanto para o HDInsight Spark 1.6 quanto para os clusters Spark 2.0. Uma descrição de notebooks e links são fornecidos em [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório do GitHub que os contém. No entanto, o código mostrado aqui e nos notebooks vinculados é genérico e funcionarão em qualquer cluster Spark. Se você não estiver usando o HDInsight Spark, as etapas de configuração e gerenciamento do cluster poderão ser ligeiramente diferentes do que é mostrado aqui. Para sua conveniência, aqui estão os links para os notebooks Jupyter para Spark 1.6 (para execução no kernel do pySpark do servidor Jupyter Notebook) e Spark 2.0 (para execução no kernel pySpark3 do servidor Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Notebooks Spark 1.6
Esses notebooks devem ser executados no kernel pySpark do servidor de notebook Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): fornece informações sobre como executar a exploração, a modelagem e a pontuação de dados com vários algoritmos diferentes.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): inclui tópicos de notebook nº 1 e o desenvolvimento de modelos usando ajude e validação cruzada de hiperparâmetro.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): mostra como colocar em operação um modelo salvo usando o Python em clusters HDInsight.

### <a name="spark-20-notebooks"></a>Notebooks Spark 2.0
Esses notebooks devem ser executados no kernel pySpark3 do servidor de notebook Jupyter.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): este arquivo fornece informações sobre como realizar a exploração, modelagem e classificação de dados nos clusters do Spark 2.0 usando o conjunto de dados de tarifas e viagens de Táxi de Nova York descrito [aqui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Este notebook pode ser um bom ponto de partida para explorar rapidamente o código que fornecemos para o Spark 2.0. Para uma análise mais detalhada do notebook dos dados de Táxi de Nova York, veja o próximo notebook nesta lista. Veja as anotações após esta lista que comparam esses notebooks. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): esse arquivo mostra como executar disputa de dados (operações SQL Spark e dataframe), exploração, modelagem e pontuação, utilizando as viagens de táxi de NYC e conjunto de dados de tarifas descritas [aqui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): esse arquivo mostra como executar disputa de dados (operações SQL Spark e dataframe), exploração, modelagem e pontuação, utilizando um conjunto de dados de partidas no horário de uma companhia aérea conhecida de 2011 e 2012. Integramos o conjunto de dados da companhia aérea com os dados de clima do aeroporto (por exemplo, velocidade do vento, temperatura, altitude etc.) antes da modelagem, para que esses recursos de tempo pudessem ser incluídos no modelo.

<!-- -->

> [!NOTE]
> O conjunto de dados da companhia aérea foi adicionado ao notebook Spark 2.0 para melhor ilustrar os algoritmos de classificação. Consulte os links a seguir para obter informações sobre um conjunto de dados de partidas no horário e de clima:

>- Dados de partidas no horário em aeroportos: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Dados de clima aeroporto: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Os notebooks do Spark 2.0 dos conjuntos de dados de atrasos de voo e táxi de Nova York podem levar 10 minutos ou mais para serem executados (dependendo do tamanho do seu cluster de HDI). O primeiro notebook da lista acima mostra muitos aspectos da exploração, visualização e treinamento de modelo AM de dados em um notebook que leva menos tempo para ser executado com um conjunto de dados de Nova York do exemplo abaixo, em que os arquivos de tarifas e táxi foram previamente integrados: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Este notebook leva menos tempo para concluir (2 a 3 minutos) e pode ser um bom ponto de partida para a exploração rápida do código que fornecemos para o Spark 2.0. 

<!-- -->

Para obter orientação sobre a operacionalização de um modelo Spark 2.0 e consumo do modelo para pontuação, consulte o [documento sobre consumo do Spark 1.6](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) para obter um exemplo que descreva as etapas necessárias. Para usar no Spark 2.0, substitua o arquivo de código Python por [esse arquivo](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Pré-requisitos
Os procedimentos a seguir são relacionados ao Spark 1.6. Para as versões do Spark 2.0, use os notebooks descritos e vinculados anteriormente. 

1.Você precisa ter uma assinatura do Azure. Se ainda não tiver uma, veja [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Você precisa de um cluster Spark 1.6 para concluir este passo a passo. Para criar um, veja as instruções fornecidas em [Introdução: criar um Apache Spark no Azure HDInsight](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). O tipo de cluster e a versão são especificados no menu **Selecionar Tipo de Cluster** . 

![Configurar cluster](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Para ver um tópico que mostra como usar o Scala, em vez do Python, para concluir as tarefas em um processo completo de ciência de dados, confira [Ciência de dados usando Scala com Spark no Azure](scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>Os dados de Táxi em NYC em 2013
Os dados de Viagens de Táxi em NYC são cerca de 20 GB de arquivos compactados em valores separados por vírgulas (CSV) (cerca de 48 GB descompactados), que incluem mais de 173 milhões de viagens individuais e a tarifa paga por cada viagem. Cada registro de corrida inclui o local e horário de saída e chegada, número da carteira de motorista de taxista anônima e o número do medalhão (identificador exclusivo do táxi). Os dados abrangem todas as corridas no ano de 2013 e são fornecidos nos dois conjuntos de dados a seguir para cada mês:

1. Os arquivos CSV “trip_data” contêm detalhes da corrida, como o número de passageiros, pontos de saída e chegada, duração e extensão da corrida. Aqui estão alguns exemplos de registros:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Os arquivos CSV “trip_fare” contêm detalhes sobre as passagens pagas por cada corrida, como tipo de pagamento, valor da tarifa, custos adicionais e impostos, gorjetas e pedágios, e o valor total pago. Aqui estão alguns exemplos de registros:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Pegamos uma amostra de 0,1% desses arquivos CVS trip\_data and trip\_fare e os unimos em um único conjunto de dados que será usado como o conjunto de dados de entrada para este passo a passo. A chave exclusiva para unir trip\_data e trip\_fare é composta pelos campos: medallion, hack\_license e pickup\_datetime. Cada registro do conjunto de dados contém os seguintes atributos que representam uma corrida de táxi em NYC:

| Campo | Breve descrição |
| --- | --- |
| medallion |Licença do táxi em forma anônima (ID exclusiva do táxi) |
| hack_license |Número de licença do carro em forma anônima |
| vendor_id |ID do fornecedor do serviço de táxi |
| rate_code |Tarifa praticada pelos táxis em NYC |
| store_and_fwd_flag |Sinalizador de que os dados foram armazenados no veículo e encaminhados posteriormente |
| pickup_datetime |Data e hora do início da corrida |
| dropoff_datetime |Data e hora do final da corrida |
| pickup_hour |Hora da corrida |
| pickup_week |Semana do ano da corrida |
| weekday |Dia da semana (intervalo de 1 a 7) |
| passenger_count |Número de passageiros em uma corrida de táxi |
| trip_time_in_secs |Tempo da corrida em segundos |
| trip_distance |Distância percorrida na corrida em milhas |
| pickup_longitude |Longitude da corrida |
| pickup_latitude |Latitude da corrida |
| dropoff_longitude |Longitude do final da corrida |
| dropoff_latitude |Latitude do final da corrida |
| direct_distance |Distância direta entre os locais inicial e final da corrida |
| payment_type |Tipo de pagamento (dinheiro, cartão de crédito etc.) |
| fare_amount |Valor da tarifa |
| surcharge |Taxa adicional |
| mta_tax |Imposto da MTA |
| tip_amount |Valor da gorjeta |
| tolls_amount |Valor dos pedágios |
| total_amount |Valor total |
| tipped |Gorjeta (0/1 para não ou sim) |
| tip_class |Classe da gorjeta (0: US$ 0, 1: US$ 0 a 5, 2: US$ 6 a 10, 3: US$ 11 a 20, 4: mais de US$ 20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Executar código de um bloco de anotações Jupyter no cluster Spark
É possível iniciar o Notebook do Jupyter no portal do Azure. Encontre o cluster Spark no painel e clique nele para entrar na página de gerenciamento de seu cluster. Para abrir o notebook associado ao cluster Spark, clique nos **Painéis do Cluster** -> **Notebook Jupyter**.

![Painéis de cluster](./media/spark-overview/spark-jupyter-on-portal.png)

Você também pode navegar até ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** para acessar os Notebooks Jupyter. Substitua a parte CLUSTERNAME desta URL pelo nome do seu próprio cluster. Você precisa da senha de sua conta de administrador para acessar os notebooks.

![Procurar Notebooks Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Selecione PySpark para ver um diretório que contém alguns exemplos de notebooks predefinidos que usam a API do PySpark. Os notebooks que contêm os exemplos de código para esse conjunto de tópicos do Spark estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Você pode carregar os blocos de anotações diretamente do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) para o servidor do bloco de anotações Jupyter no seu cluster Spark. Na home page do seu Jupyter, clique no botão **Carregar** na parte direita da tela. Ele abre o explorador de arquivos. Aqui, você pode colar a URL do GitHub (conteúdo bruto) do Notebook e clicar em **Abrir**. 

Você vê o nome do arquivo em sua lista de arquivos do Jupyter, com um botão **Carregar** novamente. Clique nesse botão **Carregar** . Agora, você importou o notebook. Repita estas etapas deste passo a passo para carregar outros notebooks.

> [!TIP]
> Clique com o botão direito nos links no seu navegador e selecione **Copiar Link** para obter a URL do conteúdo bruto github. Você pode colar essa URL na caixa de diálogo do explorador de arquivos de carregamento Jupyter.
> 
> 

Agora você pode:

* Consulte o código clicando no notebook.
* Executar cada célula pressionando **SHIFT-ENTER**.
* Executar o notebook inteiro clicando em **Célula** -> **Executar**.
* Usar a visualização automática de consultas.

> [!TIP]
> O kernel PySpark visualiza automaticamente a saída das consultas SQL (HiveQL). Você terá a opção de selecionar entre vários tipos diferentes de visualização (Tabela, Pizza, Linha, Área ou Barra) usando os botões de menu **Tipo** no notebook:
> 
> 

![Curva ROC de regressão logística de abordagem genérica](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>O que vem a seguir?
Agora que configurou um cluster HDInsight Spark e carregou os notebooks Jupyter, você está pronto para usar os tópicos que correspondem aos três notebooks PySpark. Eles mostram como explorar os dados e como criar e consumir modelos. Onotebook de exploração e modelagem de dados avançadas mostra como incluir validação cruzada, limpeza de hiperparâmetro e avaliação de modelo. 

**Exploração de Dados e modelagem com o Spark:** explore o conjunto de dados, crie, pontue e avalie os modelos de aprendizado de máquina usando o tópico [Create binary classification and regression models for data with the Spark MLlib toolkit](spark-data-exploration-modeling.md) (Criar modelos de regressão e classificação binária para dados com o kit de ferramentas MLlib do Spark).

**Consumo de modelo:** para saber como pontuar os modelos de classificação e regressão criados neste tópico, confira [Pontuar modelos de aprendizado de máquina criados no Spark](spark-model-consumption.md).

**Validação cruzada e limpeza de hiperparâmetro**: confira [Modelagem e exploração de dados avançados com o Spark](spark-advanced-data-exploration-modeling.md) para saber como os modelos podem ser treinados usando a validação cruzada e a limpeza de hiperparâmetro

