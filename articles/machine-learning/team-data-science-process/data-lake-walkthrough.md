---
title: Ciência de Dados Escalonáveis com o Azure Data Lake - Processo de Ciência de Dados de Equipe
description: Como usar o Azure Data Lake para realizar tarefas de classificação binária e de exploração de dados em um conjunto de dados.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e6eb0be4d9946907dc5bb2f22b27530a27a37aec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021445"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Ciência de Dados Escalonáveis com o Azure Data Lake: um passo a passo de ponta a ponta
Este passo a passo mostra como usar o Azure Data Lake para exploração de dados e tarefas de classificação binária em uma amostra do conjunto de dados de tarifas e corridas de táxi de Nova York para prever se uma gorjeta será ou não paga por uma tarifa. Ele orienta você pelas etapas do [Processo de Ciência de Dados de Equipe](./index.yml), de ponta a ponta, da aquisição de dados até o treinamento de modelo e, em seguida, para a implantação de um serviço Web que publica o modelo.

## <a name="technologies"></a>Tecnologias

Essas tecnologias são usadas neste passo a passos.
* Análise Azure Data Lake
* U-SQL e Visual Studio
* Python
* Azure Machine Learning
* Scripts


### <a name="azure-data-lake-analytics"></a>Análise Azure Data Lake
O [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) tem todos os recursos necessários para tornar mais fácil para os cientistas de dados a tarefa de armazenar dados de qualquer forma, velocidade e tamanho e para conduzir o processamento de dados, análise avançada e modelagem de aprendizado de máquina, com alta escalabilidade e de uma maneira econômica.   Você paga por trabalho, somente quando os dados estão realmente sendo processados. A Análise Azure Data Lake inclui o U-SQL, uma linguagem de consulta que mescla a natureza declarativa simples e familiar do SQL com o poder expressivo do C# para fornecer capacidade de consulta distribuída escalonável. Ele permite que você processe dados não estruturados aplicando o esquema na leitura, na inserção de lógica personalizada e em UDFs (funções definidas pelo usuário), e inclui extensibilidade para habilitar o controle refinado sobre como executar em grande escala. Para saber mais sobre a filosofia de design por trás do U-SQL, consulte [postagem de blog do Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Data Lake Analytics também é uma parte importante do Cortana Analytics Suite e funciona com o Azure Synapse Analytics, Power BI e Data Factory. Essa combinação fornece uma Big Data completa de nuvem e uma plataforma de análise avançada.

Este passo a passo começa descrevendo como instalar os pré-requisitos e os recursos que são necessários para concluir as tarefas de processo de ciência de dados. Em seguida, ele descreve as etapas de processamento de dados usando U-SQL e conclui mostrando como usar o Python e o hive com Azure Machine Learning Studio (clássico) para criar e implantar os modelos de previsão.

### <a name="u-sql-and-visual-studio"></a>U-SQL e Visual Studio
Este passo a passo recomenda o uso do Visual Studio para editar scripts U-SQL para processar o conjunto de dados. Os scripts U-SQL são descritos aqui e fornecidos em um arquivo separado. O processo inclui ingestão, exploração e amostragem dos dados. Ele também mostra como executar um trabalho com script U-SQL no portal do Azure. Tabelas de hive são criadas para os dados em um cluster HDInsight associado para facilitar a compilação e implantação de um modelo de classificação binária no Azure Machine Learning Studio.

### <a name="python"></a>Python
Este passo a passo também contém uma seção que mostra como compilar e implantar um modelo preditivo usando o Python com o Azure Machine Learning Studio. Nós fornecemos um bloco de anotações Jupyter com os scripts Python para as etapas neste processo. O bloco de notas inclui código para algumas etapas adicionais de engenharia de recursos e construção de modelos, como modelagem de regressão e classificação multiclasse, além do modelo de classificação binária descrito aqui. A tarefa de regressão consiste em prever o valor da gorjeta com base em outros recursos de gorjeta.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (clássico) é usado para criar e implantar os modelos de previsão usando duas abordagens: primeiro com scripts Python e, em seguida, com tabelas Hive em um cluster HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
Somente as etapas principais são descritas neste passo a passo. Você pode baixar o **script U-SQL** completo e o **Bloco de anotações Jupyter** no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar esses tópicos, você deve ter o seguinte:

* Uma assinatura do Azure. Se ainda não tiver uma, veja [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Recomendado] Visual Studio 2013 ou posterior. Se ainda não tiver uma dessas versões instaladas, você poderá baixar uma versão Community gratuita da [Comunidade do Visual Studio](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Em vez do Visual Studio, você também pode usar o Portal do Azure para enviar consultas do Azure Data Lake. Forneceremos instruções sobre como fazê-lo, tanto com o Visual Studio quanto no portal, na seção intitulada **Processar dados com o U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Preparar ambiente de ciência de dados para o Azure Data Lake
Para preparar o ambiente de ciência de dados para este passo a passo, crie os seguintes recursos:

* Azure Data Lake Storage (ADLS)
* ADLA (Análise Azure Data Lake)
* Conta de armazenamento de Blobs do Azure
* Conta Azure Machine Learning Studio (clássica)
* Ferramentas do Azure Data Lake para Visual Studio (Recomendado)

Esta seção fornece instruções sobre como criar cada um desses recursos. Se optar por usar tabelas de Hive com o Azure Machine Learning, em vez do Python, para compilar um modelo, você também precisará provisionar um cluster HDInsight (Hadoop). Esse procedimento alternativo está descrito na seção Opção 2.


> [!NOTE]
> O **Azure Data Lake Store** pode ser criado separadamente ou quando você cria a **Azure Data Lake Analytics** como o armazenamento padrão. As instruções são referenciadas para criar cada um desses recursos separadamente, mas a conta de armazenamento do Data Lake não precisa ser criada separadamente.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Criar um Azure Data Lake Storage


Crie um ADLS no [Portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [Criar um cluster HDInsight com o Data Lake Store usando o Portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de configurar a Identidade AAD do Cluster na folha **Fonte de Dados** da folha **Configuração Opcional** ali descrita.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Criar uma conta da Análise Azure Data Lake
Crie uma conta da ADLA no [Portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [Tutorial: introdução ao Azure Data Lake Analytics usando o Portal do Azure](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de Armazenamento de Blobs do Azure
Crie sua conta de armazenamento de Blobs do Azure no [Portal do Azure](https://portal.azure.com). Para obter detalhes, consulte a seção criar uma conta de armazenamento em [sobre as contas de armazenamento do Azure](../../storage/common/storage-account-create.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Configurar uma conta Azure Machine Learning Studio (clássica)
Inscreva-se/Azure Machine Learning Studio (clássico) na página do [Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning/) . Clique no botão **Comece agora mesmo** e, em seguida, escolha um "Workspace Gratuito" ou "Workspace Padrão". Depois disso, você poderá criar experimentos no Azure Machine Learning Studio.

### <a name="install-azure-data-lake-tools-recommended"></a>Instalar Ferramentas do Azure Data Lake [Recomendado]
Instale as Ferramentas do Azure Data Lake para sua versão do Visual Studio das [Ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Após a conclusão da instalação, abra o Visual Studio. Você deve ver a guia Data Lake no menu na parte superior. Os recursos do Azure devem aparecer no painel esquerdo quando você entra na sua conta do Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>O conjunto de dados Corridas de Táxi de NYC
O conjunto de dados usado aqui é um conjunto de dados disponível publicamente, o [conjunto de dados Corridas de Táxi de NYC](https://www.andresmh.com/nyctaxitrips/). Os dados de Corridas de Táxi de NYC são formados por cerca de 20 GB de arquivos CSV compactados (aproximadamente 48 GB descompactados) que incluem mais de 173 milhões de corridas individuais, com tarifas pagas por cada corrida. Cada registro de corrida inclui o local e o horário de saída e chegada, o número da carteira de habilitação do taxista anônimo e o número de medalhão (ID exclusiva do táxi). Os dados abrangem todas as corridas no ano de 2013 e são fornecidos nos dois conjuntos de dados a seguir para cada mês:

O CSV 'trip_data' contém detalhes da corrida, como o número de passageiros, pontos de saída e chegada, duração e quilometragem da corrida. Aqui estão alguns exemplos de registros:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

O CSV 'trip_fare' contém detalhes sobre as tarifas pagas em cada corrida, como tipo de pagamento, valor da tarifa, custos adicionais e impostos, gorjetas e pedágios, e o valor total pago. Aqui estão alguns exemplos de registros:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

A chave exclusiva para unir trip\_data e trip\_fare é composta destes três campos: medallion, hack\_license e pickup\_datetime. Os arquivos CSV brutos podem ser acessados de um blob de armazenamento do Azure. O script U-SQL para essa associação está na seção [Unir tabelas de corrida e tarifa](#join) .

## <a name="process-data-with-u-sql"></a>Processar dados com o U-SQL
As tarefas de processamento de dados ilustradas nesta seção incluem ingestão, verificação de qualidade, exploração e amostragem dos dados. Como unir tabelas de corrida e de tarifa também é mostrado. A seção final mostra a execução de um trabalho com script U-SQL no portal do Azure. Eis os links para cada subseção:

* [Ingestão de dados: dados de leitura de blob público](#ingest)
* [Verificações de qualidade de dados](#quality)
* [Exploração de dados](#explore)
* [Unir tabelas de corrida e tarifa](#join)
* [Amostragem de dados](#sample)
* [Executar trabalhos com U-SQL](#run)

Os scripts U-SQL são descritos aqui e fornecidos em um arquivo separado. Você pode baixar os **scripts U-SQL** completos do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Para executar o U-SQL, abra o Visual Studio, clique em **Arquivo --> Novo --> Projeto**, escolha **Projeto U-SQL**, nomeie-o e salve-o em uma pasta.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> É possível usar o Portal do Azure para executar o U-SQL em vez do Visual Studio. Você pode navegar até o recurso do Azure Data Lake Analytics no portal e enviar consultas diretamente, como ilustrado na figura a seguir:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Ingestão de dados: ler dados do blob público

O local dos dados no blob do Azure é referenciado como **WASB://Container \_ nome \@ da \_ conta de armazenamento de blob \_ \_ Name.blob.Core.Windows.net/BLOB_NAME** e pode ser extraído usando **Extractors.Csv ()**. Substitua seu próprio nome do contêiner e o nome da conta de armazenamento nos seguintes scripts para nome do contêiner \_ \@ nome da conta de armazenamento do blob \_ \_ \_ no endereço WASB. Como os nomes de arquivo estão no mesmo formato, é possível usar o **Trip \_ Data \_ \{ \* \} . csv** para ler todos os 12 arquivos de viagem.

```sql
///Read in Trip data
@trip0 =
    EXTRACT
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string
// This is reading 12 trip data from blob
FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
USING Extractors.Csv();
```

Como há cabeçalhos na primeira linha, é necessário remover os cabeçalhos e alterar os tipos de coluna para aqueles apropriados. Você pode salvar os dados processados para Azure Data Lake Storage usando **swebhdfs://data_lake_storage_name. azuredatalakestorage. net/Folder_Name/file_name** _ ou a conta de armazenamento de BLOBs do Azure usando  **wasb://container_name \@ blob_storage_account_name. blob. Core. windows. net/BLOB_NAME**.

```sql
// change data types
@trip =
    SELECT
    medallion,
    hack_license,
    vendor_id,
    rate_code,
    store_and_fwd_flag,
    DateTime.Parse(pickup_datetime) AS pickup_datetime,
    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
    Int32.Parse(passenger_count) AS passenger_count,
    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
    Double.Parse(trip_distance) AS trip_distance,
    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
FROM @trip0
WHERE medallion != "medallion";

////output data to ADL
OUTPUT @trip
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
USING Outputters.Csv();

////Output data to blob
OUTPUT @trip
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
USING Outputters.Csv();
```

Da mesma forma, podemos realizar a leitura dos conjuntos de dados de tarifa. Clique com o botão direito do mouse em Azure Data Lake Storage, você pode optar por examinar seus dados em **portal do Azure--> data Explorer** ou **Explorador de arquivos** no Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Verificações de qualidade de dados
Após as tabelas de corrida e de tarifa terem sido lidas, as verificações de qualidade de dados podem ser feitas conforme descrito a seguir. Os arquivos CSV resultantes podem ser gerados para o armazenamento de BLOBs do Azure ou Azure Data Lake Storage.

Localize o número de medalhões e o número individual dos medalhões:

```sql
///check the number of medallions and unique number of medallions
@trip2 =
    SELECT
    medallion,
    vendor_id,
    pickup_datetime.Month AS pickup_month
    FROM @trip;

@ex_1 =
    SELECT
    pickup_month,
    COUNT(medallion) AS cnt_medallion,
    COUNT(DISTINCT(medallion)) AS unique_medallion
    FROM @trip2
    GROUP BY pickup_month;
    OUTPUT @ex_1
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
USING Outputters.Csv();
```

Localize os medalhões com mais de 100 corridas:

```sql
///find those medallions that had more than 100 trips
@ex_2 =
    SELECT medallion,
           COUNT(medallion) AS cnt_medallion
    FROM @trip2
    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
    GROUP BY medallion
    HAVING COUNT(medallion) > 100;
    OUTPUT @ex_2
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
USING Outputters.Csv();
```

Localize esses registros inválidos em termos de pickup_longitude:

```sql
///find those invalid records in terms of pickup_longitude
@ex_3 =
    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
    FROM @trip
    WHERE
    pickup_longitude <- 90 OR pickup_longitude > 90;
    OUTPUT @ex_3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
USING Outputters.Csv();
```

Encontre os valores ausentes para algumas variáveis:

```sql
//check missing values
@res =
    SELECT *,
           (medallion == null? 1 : 0) AS missing_medallion
    FROM @trip;

@trip_summary6 =
    SELECT
        vendor_id,
    SUM(missing_medallion) AS medallion_empty,
    COUNT(medallion) AS medallion_total,
    COUNT(DISTINCT(medallion)) AS medallion_total_unique
    FROM @res
    GROUP BY vendor_id;
OUTPUT @trip_summary6
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
USING Outputters.Csv();
```

### <a name="data-exploration"></a><a name="explore"></a>Exploração de dados
Podemos fazer alguma exploração de dados para obter uma melhor compreensão dos dados.

Localize a distribuição de corridas com e sem gorjeta:

```sql
///tipped vs. not tipped distribution
@tip_or_not =
    SELECT *,
           (tip_amount > 0 ? 1: 0) AS tipped
    FROM @fare;

@ex_4 =
    SELECT tipped,
           COUNT(*) AS tip_freq
    FROM @tip_or_not
    GROUP BY tipped;
    OUTPUT @ex_4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
USING Outputters.Csv();
```

Localize a distribuição de valores de gorjeta com valores de corte: 0, 5, 10 e 20 dólares.

```sql
//tip class/range distribution
@tip_class =
    SELECT *,
           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @fare;
@ex_5 =
    SELECT tip_class,
           COUNT(*) AS tip_freq
    FROM @tip_class
    GROUP BY tip_class;
    OUTPUT @ex_5
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
USING Outputters.Csv();
```

Localize as estatísticas básicas de distância de corrida:

```sql
// find basic statistics for trip_distance
@trip_summary4 =
    SELECT
        vendor_id,
        COUNT(*) AS cnt_row,
        MIN(trip_distance) AS min_trip_distance,
        MAX(trip_distance) AS max_trip_distance,
        AVG(trip_distance) AS avg_trip_distance
    FROM @trip
    GROUP BY vendor_id;
OUTPUT @trip_summary4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
USING Outputters.Csv();
```

Localize os percentuais de distância de corrida:

```sql
// find percentiles of trip_distance
@trip_summary3 =
    SELECT DISTINCT vendor_id AS vendor,
                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
    FROM @trip;
   // group by vendor_id;
OUTPUT @trip_summary3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
USING Outputters.Csv();
```

### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Unir tabelas de corrida e tarifa
Tabelas de corrida e tarifa podem ser unidas por medalhão, hack_license e pickup_time.

```sql
//join trip and fare table

@model_data_full =
SELECT t.*,
f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
(f.tip_amount > 0 ? 1: 0) AS tipped,
(f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
FROM @trip AS t JOIN  @fare AS f
ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

//// output to blob
OUTPUT @model_data_full
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
USING Outputters.Csv();

////output data to ADL
OUTPUT @model_data_full
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
USING Outputters.Csv();
```

Para cada nível de contagem de passageiros, calcule o número de registros, valor médio da gorjeta, variação do valor da gorjeta, percentual de corridas com gorjeta.

```sql
// contingency table
@trip_summary8 =
    SELECT passenger_count,
           COUNT(*) AS cnt,
           AVG(tip_amount) AS avg_tip_amount,
           VAR(tip_amount) AS var_tip_amount,
           SUM(tipped) AS cnt_tipped,
           (float)SUM(tipped)/COUNT(*) AS pct_tipped
    FROM @model_data_full
    GROUP BY passenger_count;
    OUTPUT @trip_summary8
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
USING Outputters.Csv();
```

### <a name="data-sampling"></a><a name="sample"></a>Amostragem de dados
Primeiro, selecione aleatoriamente 0,1% dos dados da tabela unida:

```sql
//random select 1/1000 data for modeling purpose
@addrownumberres_randomsample =
SELECT *,
        ROW_NUMBER() OVER() AS rownum
FROM @model_data_full;

@model_data_random_sample_1_1000 =
SELECT *
FROM @addrownumberres_randomsample
WHERE rownum % 1000 == 0;

OUTPUT @model_data_random_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
USING Outputters.Csv();
```

Em seguida, faça a amostragem estratificada pela variável binária tip_class:

```sql
//stratified random select 1/1000 data for modeling purpose
@addrownumberres_stratifiedsample =
SELECT *,
        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
FROM @model_data_full;

@model_data_stratified_sample_1_1000 =
SELECT *
FROM @addrownumberres_stratifiedsample
WHERE rownum % 1000 == 0;
//// output to blob
OUTPUT @model_data_stratified_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
////output data to ADL
OUTPUT @model_data_stratified_sample_1_1000
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
```

### <a name="run-u-sql-jobs"></a><a name="run"></a>Executar trabalhos com U-SQL
Depois de editar scripts U-SQL, você pode enviá-los ao servidor usando sua conta de Azure Data Lake Analytics. Clique em **Data Lake**, **Enviar Trabalho**, selecione sua **Conta de Análise**, escolha **Paralelismo** e clique no botão **Enviar**.

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Quando o trabalho for compilado com êxito, o status do seu trabalho será exibido no Visual Studio para monitoramento. Depois que o trabalho for concluído, você poderá até reproduzir o processo de execução do trabalho e descobrir as etapas de afunilamento para melhorar a eficiência do trabalho. Você também pode ir para o Portal do Azure para verificar o status dos trabalhos com U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Agora, você pode verificar os arquivos de saída no armazenamento de Blobs do Azure ou Portal do Azure. Use os dados da amostra estratificada para nossa modelagem na próxima etapa.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Compilar e implantar modelos no Azure Machine Learning
Duas opções disponíveis para você efetuar pull de dados no Azure Machine Learning para compilar e

* Na primeira opção, você usa os dados da amostra que foram gravados em um Blob do Azure (da etapa **Amostragem de dados** acima) e usa o Python para compilar e implantar modelos do Azure Machine Learning.
* Na segunda opção, você consulta os dados no Azure Data Lake diretamente usando uma consulta de Hive. Essa opção exige que você crie um novo cluster HDInsight ou use um cluster HDInsight existente no qual as tabelas de Hive apontam para os dados de táxi de Nova York no Armazenamento do Azure Data Lake.  Essas duas opções são discutidas neste artigo.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opção 1: usar o Python para compilar e implantar modelos de aprendizado de máquina
Para compilar e implantar modelos de aprendizado de máquina usando o Python, crie um bloco de anotações do Jupyter no computador local ou no Azure Machine Learning Studio. O Jupyter Notebook fornecido no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) contém o código completo para explorar, Visualizar dados, engenharia de recursos, modelagem e implantação. Neste artigo, mostraremos apenas a modelagem e a implantação.

### <a name="import-python-libraries"></a>Importar bibliotecas Python
Para executar o exemplo de Bloco de notas Jupyter ou o arquivo de script Python, os seguintes pacotes Python serão necessários. Se você está usando o serviço de Notebook do Azure Machine Learning, esses pacotes já foram pré-instalados.

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
import sklearn
from sklearn.linear_model import LogisticRegression
from sklearn.cross_validation import train_test_split
from sklearn import metrics
from __future__ import division
from sklearn import linear_model
from azureml import services
```

### <a name="read-in-the-data-from-blob"></a>Ler dados do blob
* Cadeia de Conexão

  ```text
  CONTAINERNAME = 'test1'
  STORAGEACCOUNTNAME = 'XXXXXXXXX'
  STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
  BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
  blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
  ```

* Ler como texto

  ```text
  t1 = time.time()
  data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
  t2 = time.time()
  print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  ```

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)

* Adicionar nomes de colunas e separar colunas

  ```text
  colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
  'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
  df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
  ```

* Alterar algumas colunas para formato numérico

  ```text
  cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
  ,'tipped','tip_class','rownum']
  for col in cols_2_float:
      df1[col] = df1[col].astype(float)
  ```

### <a name="build-machine-learning-models"></a>Compilar modelos de aprendizado de máquina
Aqui, você compila um modelo de classificação binária para prever se uma corrida tem gorjeta ou não. No Bloco de notas Jupyter, você encontrará outros dois modelos: classificação multiclasse e modelos de regressão.

* Primeiro, você precisa criar variáveis fictícias que possam ser usadas em modelos scikit-learn

  ```python
  df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
  df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
  ```

* Criar um quadro de dados para a modelagem

  ```python
  cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
  data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

  X = data.iloc[:,1:]
  Y = data.tipped
    ```

* Divisão 60-40 entre treinamento e teste

  ```python
  X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
  ```

* Regressão logística no conjunto de treinamento

  ```python
  model = LogisticRegression()
  logit_fit = model.fit(X_train, Y_train)
  print ('Coefficients: \n', logit_fit.coef_)
  Y_train_pred = logit_fit.predict(X_train)
  ```

    ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)

* Conjunto de dados de testes de pontuação

  ```python
  Y_test_pred = logit_fit.predict(X_test)
  ```

* Calcular métricas de Avaliação

  ```python
  fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
  print fpr_train, tpr_train, thresholds_train

  fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
  print fpr_test, tpr_test, thresholds_test

  #AUC
  print metrics.auc(fpr_train,tpr_train)
  print metrics.auc(fpr_test,tpr_test)

  #Confusion Matrix
  print metrics.confusion_matrix(Y_train,Y_train_pred)
  print metrics.confusion_matrix(Y_test,Y_test_pred)
  ```

    ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Compilar a API do Serviço Web e consumi-la no Python
Queremos colocar o modelo de aprendizado de máquina em operação após ele ter sido compilado. Aqui, usamos o modelo logístico binário como um exemplo. Verifique se a versão scikit-Learn em seu computador local é 0.15.1 (Azure Machine Learning Studio já está pelo menos nesta versão).

* Localize suas credenciais de espaço de trabalho nas configurações Azure Machine Learning Studio (clássico). Em Azure Machine Learning Studio, clique em **configurações**  -->  **nomes**  -->  **tokens de autorização**.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

  ```output
  workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  ```

* Criar um Serviço Web

  ```python
  @services.publish(workspaceid, auth_token)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(int) #0, or 1
  def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
      return logit_fit.predict(inputArray)
  ```

* Obter as credenciais do serviço Web

  ```python
  url = predictNYCTAXI.service.url
  api_key =  predictNYCTAXI.service.api_key

  print url
  print api_key

  @services.service(url, api_key)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(float)
  def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      pass
  ```

* Chame a API do serviço Web. Normalmente, espere 5-10 segundos após a etapa anterior.

  ```python
  NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  ```

    ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opção 2: criar e implantar modelos diretamente no Azure Machine Learning
Azure Machine Learning Studio (clássico) pode ler dados diretamente do Azure Data Lake Storage e, em seguida, ser usado para criar e implantar modelos. Essa abordagem usa uma tabela Hive que aponta para a Azure Data Lake Storage. Um cluster do Azure HDInsight separado precisa ser provisionado para a tabela Hive. 

### <a name="create-an-hdinsight-linux-cluster"></a>Criar um Cluster HDInsight em Linux
Abra o [portal do Azure](https://portal.azure.com) para criar um cluster HDInsight (Linux). Para obter detalhes, consulte a seção **criar um cluster hdinsight com acesso ao Azure data Lake Storage** em [criar um cluster hdinsight com data Lake Store usando portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Criar tabela de Hive no HDInsight
Agora, você cria tabelas do hive a serem usadas no Azure Machine Learning Studio (clássico) no cluster HDInsight usando os dados armazenados em Azure Data Lake Storage na etapa anterior. Vá para o cluster HDInsight que você acabou de criar. Clique em **configurações**  -->  **Propriedades**  -->  **cluster AAD identidade**  -->  **ADLS acesso**, verifique se sua conta de Azure data Lake Storage foi adicionada na lista com direitos de leitura, gravação e execução.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Em seguida, clique em **Painel** ao lado do botão **Configurações** e uma janela se abrirá. Clique em **Exibição do Hive** no canto superior direito da página e você verá o **Editor de Consultas**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Cole os scripts de Hive a seguir para criar uma tabela. O local da fonte de dados está em referência Azure Data Lake Storage desta maneira: **ADL://data_lake_store_name. azuredatalakestore. net: 443/Folder_Name/file_name**.

```hiveql
CREATE EXTERNAL TABLE nyc_stratified_sample
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string,
  payment_type string,
  fare_amount string,
  surcharge string,
  mta_tax string,
  tolls_amount string,
  total_amount string,
  tip_amount string,
  tipped string,
  tip_class string,
  rownum string
  )
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';
```

Quando a consulta for concluída, você deverá ver os resultados como este:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Compilar e implantar modelos no Azure Machine Learning Studio
Agora estamos prontos para compilar e implantar um modelo que prevê se ou não uma gorjeta é paga com o Azure Machine Learning. Os dados de amostra estratificada estão prontos para serem usados nesse problema de classificação binária (dica ou não). Os modelos preditivos usando classificação de várias classes (tip_class) e regressão (tip_amount) também pode ser compilados e implantados no Azure Machine Learning Studio, mas aqui vamos mostrar apenas como lidar com o caso usando o modelo de classificação binária.

1. Obtenha os dados em Azure Machine Learning Studio (clássico) usando o módulo **importar dados** , disponível na seção **entrada e saída de dados** . Para saber mais, veja a página de referência do módulo [Importar Dados](/azure/machine-learning/studio-module-reference/import-data) .
2. Selecione **Consulta do Hive** como a **Fonte de dados** no painel **Propriedades**.
3. Cole o seguinte script do Hive no editor **Consulta de banco de dados do Hive**

    ```hiveql
    select * from nyc_stratified_sample;
    ```

4. Insira o URI do cluster HDInsight (esse URI pode ser encontrado em portal do Azure), as credenciais do Hadoop, o local dos dados de saída e o nome da conta de armazenamento do Azure/chave/nome do contêiner.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Um exemplo de um experimento de classificação binária lendo dados da tabela de Hive é mostrado na figura abaixo:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Após a criação do experimento, clique em configurar serviço Web **de previsão de serviço Web**  -->  

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Execute automaticamente o teste de pontuação criado e, quando ele for concluído, clique em **Implantar o Serviço Web**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

O painel do serviço Web será exibido em breve:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Resumo
Ao concluir este passo a passos, você criou um ambiente de ciência de dados para criar soluções escalonáveis de ponta a ponta no Azure Data Lake. Esse ambiente foi usado para analisar um grande conjunto de dados público, realizando as etapas canônicas do Processo de Ciência de Dados da aquisição de dados ao treinamento de modelo e prosseguindo para a implantação do modelo como um serviço Web. O U-SQL foi usado para processar, explorar e obter amostras de dados. O Python e o hive foram usados com Azure Machine Learning Studio (clássico) para criar e implantar modelos de previsão.

## <a name="whats-next"></a>O que vem a seguir?
O roteiro de aprendizagem do [TDSP (Processo de Ciência de Dados de Equipe)](./index.yml) fornece links para tópicos que descrevem cada etapa do processo de análise avançada. Há diversas instruções detalhadas listadas na página [Passo a passo do Processo de Ciência de Dados de Equipe](walkthroughs.md) que demonstram como usar recursos e serviços nos diversos cenários de análise preditiva:

* [O processo de ciência de dados de equipe em ação: usando o Azure Synapse Analytics](sqldw-walkthrough.md)
* [O processo de ciência de dados de equipe em ação: usando clusters Hadoop do HDInsight](hive-walkthrough.md)
* [O Processo de Ciência de Dados de Equipe: usando o SQL Server](sql-walkthrough.md)
* [Visão geral do Processo de Ciência de Dados usando Spark no Azure HDInsight](spark-overview.md)