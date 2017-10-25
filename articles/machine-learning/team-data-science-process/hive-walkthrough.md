---
title: Explorar dados em um cluster Hadoop e criar modelos no Azure Machine Learning | Microsoft Docs
description: "Usando o Processo de Ciência de Dados de Equipe para um cenário completo que emprega um cluster Hadoop do HDInsight para criar e implantar um modelo usando um conjunto de dados publicamente disponível."
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e9e76c91-d0f6-483d-bae7-2d3157b86aa0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 238b7d6bb6289b5f2e8d2a20f4335724087dfd48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>O Processo de Ciência de Dados de Equipe em ação: usar clusters Hadoop do Azure HDInsight
Neste passo a passo, usamos o [TDSP (Processo de Ciência de Dados de Equipe)](overview.md) em um cenário de ponta a ponta usando um [cluster Hadoop do Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e apresentar dados de engenharia do conjunto de dados publicamente disponível [Corridas de táxi em NYC](http://www.andresmh.com/nyctaxitrips/) e reduzir os dados da amostra. Modelos de dados são criados com o Azure Machine Learning para lidar com classificação binária e multiclasse e tarefas preditivas de regressão.

Para ver um passo a passo que mostra como manipular um conjunto de dados maior (1 terabyte) em um cenário semelhante usando clusters Hadoop do HDInsight para processamento de dados, confira [Processo de Ciência de Dados de Equipe — usando clusters Hadoop do Azure HDInsight em um conjunto de dados de 1 TB](hive-criteo-walkthrough.md).

Também é possível usar um bloco de anotações iPython para executar as tarefas apresentadas passo a passo usando o conjunto de dados de 1 TB. Usuários que gostariam de testar essa abordagem devem consultar o tópico [Passo a passo da Criteo usando uma conexão ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .

## <a name="dataset"></a>Descrição do conjunto de dados Corridas de Táxi em NYC
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

A chave exclusiva para unir trip\_data e trip\_fare é composta pelos campos: medallion, hack\_license e pickup\_datetime.

Para obter todos os detalhes relevantes para uma corrida em particular, é suficiente ingressar com três chaves: "medallion", "hack\_license" e "pickup\_datetime".

Descreveremos mais detalhes dos dados quando os armazenarmos em tabelas Hive em breve.

## <a name="mltasks"></a>Exemplos de tarefas de previsão
Ao abordar a dados, determinar o tipo de previsões que deseja fazer com base na sua análise ajuda a esclarecer as tarefas que você precisará incluir em seu processo.
Aqui estão três exemplos de problemas de previsão que abordados neste passo a passo cuja formulação se baseia no *tip\_amount*:

1. **Classificação binária**: prever ou não se uma gorjeta foi paga por uma corrida, ou seja, um *tip\_amount* maior que US$ 0 é um exemplo positivo, enquanto um *tip\_amount* de US$ 0 é um exemplo negativo.
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0
2. **Classificação multiclasse**: prever o intervalo da gorjetas pagas pela corrida. Dividimos *tip\_amount* em cinco compartimentos ou classes:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Tarefa de regressão**: prever o valor da gorjeta paga por uma corrida.  

## <a name="setup"></a>Configurar um cluster Hadoop do HDInsight para análises avançadas
> [!NOTE]
> Essa normalmente é uma tarefa **Admin** .
> 
> 

Você pode configurar um ambiente do Azure para análises avançadas que empregue um cluster HDInsight em três etapas:

1. [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md): essa conta de armazenamento é usada para armazenar dados no Armazenamento de Blob do Azure. Os dados usados em clusters HDInsight também estão localizados aqui.
2. [Personalizar os clusters do Hadoop do Azure HDInsight para Processo e Tecnologia de Análise Avançada](customize-hadoop-cluster.md). Esta etapa cria um cluster do Hadoop do Azure HDInsight com Anaconda Python 2.7 de 64 bits instalado em todos os nós. Há duas etapas importantes a serem lembradas durante a personalização de seu cluster HDInsight.
   
   * Lembre-se de vincular a conta de armazenamento criada na etapa 1 ao cluster do HDInsight ao criá-lo. Essa conta de armazenamento é usada para acessar dados que são processados no cluster.
   * Você deve habilitar o Acesso Remoto ao nó principal do cluster após sua criação. Navegue até a guia **Configuração** e clique em **Habilitar Remoto**. Esta etapa especifica as credenciais de usuário usadas para logon remoto.
3. [Criar um espaço de trabalho de Azure Machine Learning](../studio/create-workspace.md): esse espaço de trabalho Azure Machine Learning é usado para criar modelos de aprendizado de máquina. Essa tarefa é abordada depois de concluir uma análise inicial e redução de dados usando o cluster HDInsight.

## <a name="getdata"></a>Obter os dados de uma fonte de pública
> [!NOTE]
> Essa normalmente é uma tarefa **Admin** .
> 
> 

Para obter o conjunto de dados [Corridas de Táxi em NYC](http://www.andresmh.com/nyctaxitrips/) do seu local público, você pode usar qualquer um dos métodos descritos em [Mover dados bidirecionalmente no Armazenamento de Blobs do Azure](move-azure-blob.md) para copiar os dados para o seu computador.

Neste artigo, descrevemos como usar AzCopy para transferir os arquivos que contêm dados. Para baixar e instalar o AzCopy, siga as instruções em [Introdução ao utilitário de linha de comando do AzCopy](../../storage/common/storage-use-azcopy.md).

1. Em uma janela de Prompt de Comando, execute os seguintes comandos do AzCopy, substituindo *<path_to_data_folder>* pelo destino desejado:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Quando a cópia for concluída, um total de 24 arquivos compactados estarão na pasta de dados escolhida. Descompacte os arquivos baixados para o mesmo diretório em seu computador local. Tome nota da pasta em que os arquivos descompactados estão. Essa pasta, que será chamada de *<path\_to\_unzipped_data\_files\>*, é o que se segue.

## <a name="upload"></a>Carregar os dados para o contêiner padrão do cluster do Hadoop do Azure HDInsight
> [!NOTE]
> Essa normalmente é uma tarefa **Admin** .
> 
> 

Nos comandos de AzCopy a seguir, substitua os seguintes parâmetros pelos valores reais especificados ao criar o cluster de Hadoop e extrair os arquivos de dados.

* ***&#60;caminho_para_pasta_de_dados>*** o diretório (juntamente com o caminho) no computador que contêm os arquivos de dados descompactados  
* ***&#60;nome da conta de armazenamento do cluster do Hadoop>*** a conta de armazenamento associada ao cluster HDInsight
* ***&#60;contêiner padrão do cluster do Hadoop>*** o contêiner padrão usado pelo seu cluster. Observe que o nome do contêiner padrão geralmente é o mesmo nome que o do cluster em si. Por exemplo, se o cluster se chamar "abc123.azurehdinsight.net", o contêiner padrão será abc123.
* ***&#60;chave de conta de armazenamento>*** a chave para a conta de armazenamento usada pelo cluster

Em um Prompt de Comando ou uma janela do Windows PowerShell no seu computador, execute estes dois comandos de AzCopy.

Este comando carrega os dados de corrida para o diretório ***nyctaxitripraw*** no contêiner padrão do cluster do Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Este comando carrega os dados de tarifa para o diretório ***nyctaxifareraw*** no contêiner padrão do cluster do Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Os dados agora devem estar no armazenamento de Blob do Azure e prontos para serem consumidos dentro do cluster HDInsight.

## <a name="#download-hql-files"></a>Faça logon no nó principal do cluster Hadoop e prepare-se para análise exploratória de dados
> [!NOTE]
> Essa normalmente é uma tarefa **Admin** .
> 
> 

Para acessar o nó principal do cluster para análise exploratória de dados e redução de dados, siga o procedimento descrito em [Acessar o nó principal do cluster do Hadoop](customize-hadoop-cluster.md#headnode).

Neste passo a passo, podemos usar principalmente consultas escritas em [Hive](https://hive.apache.org/), uma linguagem de consulta do tipo SQL, para realizar explorações de dados preliminares. As consultas de Hive são armazenadas em arquivos .hql. Podemos então reduzir esses dados a serem usados no Azure Machine Learning para criar modelos.

Para preparar o cluster para análise exploratória de dados, podemos baixar os arquivos de .hql que contêm os scripts de Hive relevantes de [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) para um diretório local (C:\temp) no nó principal. Para fazer isso, abra o **Prompt de Comando** de dentro do nó principal do cluster e emita os comandos a seguir:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Esses dois comandos baixarão todos os arquivos .hql necessários neste passo a passo para o diretório local ***C:\temp&#92;*** no nó principal.

## <a name="#hive-db-tables"></a>Criar banco de dados e tabelas Hive particionadas por mês
> [!NOTE]
> Essa normalmente é uma tarefa **Admin** .
> 
> 

Agora estamos prontos para criar tabelas Hive para nosso conjunto de dados de táxi NYC.
No nó principal do cluster do Hadoop, abra a ***Linha de Comando do Hadoop*** na área de trabalho do nó principal e entre no diretório de Hive digitando o comando

    cd %hive_home%\bin

> [!NOTE]
> **Execute todos os comandos do Hive neste passo a passo no prompt do diretório bin/ do Hive acima. Isso solucionará automaticamente quaisquer problemas de caminho. Usamos os termos "Prompt do diretório do Hive", "prompt do diretório bin/ do Hive" e "Linha de Comando do Hadoop" alternadamente. neste passo a passo.**
> 
> 

No prompt do diretório Hive, digite o seguinte comando na linha de comando do Hadoop do nó principal para enviar a consulta Hive para criar as tabelas e o banco de dados Hive:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Aqui está o conteúdo do arquivo ***C:\temp\sample\_hive\_create\_db\_e\_tables.hql*** que cria o banco de dados do Hive ***nyctaxidb*** e as tabelas ***trip*** e ***fare***.

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

Esse script do Hive cria duas tabelas:

* a tabela "corrida" contém detalhes da corrida de cada jornada (detalhes do condutor, tempo de busca, distância da corrida e tempos)
* a tabela "tarifa" contém detalhes da tarifa (valor da tarifa, gorjeta, pedágio e sobretaxas).

Se precisar de qualquer assistência adicional com esses procedimentos ou desejar investigar procedimentos alternativos, veja a seção [Enviar consultas Hive diretamente da Linha de Comando do Hadoop ](move-hive-tables.md#submit).

## <a name="#load-data"></a>Carregar dados para tabelas Hive de partições
> [!NOTE]
> Essa normalmente é uma tarefa **Admin** .
> 
> 

O conjunto de dados de táxi de NYC tem um particionamento natural por mês, que usamos para habilitar os tempos de processamento e consulta. Os comandos do PowerShell abaixo (emitidos no diretório Hive usando a **Linha de Comando do Hadoop**) carregam dados para as tabelas Hive "corrida" e "tarifa" particionadas por mês.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

O arquivo *sample\_hive\_load\_data\_by\_partitions.hql* contém os seguintes comandos **LOAD**.

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Observe que várias consultas de Hive que usamos aqui no processo de exploração envolvem a pesquisa em uma única partição ou em apenas algumas das partições. Mas essas consultas podem ser executadas em todos os dados.

### <a name="#show-db"></a>Mostra os bancos de dados no cluster do Hadoop do HDInsight
Para mostrar os bancos de dados criados no cluster do Hadoop do HDInsight dentro da janela de Linha de Comando do Hadoop, execute o seguinte comando na Linha de Comando do Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Mostrar as tabelas no banco de dados nyctaxidb
Para mostrar as tabelas no banco de dados nyctaxidb, execute o seguinte comando na Linha de Comando do Hadoop:

    hive -e "show tables in nyctaxidb;"

Podemos afirmar que as tabelas são particionadas emitindo o comando a seguir:

    hive -e "show partitions nyctaxidb.trip;"

A saída esperada é mostrada abaixo:

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

Da mesma forma, podemos garantir que a tabela de passagens é particionada emitindo o comando a seguir:

    hive -e "show partitions nyctaxidb.fare;"

A saída esperada é mostrada abaixo:

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

## <a name="#explore-hive"></a>Exploração de dados e engenharia de recursos no Hive
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

As tarefas de exploração de dados e engenharia de recursos para os dados carregados nas tabelas Hive podem ser realizados usando consultas de Hive. Aqui estão exemplos de tais tarefas que percorreremos ao longo desta seção:

* Exibir os 10 primeiros registros em ambas as tabelas.
* Explorar as distribuições de dados de alguns campos em períodos diferentes.
* Investigar a qualidade dos dados dos campos de longitude e latitude.
* Gerar rótulos de classificação binária e multiclasse com base em **tip\_amount**.
* Gerar recursos computando as distâncias diretas de viagem.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Exploração: exibir os 10 principais registros na tabela de corridas
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

Para ver a aparência dos dados, examinamos 10 registros de cada tabela. Execute as duas consultas a seguintes separadamente no prompt do diretório Hive no console da linha de comando do Hadoop para inspecionar os registros.

Para obter os primeiros 10 registros na tabela "corrida" do primeiro mês:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Para obter os primeiros 10 registros na tabela "tarifa" do primeiro mês:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Costuma ser útil salvar os registros em um arquivo para exibição conveniente. Uma pequena alteração à consulta acima realiza isto:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Exploração: exibir o número de registros em cada uma das 12 partições
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

É de interesse como o número de corridas varia durante o ano calendário. Agrupar por mês permite ver a aparência de distribuição de corridas.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Isso produz:

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

Aqui, a primeira coluna é o mês e a segunda é o número de corridas para aquele mês.

Também podemos contar o número total de registros em nosso conjunto de dados de corrida emitindo o comando a seguir no prompt do diretório Hive.

    hive -e "select count(*) from nyctaxidb.trip;"

Isso resulta em:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Usando comandos semelhantes aos mostrados para o conjunto de dados de corrida, podemos emitir consultas do prompt do diretório Hive para o conjunto de dados de tarifa para validar o número de registros.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Isso produz:

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

Observe que o mesmo número exato de corridas por mês é retornado para os dois conjuntos de dados. Isso fornece a primeira validação de que os dados foram carregados corretamente.

A contagem do número total de registros no conjunto de dados de tarifa pode ser feita usando o comando a seguir no prompt do diretório Hive:

    hive -e "select count(*) from nyctaxidb.fare;"

Isso resulta em:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

O número total de registros em ambas as tabelas também é o mesmo. Isso fornece a segunda validação de que os dados foram carregados corretamente.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: distribuição de corridas por licença
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

Este exemplo identifica o medalhão (número do táxi) com mais de 100 corridas dentro de um determinado período de tempo. A consulta aproveita o acesso à tabela particionada, já que ele está condicionada à variável de partição **mês**. Os resultados da consulta são gravados em um arquivo local queryoutput.tsv em `C:\temp` no nó principal.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Aqui está o conteúdo do arquivo *sample\_hive\_trip\_count\_by\_medallion.hql* para inspeção.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

O Medallion no conjunto de dados de táxi de NYC identifica um táxi exclusivo. Podemos identificar quais táxis estão "ocupados" perguntando quais fizeram mais do que um determinado número de corridas em um determinado período de tempo. O exemplo a seguir identifica táxis que fizeram mais de cem corridas nos primeiros três meses e salva os resultados da consulta em um arquivo local, C:\temp\queryoutput.tsv.

Aqui está o conteúdo do arquivo *sample\_hive\_trip\_count\_by\_medallion.hql* para inspeção.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

No prompt da pasta Hive, emita o comando a seguir:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploração: distribuição de corridas por medallion e hack_license
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

Ao explorar um conjunto de dados, frequentemente queremos examinar o número de co-ocorrências de grupos de valores. Esta seção fornece um exemplo de como fazer isso para táxis e condutores.

O arquivo *sample\_hive\_trip\_count\_by\_medallion\_license.hql* agrupa o conjunto de dados de tarifas "medallion" e "hack_license" e retorna a contagem de cada combinação. Abaixo estão os conteúdos.

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Esta consulta retorna combinações de táxi e condutor específico por número decrescente de corridas.

No prompt do diretório do Hive, execute:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Os resultados da consulta são gravados em um arquivo local C:\temp\queryoutput.tsv.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitudelatitude-records"></a>Exploração: avaliar a qualidade dos dados através da verificação de registros de latitude/longitude inválidos
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

Um objetivo comum da análise exploratória de dados é eliminar registros inválidos ou incorretos. O exemplo nesta seção determina se os campos de latitude ou longitude contêm um valor muito fora da área de NYC. Uma vez que é provável que esses registros tenham valores incorretos de latitude de longitude, queremos eliminá-los de todos os dados a serem usados para modelagem.

Aqui está o conteúdo do arquivo *sample\_hive\_quality\_assessment.hql* para inspeção.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


No prompt do diretório do Hive, execute:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

O argumento *-S* incluído neste comando suprime a impressão de tela de status dos trabalhos de Mapear/Reduzir de Hive. Isso é útil, pois ele torna a tela de impressão da saída da consulta de Hive mais legível.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploração: distribuições de classe binária de gorjetas para corridas
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

Para o problema de classificação binária descrito na seção [Exemplos de tarefas de previsão](hive-walkthrough.md#mltasks) , é útil saber se uma dica foi fornecida ou não. Essa distribuição de gorjetas é binária:

* tip given(Class 1, tip\_amount > $0)  
* no tip (Class 0, tip\_amount = $0).

O arquivo *sample\_hive\_tipped\_frequencies.hql* mostrado a seguir faz isso.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

No prompt do diretório do Hive, execute:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Exploração: distribuições de classe na configuração multiclasse
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

Para o problema de classificação multiclasse descrito na seção [Exemplos de tarefas de previsão](hive-walkthrough.md#mltasks) , esse conjunto de dados também se presta a uma classificação natural em que gostaríamos de prever o valor das gorjetas fornecidas. Podemos usar compartimentos para definir intervalos de gorjeta na consulta. Para obter as distribuições de classe para os vários intervalos de gorjetas, usamos o arquivo *sample\_hive\_tip\_range\_frequencies.hql*. Abaixo estão os conteúdos.

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

Execute o seguinte comando no console de Linha de Comando do Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-direct-distance-between-two-longitude-latitude-locations"></a>Exploração: calcular a distância direta entre dois locais de latitude-longitude
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

Ter uma medida da distância direta nos permite descobrir a discrepância entre ela e a distância real da corrida. Podemos motivar esse recurso ressaltando que um passageiro pode ter menos probabilidade de dar gorjeta se eles descobrir que o condutor intencionalmente o levou por uma rota muito mais longa.

Para ver a comparação entre a distância real da corrida e a [Distância do Haverseno](http://en.wikipedia.org/wiki/Haversine_formula) entre dois pontos de latitude-longitude (a distância do "grande círculo"), usamos as funções trigonométricas disponíveis no Hive, assim:

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

Na consulta acima, R é o raio da Terra em milhas e pi é convertido para radianos. Observe que os pontos de latitude-longitude são "filtrados" para remover os valores que não são da área de NYC.

Nesse caso, podemos gravar nossos resultados em um diretório chamado "queryoutputdir". A sequência de comandos mostrada abaixo primeiro cria esse diretório de saída e, em seguida, executa o comando do Hive.

No prompt do diretório do Hive, execute:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Os resultados da consulta são gravados em nove blobs do Azure ***queryoutputdir/000000\_0*** a ***queryoutputdir/000008\_0*** no contêiner padrão do cluster Hadoop.

Para ver o tamanho dos blobs individuais, executamos o seguinte comando no prompt do diretório de Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Assim, para ver o conteúdo de um determinado arquivo, digamos, 000000\_0, usamos o comando `copyToLocal` do Hadoop.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> O `copyToLocal` pode ser muito lento para arquivos grandes e não é recomendado para uso com eles.  
> 
> 

A principal vantagem de os dados residirem em um blob do Azure é que podemos podem explorar os dados dentro de Azure Machine Learning usando o módulo [Importar Dados][import-data].

## <a name="#downsample"></a>Para reduzir dados e criar modelos no Azure Machine Learning
> [!NOTE]
> Essa é normalmente é uma tarefa de **Cientista de Dados** .
> 
> 

Após a fase de análise exploratória de dados, agora estamos prontos para reduzir os dados para a criação de modelos no Azure Machine Learning. Nesta seção, mostraremos como usar uma consulta do Hive para reduzir os dados, que são acessados do módulo [Importar Dados][import-data] no Azure Machine Learning.

### <a name="down-sampling-the-data"></a>Reduzindo os dados
Há duas etapas neste procedimento. Primeiro, unimos as tabelas **nyctaxidb.trip** e **nyctaxidb.fare** em três chaves presentes em todos os registros: "medallion", "hack\_license" e "pickup\_datetime". Então geramos um rótulo de classificação binária **tipped** e um rótulo de classificação multiclasse **tip\_class**.

Para poder usar a busca dados diretamente no módulo [Importar Dados][import-data] no Azure Machine Learning, é necessário armazenar os resultados da consulta anterior em uma tabela interna do Hive. No que vem em seguida, criamos uma tabela interna do Hive e preenchemos seus conteúdos com os dados unidos e reduzidos.

A consulta se aplica a funções Hive padrão diretamente para gerar a hora do dia, a semana do ano, o dia da semana (1 significa segunda-feira e 7 representa domingo) dos campos de "pickup\_datetime" e a distância direta entre os locais de subida e descida. Os usuários podem consultar [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) para ver uma lista de tais funções.

Essa consulta também reduz as amostras dos dados para que os resultados da consulta sejam adequados ao Azure Machine Learning Studio. Apenas cerca de 1% do conjunto de dados original é importado para o Studio.

A seguir, os conteúdos do arquivo *sample\_hive\_prepare\_for\_aml\_full.hql*, que prepara os dados para o modelo de compilação no Azure Machine Learning.

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

Para executar essa consulta, no prompt do diretório do Hive:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Agora temos uma tabela interna “nyctaxidb.nyctaxi_downsampled_dataset”, que pode ser acessada usando o módulo [Importar Dados][import-data] do Azure Machine Learning. Além disso, podemos usar esse conjunto de dados para criar modelos de Machine Learning.  

### <a name="use-the-import-data-module-in-azure-machine-learning-to-access-the-down-sampled-data"></a>Use o módulo Importar Dados no Azure Machine Learning para acessar os dados reduzidos
Como pré-requisitos para a emissão de consultas Hive no módulo [Importar Dados][import-data] do Azure Machine Learning, precisamos de acesso a um espaço de trabalho do Azure Machine Learning e acesso às credenciais do cluster e sua conta de armazenamento associada.

Alguns detalhes sobre o módulo [Importar Dados][import-data] e os parâmetros de entrada:

**O URI do servidor HCatalog**: se o nome do cluster for abc123, será simplesmente: https://abc123.azurehdinsight.net

**Nome de conta de usuário do Hadoop**: o nome de usuário escolhido para o cluster (**não** o nome de usuário de acesso remoto)

**Senha da conta ser do Hadoop**: a senha escolhida para o cluster (**não** a senha de acesso remoto)

**Local dos dados de saída** : é escolhido para ser o Azure.

**Nome da Conta de Armazenamento do Azure** : nome da conta de armazenamento padrão associada ao cluster.

**Nome do contêiner do Azure** : pe o nome do contêiner padrão para o cluster e normalmente o mesmo que o nome do cluster. Para um cluster chamado "abc123", é simplesmente abc123.

> [!IMPORTANT]
> **Qualquer tabela que desejamos consultar usando o módulo [Importar Dados][import-data] no Azure Machine Learning deve ser uma tabela interna.** Uma dica para determinar se uma tabela T em um banco de dados D.db é uma tabela interna é a seguinte:
> 
> 

No prompt do diretório Hive, emita o comando a seguir:

    hdfs dfs -ls wasb:///D.db/T

Se a tabela for uma tabela interna e preenchida, seu conteúdo deverá aparecer aqui. Outra maneira de determinar se uma tabela é uma tabela interna é usar o Azure Storage Explorer. Use-o para navegar até o nome do contêiner padrão do cluster e, em seguida, filtre pelo nome da tabela. Se a tabela e seu conteúdo aparecerem, isso confirma que ela é uma tabela interna.

Este é um instantâneo da consulta Hive e do módulo [Importar Dados][import-data]:

![Consulta de hive para o módulo Importar Dados](./media/hive-walkthrough/1eTYf52.png)

Observe que, uma vez que nossos dados reduzidos residem no contêiner padrão, a consulta resultante da seção do Azure Machine Learning é muito simples e é apenas "SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data".

O conjunto de dados agora pode ser usado como o ponto de partida para criar modelos de Machine Learning.

### <a name="mlmodel"></a>Compilar modelos no Azure Machine Learning
Agora estamos prontos para prosseguir com a criação e implantação de modelo no [Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos para uso para resolver os problemas de previsão identificados acima:

**1. Classificação binária**: prever se uma gorjeta foi ou não paga em uma corrida.

**Aprendiz usado:** regressão logística de classe dois

a. Para esse problema, nosso rótulo (ou classe) de destino é "tipped". Nosso conjunto de dados original convertidos tem algumas colunas que são vazamentos de destino para esse teste de classificação. Em particular: tip\_class, tip\_amount e total\_amount revelam informações sobre a etiqueta de destino que não estão disponível no momento do teste. Podemos deixar de considerar essas colunas usando o módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

O instantâneo abaixo mostra o nosso teste para prever se uma gorjeta será paga ou não uma determinada corrida.

![Instantâneo de teste](./media/hive-walkthrough/QGxRz5A.png)

b. Para esse teste, nossas distribuições de rótulo de destino foram de aproximadamente 1:1.

O instantâneo a seguir mostra a distribuição dos rótulos de classe de gorjeta para o problema de classificação binária.

![Distribuição de rótulos de classe de dica](./media/hive-walkthrough/9mM4jlD.png)

Como resultado, obtemos um AUC de 0,987, conforme mostrado na figura abaixo.

![Valor AUC](./media/hive-walkthrough/8JDT0F8.png)

**2. Classificação multiclasse**: para prever o intervalo de valores de gorjetas pagas pela corrida usando as classes definidas anteriormente.

**Aprendiz usado:** regressão logística de várias classes

a. Para esse problema, nosso rótulo de destino (ou classe) é "tip\_class", que pode ter um dos cinco valores (0,1,2,3,4). Como no caso de classificação binária, temos algumas colunas que são vazamentos de destino para esse experimento. Em particular: tipped, tip\_amount, total\_amount revelam informações sobre a etiqueta de destino que não estão disponível no momento do teste. Podemos remover essas colunas usando o módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

O instantâneo a seguir mostra a nossa experiência para prever em qual bin uma gorjeta provavelmente ficará (Classe 0: tip = $0, classe 1 : gorjeta > $0 e gorjeta <= $5, Classe 2 : gorjeta > $5 e gorjeta <= $10, Classe 3 : gorjeta > $10 e gorjeta <= $20, Classe 4 : gorjeta > $20)

![Instantâneo de teste](./media/hive-walkthrough/5ztv0n0.png)

Agora mostraremos a aparência de nossa distribuição de classe de teste real. Podemos ver que, embora a Classe 0 e a Classe 1 sejam predominantes, as outras classes são raras.

![Distribuição de classe de teste](./media/hive-walkthrough/Vy1FUKa.png)

b. Neste teste, usamos uma matriz de confusão para examinar as precisões de previsão. Isso é mostrado abaixo.

![Matriz de confusão](./media/hive-walkthrough/cxFmErM.png)

Observe que, embora as precisões de classe nas classes predominantes sejam muito boas, o modelo não faz um bom trabalho de "aprender" nas classes mais raras.

**3. Tarefa de regressão**: prever o valor da gorjeta paga por uma corrida.

**Aprendiz usado:** árvore de decisão aprimorada

a. Para esse problema, nosso rótulo (ou classe) de destino é "tip\_amount". Nesse caso, nossos vazamentos de destino são: tipped, tip\_class, total\_amount; todas essas variáveis revelam informações sobre a quantidade de gorjeta normalmente indisponível no momento de teste. Podemos remover essas colunas usando o módulo [Selecionar Colunas no Conjunto de Dados][select-columns].

O instantâneo abaixo mostra nossa experiência para prever o valor da gorjeta dada.

![Instantâneo de teste](./media/hive-walkthrough/11TZWgV.png)

b. Para problemas de regressão, medimos as precisões de nossa previsão examinando o erro ao quadrado em previsões, o coeficiente de determinação e assim por diante. Vamos mostrar isso abaixo.

![Estatísticas de previsão](./media/hive-walkthrough/Jat9mrz.png)

Podemos ver que o coeficiente de determinação é de 0,709, implicando que aproximadamente 71% da variância é explicada por nossos coeficientes de modelo.

> [!IMPORTANT]
> Para saber mais sobre o Azure Machine Learning e como acessá-lo e usá-lo, veja [O que é Machine Learning?](../studio/what-is-machine-learning.md). Um recurso muito útil para brincar com vários testes de Machine Learning no Azure Machine Learning é a [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/). A galeria abrange uma gama de experimentos e fornece uma introdução abrangente para o intervalo de recursos de Azure Machine Learning.
> 
> 

## <a name="license-information"></a>Informações de Licença
Este passo a passo do exemplo e os scripts que o acompanham são compartilhados pela Microsoft sob a licença MIT. Verifique o arquivo LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

## <a name="references"></a>Referências
•   [Página de download de Viagens de Táxi de NYC, de Andrés Monroy](http://www.andresmh.com/nyctaxitrips/)  
•   [Dados de Viagem de Táxi de FOILing NYC, de Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•   [Pesquisa e estatísticas de comissionamento de táxis e limusines de NYC](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
