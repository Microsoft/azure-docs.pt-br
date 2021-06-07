---
title: Explorar dados em um cluster do Hadoop – Processo de Ciência de Dados da Equipe
description: Usando o Processo de Ciência de Dados de Equipe para um cenário completo, que emprega um cluster HDInsight Hadoop para criar e implantar um modelo.
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
ms.openlocfilehash: 53f50e98bcec4b8ace342808f0bcfd96770834b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002214"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>O Processo de Ciência de Dados de Equipe em ação: usar clusters Hadoop do Azure HDInsight
Neste passo a passo, usamos o [TDSP (Processo de ciência de dados da equipe)](overview.md) em um cenário de ponta a ponta. Usamos um [cluster Hadoop do Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e apresentar dados de engenharia do conjunto de dados publicamente disponível [Corridas de táxi em NYC](https://www.andresmh.com/nyctaxitrips/) e reduzir os dados da amostra. Para manipular a classificação binária e multiclasse e tarefas preditivas de regressão, criamos modelos dos dados com o Azure Machine Learning. 

Para obter uma explicação que mostra como lidar com um conjunto de dados maior, consulte [processo de ciência de dado de equipe – usando clusters de Azure HDInsight Hadoop em um conjunto](hive-criteo-walkthrough.md)de dados de 1 TB.

Você também pode usar um bloco de anotações IPython para realizar as tarefas apresentadas no passo a passos que usa o conjunto de um DataSet de 1 TB. Para obter mais informações, consulte [Passo a passo da Criteo usando uma conexão ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Descrição do conjunto de dados Corridas de Táxi em NYC
Os dados de Corridas de Táxi em NYC têm aproximadamente de 20 GB de arquivos CSV (valores separados por vírgula) compactados (cerca de 48 GB descompactados). Contêm mais de 173 milhões de corridas individuais e inclui as tarifas pagas para cada corrida. Cada registro de corrida inclui o local e o horário de saída e chegada, número da carteira (de motorista) anônima do taxista e número do medalhão (a ID exclusiva do táxi). Os dados abrangem todas as corridas no ano de 2013 e são fornecidos nos dois seguintes conjuntos de dados para cada mês:

- Os arquivos CSV do trip_data contêm detalhes da viagem: o número de passageiros, pontos de chegada, duração da corrida e duração da viagem. Aqui estão alguns exemplos de registros:

  `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

- Os arquivos CSV trip_fare contêm detalhes da tarifa paga para cada corrida: tipo de pagamento, valor da tarifa, sobretaxa e impostos, dicas e tarifas e o valor total pago. Aqui estão alguns exemplos de registros:

  `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

A chave exclusiva para unir trip\_data e trip\_fare é composta pelos campos: medallion, hack\_license e pickup\_datetime. Para obter todos os detalhes relevantes a uma corrida específica, é suficiente unir essas três chaves.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exemplos de tarefas de previsão
Determine o tipo de previsões que você deseja fazer com base na análise de dados para ajudar a esclarecer as tarefas de processo necessárias. Aqui estão três exemplos de problemas de previsão que abordamos neste passo a passos, tudo com base *no \_ valor da gorjeta*:

- **Classificação binária**: preveja se uma gorjeta foi ou não paga em uma corrida. Ou seja, um *valor\_de gorjeta* maior do que $0 é um exemplo positivo, enquanto um *valor\_ de gorjeta* de $0 é um exemplo de negativo.

  - Classe 0: tip_amount = $0
  - Classe 1: tip_amount > $0

- **Classificação multiclasse**: preveja o intervalo do valor da gorjeta pago pela corrida. Dividimos *tip\_amount* em cinco classes:

  - Classe 0: tip_amount = $0
  - Classe 1: tip_amount > US$ 0 e tip_amount <= US$ 5
  - Classe 2: tip_amount > US$ 5 e tip_amount <= US$ 10
  - Classe 3: tip_amount > US$ 10 e tip_amount <= US$ 20
  - Classe 4: tip_amount > $20

- **Tarefa de regressão**: preveja o valor da gorjeta pago por uma corrida.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Configurar um cluster Hadoop do HDInsight para análises avançadas
> [!NOTE]
> Normalmente, essa é uma tarefa de administrador.
> 
> 

Você pode configurar um ambiente do Azure para análises avançadas que empregue um cluster HDInsight em três etapas:

1. [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md): essa conta de armazenamento é usada para armazenar dados no armazenamento de BLOBs do Azure. Os dados usados em clusters HDInsight também estão localizados aqui.
2. [Personalizar os clusters do Hadoop do Azure HDInsight para Processo e Tecnologia de Análise Avançada](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Esta etapa cria um cluster HDInsight Hadoop com o Anaconda Python 2.7 de 64 bits instalado em todos os nós. Há duas etapas importantes a serem lembradas durante a personalização de seu cluster HDInsight.
   
   * Lembre-se de vincular a conta de armazenamento criada na etapa 1 ao cluster HDInsight ao criá-lo. Essa conta de armazenamento acessa dados que são processados no cluster.
   * Depois de criar o cluster, habilite o Acesso Remoto no nó de cabeçalho do cluster. Navegue para a guia **Configuração** e selecione **Habilitar Remoto**. Esta etapa especifica as credenciais de usuário usadas para logon remoto.
3. [Criar um workspace do Azure Machine Learning](../classic/create-workspace.md): use esse workspace para criar modelos de aprendizado de máquina. Essa tarefa é abordada depois de concluir uma exploração inicial e redução de dados usando o cluster HDInsight.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Obter os dados de uma fonte de pública
> [!NOTE]
> Normalmente, essa é uma tarefa de administrador.
> 
> 

Para copiar o conjunto de dados [Corridas de Táxi em NYC](https://www.andresmh.com/nyctaxitrips/) para seu computador de seu local público, use um dos métodos descritos em [Mover dados bidirecionalmente no armazenamento de Blobs do Azure](move-azure-blob.md).

Aqui, descrevemos como usar o AzCopy para transferir os arquivos que contêm dados. Para baixar e instalar o AzCopy, siga as instruções em [introdução ao utilitário de linha de comando AzCopy](../../storage/common/storage-use-azcopy-v10.md).

1. Em uma janela de prompt de comando, execute os seguintes comandos AzCopy, substituindo *\<path_to_data_folder>* pelo destino desejado:

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

1. Quando a cópia for concluída, você verá um total de 24 arquivos compactados na pasta de dados escolhida. Descompacte os arquivos baixados para o mesmo diretório em seu computador local. Tome nota da pasta em que os arquivos descompactados estão. Essa pasta é referida como a *\<path\_to\_unzipped_data\_files\>* seguir.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Carregar os dados no contêiner padrão do cluster HDInsight Hadoop
> [!NOTE]
> Normalmente, essa é uma tarefa de administrador.
> 
> 

Nos comandos de AzCopy a seguir, substitua os seguintes parâmetros pelos valores reais especificados ao criar o cluster de Hadoop e extrair os arquivos de dados.

* ***\<path_to_data_folder>*** O diretório (junto com o caminho) em seu computador que contém os arquivos de dados descompactados.  
* ***\<storage account name of Hadoop cluster>*** A conta de armazenamento associada ao cluster HDInsight.
* ***\<default container of Hadoop cluster>*** O contêiner padrão usado pelo seu cluster. O nome do contêiner padrão geralmente é o mesmo nome que o próprio cluster. Por exemplo, se o cluster se chamar "abc123.azurehdinsight.net", o contêiner padrão será abc123.
* ***\<storage account key>*** A chave para a conta de armazenamento usada pelo cluster.

Em um prompt de comando ou uma janela do Windows PowerShell, execute os dois comandos do AzCopy a seguir.

Esse comando carrega os dados de corrida no diretório ***nyctaxitripraw*** no contêiner padrão do cluster Hadoop.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
```

Esse comando carrega os dados de tarifa no diretório ***nyctaxifareraw*** no contêiner padrão do cluster Hadoop.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv
```

Os dados agora devem estar no armazenamento de Blobs e prontos para serem consumidos dentro do cluster HDInsight.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Conectar-se ao nó do cabeçalho do cluster Hadoop e preparar a análise de exploração de dados
> [!NOTE]
> Normalmente, essa é uma tarefa de administrador.
> 
> 

Para acessar o nó de cabeçalho do cluster para a análise exploratória e redução dos dados, siga o procedimento descrito em [Acessar o nó de cabeçalho do cluster Hadoop](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

Neste passo a passo, podemos usar principalmente consultas escritas em [Hive](https://hive.apache.org/), uma linguagem de consulta do tipo SQL, para realizar explorações de dados preliminares. As consultas do hive são armazenadas em arquivos '. HQL '. Podemos então reduzir esses dados a serem usados no Machine Learning para criar modelos.

Para preparar o cluster para análise de dados exploratório, baixe os arquivos '. HQL ' que contêm os scripts de Hive relevantes do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) para um diretório local (C:\temp) no nó de cabeçalho. Abra o prompt de comando de dentro do nó principal do cluster e execute os dois comandos a seguir:

```console
set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
```

Esses dois comandos baixam todos os arquivos '. HQL ' necessários neste passo a passos para o diretório local ***C:\temp&#92;*** no nó de cabeçalho.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Criar banco de dados e tabelas Hive particionadas por mês
> [!NOTE]
> Essa tarefa é normalmente para um administrador.
> 
> 

Agora você está pronto para criar tabelas do Hive para o conjunto de dados de táxi em NYC.
No nó de cabeçalho do cluster Hadoop, abra a linha de comando do Hadoop na área de trabalho do nó de cabeçalho. Insira o diretório do Hive executando o seguinte comando:

```console
cd %hive_home%\bin
```

> [!NOTE]
> Execute todos os comandos do Hive neste passo a passo no prompt do diretório bin/do Hive. Isso resolve os problemas de caminho automaticamente. Usamos os termos “prompt do diretório do Hive”, “prompt do diretório bin/ do Hive” e “linha de comando do Hadoop” de forma intercambiável neste passo a passo.
> 
> 

No prompt do diretório do hive, execute o seguinte comando na linha de comando do Hadoop do nó principal que cria o banco de dados e as tabelas do hive:

```console
hive -f "C:\temp\sample_hive_create_db_and_tables.hql"
```

Aqui está o conteúdo do arquivo **arquivo c:\Temp\sample \_ Hive \_ Create \_ DB \_ e \_ Tables. HQL** que cria o banco de dados Hive **nyctaxidb** e as tabelas **Trip** e **Tarifa**.

```hiveql
create database if not exists nyctaxidb;

create external table if not exists nyctaxidb.trip
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count int,
    trip_time_in_secs double,
    trip_distance double,
    pickup_longitude double,
    pickup_latitude double,
    dropoff_longitude double,
    dropoff_latitude double)  
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

create external table if not exists nyctaxidb.fare
(
    medallion string,
    hack_license string,
    vendor_id string,
    pickup_datetime string,
    payment_type string,
    fare_amount double,
    surcharge double,
    mta_tax double,
    tip_amount double,
    tolls_amount double,
    total_amount double)
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
```

Esse script do Hive cria duas tabelas:

* A tabela de **viagens** contém detalhes de cada passeio (detalhes do driver, tempo de retirada, distância da corrida e horas).
* A tabela de **tarifas** contém detalhes da Tarifa (valor da tarifa, valor da gorjeta, tarifas e sobretaxas).

Se precisar de assistência adicional com esses procedimentos ou desejar investigar procedimentos alternativos, consulte a seção [Enviar consultas do Hive diretamente da linha de comando do Hadoop](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Carregar dados em tabelas do Hive por partições
> [!NOTE]
> Essa tarefa é normalmente para um administrador.
> 
> 

O conjunto de dados de táxi de NYC tem um particionamento natural por mês, que usamos para habilitar os tempos de processamento e consulta. Os comandos do PowerShell a seguir (emitidos no diretório do Hive usando a linha de comando do Hadoop) carregam dados para as tabelas de corridas e tarifas do Hive, particionadas por mês.

```powershell
for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")
```

O arquivo do **hive de exemplo \_ \_ carregar \_ dados \_ por \_ partições. HQL** contém os seguintes comandos de **carregamento** :

```hiveql
LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});
```

Um número de consultas de Hive usadas aqui no processo de exploração envolve a análise de apenas uma ou duas partições. Porém, essas consultas podem ser executadas em todo o conjunto de dados.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Mostra os bancos de dados no cluster do Hadoop do HDInsight
Para mostrar os bancos de dados criados no cluster HDInsight Hadoop dentro da janela de linha de comando do Hadoop, execute o seguinte comando na linha de comando do Hadoop:

```console
hive -e "show databases;"
```

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Mostrar as tabelas do Hive no banco de dados **nyctaxidb**
Para mostrar as tabelas no banco de dados **nyctaxidb**, execute o seguinte comando na linha de comando do Hadoop:

```console
hive -e "show tables in nyctaxidb;"
```

Podemos confirmar se as tabelas foram particionadas executando o seguinte comando:

```console
hive -e "show partitions nyctaxidb.trip;"
```

Esta é a saída esperada:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 2.075 seconds, Fetched: 12 row(s)
```

Da mesma forma, podemos garantir que a tabela de tarifas foi particionada executando o seguinte comando:

```console
hive -e "show partitions nyctaxidb.fare;"
```

Esta é a saída esperada:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 1.887 seconds, Fetched: 12 row(s)
```

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Exploração de dados e engenharia de recursos no Hive
> [!NOTE]
> Normalmente, essa é uma tarefa de cientista de dados.
> 
> 

Use consultas do Hive para realizar tarefas de exploração de dados e engenharia de recursos para os dados carregados nas tabelas do Hive. Estes são exemplos de tarefas desse tipo:

* Exibir os 10 primeiros registros em ambas as tabelas.
* Explorar as distribuições de dados de alguns campos em períodos diferentes.
* Investigar a qualidade dos dados dos campos de longitude e latitude.
* Gere rótulos de classificação binária e multiclasse com base no valor da gorjeta.
* Gerar recursos computando as distâncias diretas de viagem.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Exploração: exibir os 10 principais registros na tabela de corridas
> [!NOTE]
> Normalmente, essa é uma tarefa de cientista de dados.
> 
> 

Para ver a aparência dos dados, examine 10 registros de cada tabela. Para inspecionar os registros, execute as duas consultas a seguir separadamente no prompt do diretório do Hive no console da linha de comando do Hadoop.

Para obter os 10 primeiros registros na tabela de corridas do primeiro mês:

```console
hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
```

Para obter os 10 primeiros registros na tabela de tarifas do primeiro mês:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;"
```

Você pode salvar os registros em um arquivo para exibição conveniente com uma pequena alteração na consulta anterior:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput
```

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Exploração: exibir o número de registros em cada uma das 12 partições
> [!NOTE]
> Normalmente, essa é uma tarefa de cientista de dados.
> 
> 

É interessante como o número de corridas varia durante o ano calendário. O agrupamento por mês mostra a distribuição de corridas.

```console
hive -e "select month, count(*) from nyctaxidb.trip group by month;"
```

Esse comando gera a seguinte saída:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 283.406 seconds, Fetched: 12 row(s)
```

Aqui, a primeira coluna é o mês e a segunda é o número de corridas desse mês.

Também podemos contar o número total de registros em nosso conjunto de dados de corridas executando o seguinte comando no prompt do diretório do Hive:

```console
hive -e "select count(*) from nyctaxidb.trip;"
```

Esse comando produz:

```output
173179759
Time taken: 284.017 seconds, Fetched: 1 row(s)
```

Usando comandos semelhantes aos mostrados para o conjunto de dados de corridas, podemos emitir consultas do Hive no prompt do diretório do Hive para o conjunto de dados de tarifas para validar o número de registros.

```console
hive -e "select month, count(*) from nyctaxidb.fare group by month;"
```

Esse comando produz esta saída:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 253.955 seconds, Fetched: 12 row(s)
```

O mesmo número exato de viagens por mês é retornado para ambos os conjuntos de dados, fornecendo a primeira validação de que os dados foram carregados corretamente.

Conte o número total de registros no conjunto de dados de tarifas usando o seguinte comando no prompt do diretório do Hive:

```console
hive -e "select count(*) from nyctaxidb.fare;"
```

Esse comando produz:

```output
173179759
Time taken: 186.683 seconds, Fetched: 1 row(s)
```

O número total de registros em ambas as tabelas também é o mesmo, fornecendo uma segunda validação de que os dados foram carregados corretamente.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: distribuição de corridas por licença
> [!NOTE]
> Essa análise é normalmente uma tarefa de cientista de dados.
> 
> 

Esse exemplo identifica os medalhões (números de táxi) com mais de 100 corridas em determinado período. A consulta aproveita o acesso à tabela particionada, pois ela está condicionada pela variável de partição **month**. Os resultados da consulta são gravados em um arquivo local, **queryoutput. tsv**, no `C:\temp` nó de cabeçalho.

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

Este é o conteúdo do arquivo **sample\_hive\_trip\_count\_by\_medallion.hql** para inspeção.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

O medalhão no conjunto de dados de táxi em NYC identifica um táxi exclusivo. Você pode identificar quais táxis estão comparativamente ocupados perguntando quais fizeram mais de determinado número de corridas em determinado período. O exemplo a seguir identifica os táxis que fizeram mais de cem corridas nos primeiros três meses e salva os resultados da consulta em um arquivo local, **C:\temp\queryoutput.tsv**.

Este é o conteúdo do arquivo **sample\_hive\_trip\_count\_by\_medallion.hql** para inspeção.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

No prompt do diretório do Hive, execute o seguinte comando:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploração: distribuição de corridas por medalhão e carteira de habilitação
> [!NOTE]
> Essa tarefa é normalmente para um cientista de dados.
> 
> 

Ao explorar um conjunto de um DataSet, frequentemente queremos examinar as distribuições de grupos de valores. Esta seção fornece um exemplo de como fazer essa análise para cabs e drivers.

O arquivo **sample\_hive\_trip\_count\_by\_medallion\_license.hql** agrupa o conjunto de dados de tarifas em **medallion** e **hack_license** e retorna a contagem de cada combinação. Estes são os conteúdos:

```hiveql
SELECT medallion, hack_license, COUNT(*) as trip_count
FROM nyctaxidb.fare
WHERE month=1
GROUP BY medallion, hack_license
HAVING trip_count > 100
ORDER BY trip_count desc;
```

Essa consulta retorna combinações de táxi e motorista, ordenadas pelo número decrescente de corridas.

No prompt do diretório do Hive, execute:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv
```

Os resultados da consulta são gravados em um arquivo local, **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Exploração: avaliar a qualidade dos dados com a verificação de registros de latitude ou longitude inválidos
> [!NOTE]
> Normalmente, essa é uma tarefa de cientista de dados.
> 
> 

Um objetivo comum da análise exploratória de dados é eliminar registros inválidos ou incorretos. O exemplo nesta seção determina se os campos de latitude ou longitude contêm um valor muito fora da área de NYC. Uma vez que é provável que esses registros tenham valores incorretos de latitude de longitude, queremos eliminá-los de todos os dados a serem usados para modelagem.

Aqui está o conteúdo do arquivo **sample\_hive\_quality\_assessment.hql** para inspeção.

```hiveql
    SELECT COUNT(*) FROM nyctaxidb.trip
    WHERE month=1
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);
```

No prompt do diretório do Hive, execute:

```console
hive -S -f "C:\temp\sample_hive_quality_assessment.hql"
```

O argumento *-S* incluído neste comando suprime a impressão de tela de status dos trabalhos de Mapear/Reduzir de Hive. Esse comando é útil porque torna a tela impressa da saída da consulta do hive mais legível.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploração: distribuições de classe binária de gorjetas para corridas
> [!NOTE]
> Normalmente, essa é uma tarefa de cientista de dados.
> 
> 

Para o problema de classificação binária descrito na seção [Exemplos de tarefas de previsão](hive-walkthrough.md#mltasks) , é útil saber se uma dica foi fornecida ou não. Essa distribuição de gorjetas é binária:

* gorjeta oferecida (Classe 1, tip\_amount > $0)  
* nenhuma gorjeta (Classe 0, tip\_amount = $0)

O arquivo **Sample \_ \_ \_ . HQL de exemplo** a seguir mostra o comando a ser executado:

```hiveql
SELECT tipped, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tipped;
```

No prompt do diretório do Hive, execute:

```console
hive -f "C:\temp\sample_hive_tipped_frequencies.hql"
```


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Exploração: distribuições de classe na configuração multiclasse
> [!NOTE]
> Normalmente, essa é uma tarefa de cientista de dados.
> 
> 

Para o problema de classificação multiclasse descrito na seção [Exemplos de tarefas de previsão](hive-walkthrough.md#mltasks), esse conjunto de dados também é adequado a uma classificação natural para prever o valor das gorjetas oferecidas. Podemos usar compartimentos para definir intervalos de gorjeta na consulta. Para obter as distribuições de classe para os vários intervalos de gorjetas, use o arquivo **sample\_hive\_tip\_range\_frequencies.hql**. Estes são os conteúdos.

```hiveql
SELECT tip_class, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount=0, 0,
        if(tip_amount>0 and tip_amount<=5, 1,
        if(tip_amount>5 and tip_amount<=10, 2,
        if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tip_class;
```

Execute o seguinte comando no console de linha de comando do Hadoop:

```console
hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"
```

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Exploração: calcular a distância direta entre dois locais de latitude-longitude
> [!NOTE]
> Normalmente, essa é uma tarefa de cientista de dados.
> 
> 

Talvez você queira saber se há uma diferença entre a distância direta entre dois locais e a distância real da corrida do táxi. Pode haver uma menor probabilidade de um passageiro dar gorjeta se ele descobrir que o motorista intencionalmente o levou por uma rota mais longa.

Para ver a comparação entre a distância real da corrida e a [distância do Haversine](https://en.wikipedia.org/wiki/Haversine_formula) entre dois pontos de latitude-longitude (a distância do “círculo grande”), use as funções trigonométricas disponíveis no Hive:

```hiveql
set R=3959;
set pi=radians(180);

insert overwrite directory 'wasb:///queryoutputdir'

select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
 *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
 *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
 /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
 +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
 pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxidb.trip
where month=1
and pickup_longitude between -90 and -30
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and -30
and dropoff_latitude between 30 and 90;
```

Na consulta anterior, R é o raio da Terra em milhas e pi é convertido para radianos. Os pontos de latitude de longitude são filtrados para remover os valores que estão longe da área NYC.

Nesse caso, gravamos os resultados em um diretório chamado **queryoutputdir**. A sequência dos comandos a seguir primeiro cria esse diretório de saída e, em seguida, executa o comando do Hive.

No prompt do diretório do Hive, execute:

```hiveql
hdfs dfs -mkdir wasb:///queryoutputdir

hive -f "C:\temp\sample_hive_trip_direct_distance.hql"
```

Os resultados da consulta são gravados em nove blobs do Azure (**queryoutputdir/000000\_0** a **queryoutputdir/000008\_0**) no contêiner padrão do cluster Hadoop.

Para ver o tamanho dos blobs individuais, execute o seguinte comando no prompt do diretório do Hive:

```hiveql
hdfs dfs -ls wasb:///queryoutputdir
```

Para ver o conteúdo de determinado arquivo, digamos, **000000\_0**, use o comando `copyToLocal` do Hadoop.

```hiveql
hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile
```

> [!WARNING]
> O `copyToLocal` pode ser muito lento para arquivos grandes e não é recomendado para uso com eles.  
> 
> 

Uma das principais vantagens de os dados residirem em um blob do Azure é que podemos explorar os dados no Machine Learning usando o módulo [Importar Dados][import-data].

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Reduzir dados e criar modelos no Machine Learning
> [!NOTE]
> Normalmente, essa é uma tarefa de cientista de dados.
> 
> 

Após a fase de análise exploratória de dados, agora estamos prontos para reduzir os dados para a criação de modelos no Machine Learning. Nesta seção, mostramos como usar uma consulta do Hive para reduzir os dados de amostra. Em seguida, o Machine Learning acessa-os no módulo [Importar Dados][import-data].

### <a name="down-sampling-the-data"></a>Reduzindo os dados
Há duas etapas neste procedimento. Primeiro, juntamos as tabelas **nyctaxidb. Trip** e **nyctaxidb. Tarifa** em três chaves que estão presentes em todos os registros: **Medallion**, **\_ licença de hack** e **\_ DateTime de retirada**. Em seguida, geramos um rótulo de classificação binária **tipped** e um rótulo de classificação multiclasse **tip\_class**.

Para usar os dados reduzidos diretamente no módulo [Importar Dados][import-data] do Machine Learning, é necessário armazenar os resultados da consulta anterior em uma tabela interna do Hive. No que vem em seguida, criamos uma tabela interna do Hive e preenchemos seus conteúdos com os dados unidos e reduzidos.

A consulta aplica funções de Hive padrão diretamente para gerar os seguintes parâmetros de tempo do campo de **retirada \_ DateTime** :
- hora do dia
- semana do ano
- dia da semana (' 1 ' significa segunda-feira e ' 7 ' significa domingo)

A consulta também gera a distância direta entre os locais de saída e chegada. Para obter uma lista completa de funções desse tipo, consulte [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Em seguida, a consulta reduz os dados, para que os resultados da consulta possam ser inseridos no Azure Machine Learning Studio. Apenas cerca de 1% do conjunto de dados original é importado para o Studio.

Este é o conteúdo do arquivo **sample\_hive\_prepare\_for\_aml\_full.hql** que prepara os dados para a criação de modelo no Machine Learning:

```hiveql
set R = 3959;
set pi=radians(180);

create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\n'
stored as textfile;

--- now insert contents of the join into the above internal table

insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
rand() as sample_key

from nyctaxidb.trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from nyctaxidb.fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
```

Para executar essa consulta no prompt do diretório do Hive:

```console
hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"
```

Agora temos uma tabela interna, **nyctaxidb.nyctaxi_downsampled_dataset**, que pode ser acessada usando o módulo [Importar Dados][import-data] do Machine Learning. Além disso, podemos usar esse conjunto de dados para criar modelos do Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Use o módulo Importar Dados no Machine Learning para acessar os dados reduzidos
Para emitir consultas do Hive no módulo [Importar Dados][import-data] do Machine Learning, você precisa ter acesso a um workspace do Machine Learning. Também precisa ter acesso às credenciais do cluster e à sua conta de armazenamento associada.

Estes são alguns detalhes sobre o módulo [Importar Dados][import-data] e os parâmetros a serem inseridos:

**URI do servidor HCatalog**: se o nome do cluster for **abc123**, use: https: \/ /abc123.azurehdinsight.net.

**Nome da conta de usuário do Hadoop**: o nome de usuário escolhido para o cluster (não o nome de usuário de acesso remoto).

**Senha da conta de usuário do Hadoop**: a senha escolhida para o cluster (não a senha de acesso remoto).

**Local dos dados de saída**: escolhido para ser o Azure.

**Nome da conta de armazenamento do Azure**: nome da conta de armazenamento padrão associada ao cluster.

**Nome do contêiner do Azure**: o nome do contêiner padrão para o cluster e geralmente é o mesmo que o nome do cluster. Para um cluster chamado **abc123**, o nome é abc123.

> [!IMPORTANT]
> Qualquer tabela que desejemos consultar usando o módulo [Importar Dados][import-data] no Machine Learning precisa ser uma tabela interna.
> 
> 

É assim como se determina se uma tabela **T** em um banco de dados **D.db** é uma tabela interna. No prompt do diretório do Hive, execute o seguinte comando:

```hiveql
hdfs dfs -ls wasb:///D.db/T
```

Se a tabela for uma tabela interna e preenchida, seu conteúdo deverá aparecer aqui.

Outra maneira de determinar se uma tabela é uma tabela interna é usar o Gerenciador de Armazenamento do Azure. Use-o para navegar até o nome do contêiner padrão do cluster e, em seguida, filtre pelo nome da tabela. Se a tabela e seu conteúdo aparecerem, isso confirma que ela é uma tabela interna.

Esta é uma captura de tela da consulta do Hive e do módulo [Importar Dados][import-data]:

![Captura de tela da consulta do Hive para o módulo Importar Dados](./media/hive-walkthrough/1eTYf52.png)

Como nossos dados reduzidos residem no contêiner padrão, a consulta de Hive resultante de Machine Learning é simples. É apenas um **SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data**.

O conjunto de dados agora pode ser usado como o ponto de partida para criar modelos do Machine Learning.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Criar modelos no Machine Learning
Agora você pode continuar a criação e a implantação do modelo no [Machine Learning](https://studio.azureml.net). Os dados estão prontos para serem usados para resolvermos os problemas de previsão identificados anteriormente:

- **Classificação binária**: para prever se uma gorjeta foi paga ou não por uma viagem.

  **Aprendiz usado:** regressão logística de classe dois

  a. Para esse problema, o rótulo (ou a classe) de destino é **tipped**. O conjunto de dados original convertidos tem algumas colunas que são vazamentos de destino para esse teste de classificação. Em particular, **a \_ classe Tip**, o **\_ valor Tip** e o **\_ valor total** revelam informações sobre o rótulo de destino que não está disponível no momento do teste. Deixamos de considerar essas colunas usando o módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

  O seguinte diagrama mostra nosso experimento para prever se uma gorjeta foi paga ou não para determinada corrida:

  ![Diagrama do experimento para prever se a gorjeta foi paga](./media/hive-walkthrough/QGxRz5A.png)

  b. Para esse teste, nossas distribuições de rótulo de destino foram de aproximadamente 1:1.

   O seguinte gráfico mostra a distribuição dos rótulos de classe de gorjeta para o problema de classificação binária:

  ![Gráfico de distribuição de rótulos de classe de gorjeta](./media/hive-walkthrough/9mM4jlD.png)

    Como resultado, obtemos uma AUC (área sob a curva) de 0,987, conforme mostrado na seguinte figura:

  ![Gráfico de valor AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Classificação multiclasse**: para prever o intervalo de valores de gorjetas pagos pela corrida, usando as classes definidas anteriormente.

  **Aprendiz usado:** regressão logística de várias classes

  a. Para esse problema, nosso rótulo de destino (ou classe) é **Tip \_ Class**, que pode usar um dos cinco valores (0, 1, 2, 3, 4). Como no caso de classificação binária, temos algumas colunas que são vazamentos de destino para esse experimento. Em particular, **tipped**, **tip\_amount** e **total\_amount** revelam informações sobre o rótulo de destino que não estão disponíveis no momento do teste. Removemos essas colunas usando o módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

  O diagrama a seguir mostra o experimento para prever em qual compartimento uma gorjeta provavelmente se enquadrará. Os compartimentos são: Classe 0: gorjeta = US$ 0, Classe 1: gorjeta > US$ 0 e gorjeta <= US$ 5, Classe 2: gorjeta > US$ 5 e gorjeta <= US$ 10, Classe 3: gorjeta > US$ 10 e gorjeta <= US$ 20 e Classe 4: gorjeta > US$ 20.

  ![Diagrama do experimento para prever o compartimento da gorjeta](./media/hive-walkthrough/5ztv0n0.png)

  Agora mostraremos a aparência da distribuição de classe de teste real. Classe 0 e Classe 1 são predominantes e as outras classes são raras.

  ![Gráfico de distribuição de classe de teste](./media/hive-walkthrough/Vy1FUKa.png)

  b. Para este experimento, usamos uma matriz de confusão para examinar a imprecisões de previsão, como mostrado aqui:

  ![Matriz de confusão](./media/hive-walkthrough/cxFmErM.png)

  Embora a escolha da classe nas classes predominantes seja boa, o modelo não faz um bom trabalho de "aprender" sobre as classes mais raras.

- **Tarefa de regressão**: prever a quantidade de gorjeta paga por uma corrida.

  **Aprendiz usado:** árvore de decisão aprimorada

  a. Para esse problema, o rótulo (ou a classe) de destino é **tip\_amount**. As perdas de destino nesse caso são: **tipped**, **tip\_class** e **total\_amount**. Todas essas variáveis revelam informações sobre o valor da gorjeta que normalmente não estão disponíveis no momento do teste. Removemos essas colunas usando o módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

  O seguinte diagrama mostra o experimento para prever o valor da gorjeta oferecida:

  ![Diagrama do experimento para prever o valor da gorjeta](./media/hive-walkthrough/11TZWgV.png)

  b. Para problemas de regressão, medimos as precisões da previsão examinando o erro ao quadrado nas previsões e o coeficiente de determinação:

  ![Captura de tela das estatísticas de previsão](./media/hive-walkthrough/Jat9mrz.png)

  Aqui, o coeficiente de determinação é 0,709, implicando que aproximadamente 71% da variação é explicada pelos coeficientes de modelo.

> [!IMPORTANT]
> Para saber mais sobre o Machine Learning e como acessá-lo e usá-lo, consulte [O que é o Machine Learning](../classic/index.yml). Além disso, a [galeria de IA do Azure](https://gallery.cortanaintelligence.com/) abrange uma gama de experimentos e fornece uma introdução abrangente às várias funcionalidades do Machine Learning.
> 
> 

## <a name="license-information"></a>Informações de licença
Este passo a passo do exemplo e os scripts que o acompanham são compartilhados pela Microsoft sob a licença MIT. Para obter mais informações, consulte o arquivo **LICENSE.txt** no diretório do código de exemplo no github.

## <a name="references"></a>Referências
•   [Página de download de Viagens de Táxi de NYC, de Andrés Monroy](https://www.andresmh.com/nyctaxitrips/)  
•    [Frustrando os dados de corrida de táxi de NYC por Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [Pesquisa e estatísticas de NYC táxi e limusines Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data