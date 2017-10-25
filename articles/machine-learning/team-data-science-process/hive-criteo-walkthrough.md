---
title: "O Processo de Ciência de Dados de Equipe em ação - usando um cluster Hadoop do Azure HDInsight em um conjunto de dados de 1 TB | Microsoft Docs"
description: "Usando o Processo de Ciência de Dados de Equipe para um cenário completo que emprega um cluster Hadoop do HDInsight para criar e implantar um modelo usando um conjunto de dados grande (1 TB) publicamente disponível"
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 72d958c4-3205-49b9-ad82-47998d400d2b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: 13dc1b516946aadc9c8a57a55768113bc925e63e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>O Processo de Ciência de Dados de Equipe em ação - usando um cluster Hadoop do Azure HDInsight em um conjunto de dados de 1 TB

Neste passo a passo, demonstramos como usar o Processo de Ciência de Dados de Equipe em um cenário completo com um [cluster Hadoop do Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, apresentar dados de engenharia e reduzir dados de exemplo de um dos conjuntos de dados [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) publicamente disponível. Usamos o Azure Machine Learning para criar um modelo de classificação binária nesses dados. Nós também mostramos como publicar um desses modelos como um serviço Web.

Também é possível usar um bloco de anotações do IPython para executar as tarefas apresentadas nesse passo a passo. Usuários que gostariam de testar essa abordagem devem consultar o tópico [Passo a passo da Criteo usando uma conexão ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .

## <a name="dataset"></a>Descrição do conjunto de dados da Criteo
O conjunto de dados da Criteo é um conjunto de dados de previsão de cliques que contém cerca de 370 GB de arquivos TSV comprimidos em gzip (cerca de 1,3 TB de arquivos não comprimidos), totalizando mais de 4,3 bilhões de registros. Ele é obtido a partir de 24 dias de cliques de dados disponibilizados pela [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Para a conveniência dos cientistas de dados, nós descompactamos os dados disponibilizados para fazermos os experimentos.

Cada registro deste conjunto de dados contém 40 colunas:

* a primeira coluna é uma coluna de rótulos que indica se o usuário clica em um **anúncio** (valor 1) ou não clica (valor 0)
* as 13 colunas seguintes são numéricas e
* as últimas 26 colunas são colunas categóricas

As colunas são anônimas e usam uma série de nomes enumerados: "Col1" (para a coluna de rótulos) a "Col40" (para a última coluna categórica).            

Este é um trecho das 20 primeiras colunas de duas observações (linhas) desse conjunto de dados:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Há valores ausentes nas colunas numéricas e categóricas do conjunto de dados. Nós descrevemos um método simples para lidar com os valores ausentes. Detalhes adicionais dos dados são explorados quando os armazenamos em tabelas do Hive.

**Definição:** *CTR (taxa de clickthrough):* é o percentual de cliques nos dados. Neste conjunto de dados da Criteo, a CTR é de cerca de 3,3% ou 0,033.

## <a name="mltasks"></a>Exemplos de tarefas de previsão
Dois exemplos de problemas de previsão são abordados neste passo a passo:

1. **Classificação binária**: prevê se um usuário clicou em um anúncio:
   
   * Classe 0: não clicou
   * Classe 1: clicou
2. **Regressão**: prevê a probabilidade de um clique no anúncio por meio de características do usuário.

## <a name="setup"></a>Configurar um cluster Hadoop do HDInsight para ciência de dados
**Observação:** normalmente, esta é uma tarefa para o **Administrador**.

Configure seu ambiente de Ciência de dados do Azure para a criação de soluções analíticas de previsão com clusters do HDInsight em três etapas:

1. [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md): esta conta de armazenamento é usada para armazenar dados no Armazenamento de Blob do Azure. Os dados usados em clusters do HDInsight são armazenados aqui.
2. [Personalizar clusters Hadoop do Azure HDInsight para ciência de dados](customize-hadoop-cluster.md): esta etapa cria um cluster Hadoop do Azure HDInsight com o Anaconda Python 2.7 de 64 bits instalado em todos os nós. Há duas etapas importantes (descritas neste tópico) a serem executadas para personalizar o cluster do HDInsight.
   
   * Você deve vincular a conta de armazenamento criada na etapa 1 ao cluster do HDInsight quando ele é criado. Essa conta de armazenamento é usada para acessar dados que podem ser processados dentro do cluster.
   * Você deve habilitar o Acesso Remoto ao nó principal do cluster após sua criação. Lembre das credenciais de acesso remoto que você especificar aqui (diferentes daqueles especificadas para o cluster durante sua criação): você precisa delas para concluir os procedimentos a seguir.
3. [Criar um espaço de trabalho do AM do Azure](../studio/create-workspace.md): este espaço de trabalho do Azure Machine Learning é usado para criar modelos de aprendizado de máquina após uma exploração de dados inicial e para reduzir a resolução no cluster do HDInsight.

## <a name="getdata"></a>Obter e consumir dados de uma fonte de pública
O conjunto de dados da [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) pode ser acessado clicando no link, aceitando os termos de uso e fornecendo um nome. Um instantâneo desse processo é mostrado aqui:

![Aceitar os termos da Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Clique em **Continuar a Baixar** para saber mais sobre o conjunto de dados e sua disponibilidade.

Os dados residem em um local público do [Armazenamento de blobs do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md): wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. O "wasb" refere-se ao local do Armazenamento de Blob do Azure. 

1. Os dados desse armazenamento de blobs público consistem de três subpastas de dados descompactados.
   
   1. A subpasta *raw/count/* contém os primeiros 21 dias de dados, de dia\_00 a dia\_20
   2. A subpasta *raw/train/* consiste de um dia de dados, dia\_21
   3. A subpasta *raw/test/* consiste de dois dias de dados, dia\_22 e dia\_23
2. Para quem quiser começar com os dados no gzip brutos, eles também estão disponíveis na pasta principal, *raw/*, como dia_NN.gz, em que NN vai de 00 a 23.

Uma abordagem alternativa para acessar, explorar e modelar esses dados que não requer nenhum download local é explicada mais adiante neste passo a passo, quando criamos tabelas de Hive.

## <a name="login"></a>Faça logon no nó principal do cluster
Para entrar no nó principal do cluster, use o [portal do Azure](https://ms.portal.azure.com) para localizar o cluster. Clique no ícone de elefante do HDInsight à esquerda e depois clique duas vezes no nome do cluster. Navegue até a guia **Configuration** , clique duas vezes no ícone CONNECT na parte inferior da página e insira suas credenciais de acesso remoto quando solicitado. Isso leva ao nó principal do cluster.

Um típico primeiro logon no nó principal do cluster é semelhante ao seguinte:

![Entrar no cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

À esquerda, vemos a "Linha de Comando do Hadoop", que é onde fazemos o trabalho pesado de exploração de dados. Nós também vemos duas URLs úteis - "Status do Hadoop Yarn" e "Nó de Nome do Hadoop". A URL do status de yarn mostra o andamento do trabalho e a URL de nó de nome fornece detalhes sobre a configuração do cluster.

Agora estamos prontos para começar a primeira parte do passo a passo: explorar dados usando o Hive e preparar dados para o Azure Machine Learning.

## <a name="hive-db-tables"></a> Criar tabelas e banco de dados do Hive
Para criar tabelas de Hive para nosso conjunto de dados da Criteo, abra a ***Linha de Comando do Hadoop*** na área de trabalho do nó principal e entre no diretório do Hive digitando o comando

    cd %hive_home%\bin

> [!NOTE]
> Execute todos os comandos do Hive neste passo a passo no prompt do diretório bin/do Hive. Isso soluciona automaticamente possíveis problemas de caminho. Nós usamos os termos "Prompt do diretório do Hive", "prompt do diretório bin/ do Hive" e "Linha de Comando do Hadoop" alternadamente.
> 
> [!NOTE]
> Para executar qualquer consulta de Hive, é sempre possível usar os seguintes comandos:
> 
> 

        cd %hive_home%\bin
        hive

Após a REPL do Hive aparecer com um sinal "hive>", basta recortar e colar a consulta para executá-la.

O código abaixo cria um banco de dados "criteo" e gera quatro tabelas:

* uma *tabela para gerar contagens* criada nos dias dia\_00 a dia\_20,
* uma *tabela para ser usada como o conjunto de dados de treinamento* criada no dia\_21 e
* duas *tabelas para uso como conjuntos de dados de teste* criadas no dia\_22 e no dia\_23, respectivamente.

Nós dividimos nosso conjunto de dados de teste em duas tabelas diferentes porque um dos dias é feriado, e queremos determinar se o modelo é capaz de detectar diferenças entre dias que são feriado e dias que não são, com base na taxa de clickthrough.

O script [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) é exibido aqui para fins de conveniência:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Observe que todas essas tabelas são externas, uma vez que apenas apontamos para locais do Armazenamento de Blob do Azure (wasb).

**Há duas maneiras de executar QUALQUER consulta de Hive que mencionamos agora.**

1. **Usando a linha de comando REPL do Hive**: a primeira maneira é emitir um comando "hive" e copiar e colar uma consulta na linha de comando REPL do Hive. Para fazer isto:
   
        cd %hive_home%\bin
        hive
   
     Agora na linha de comando REPL, recortar e colar a consulta a executa.
2. **Salvando consultas em um arquivo e executando o comando**: a segunda maneira é salvar as consultas em um arquivo .hql ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e emitir o seguinte comando para executar a consulta:
   
        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Confirme a criação do banco de dados e da tabela
Em seguida, confirmamos a criação do banco de dados com o seguinte comando no prompt do diretório bin/do Hive:

        hive -e "show databases;"

Isso fornece:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Isso confirma a criação do novo banco de dados, "criteo".

Para ver quais tabelas foram criadas, basta emitirmos o comando aqui no prompt do diretório bin/ do Hive:

        hive -e "show tables in criteo;"

Você verá a saída a seguir:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Exploração de dados no Hive
Agora, estamos prontos para fazer algumas explorações de dados básicas no Hive. Vamos começar pela contagem do número de exemplos nas tabelas de dados de treinamento e teste.

### <a name="number-of-train-examples"></a>Número de exemplos de treinamento
O conteúdo de [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) é mostrado aqui:

        SELECT COUNT(*) FROM criteo.criteo_train;

Isso resulta em:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Também é possível emitir o seguinte comando no prompt do diretório bin/do Hive:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Número de exemplos de teste nos dois conjuntos de dados de teste
Agora, nós contamos o número de exemplos nos dois conjuntos de dados de teste. O conteúdo de [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) é mostrado aqui:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Isso resulta em:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Como de costume, também podemos chamar o script no prompt do diretório bin/do Hive emitindo o comando:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Por fim, examinamos o número de exemplos de teste do conjunto de dados de teste com base no dia\_23.

O comando para fazer isso é semelhante ao que acabou de ser mostrado (confira [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Isso fornece:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribuição de rótulos no conjunto de dados de treinamento
A distribuição de rótulos no conjunto de dados de treinamento é de interesse. Para ver isso, mostramos o conteúdo de [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Isso gera a distribuição de rótulos:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Observe que o percentual de rótulos positivos é de cerca de 3,3% (consistente com o conjunto de dados original).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuições de histograma de algumas variáveis numéricas no conjunto de dados de treinamento
Podemos usar função nativa do Hive "histogram\_numeric" para descobrir como é a distribuição das variáveis numéricas. Eis o conteúdo de [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Isso produz o seguinte:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

A combinação de EXIBIÇÃO LATERAL -explodir no Hive serve para produzir uma saída semelhante ao SQL em vez da lista comum. Observe que, nessa tabela, a primeira coluna corresponde ao centro do compartimento e a segunda à frequência do compartimento.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentis aproximados de algumas variáveis numéricas no conjunto de dados de treinamento
Outro aspecto que é de interesse com relação às variáveis numéricas é o cálculo de percentuais aproximados. O "percentile\_approx" nativo do Hive faz isso para nós. O conteúdo de [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) é:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Isso resulta em:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Observe que, normalmente, a distribuição de percentis está estreitamente relacionada à distribuição de histograma de qualquer variável numérica.         

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Encontre o número de valores exclusivos para algumas colunas categóricas no conjunto de dados de treinamento
Continuando a exploração de dados, nós encontramos agora, para algumas colunas categóricas, o número de valores exclusivos que elas levam. Para fazer isso, mostramos o conteúdo de [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Isso resulta em:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Observe que Col15 tem valores exclusivos em 19M! Usar técnicas simples como codificação "one-hot" para codificar variáveis categóricas altamente dimensionais deste tipo é inviável. Em particular, nós explicamos e demonstramos uma técnica poderosa e robusta chamada [Aprendizado com contagens](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para lidar com esse problema de modo eficaz.

Finalizamos esta subseção examinando também o número de valores exclusivos para algumas outras colunas categóricas. O conteúdo de [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) é:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Isso resulta em:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Mais uma vez vemos que, com exceção de Col20, todas as colunas têm muitos valores exclusivos.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Contagens de ocorrências conjuntas de pares de variáveis categóricas no conjunto de dados de treinamento

As contagens de ocorrências conjuntas de pares de variáveis categóricas no conjunto de dados de treinamento também é de interesse. Isso pode ser determinado usando o código em [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Neste caso, ordenamos as contagens de modo inverso e examinamos as 15 primeiras. Isso nos fornece:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Reduzir a resolução de conjuntos de dados para o Azure Machine Learning
Após termos explorado os conjuntos de dados e demonstrado como podemos fazer esse tipo de exploração para quaisquer variáveis (incluindo combinações), agora nós reduzimos a resolução dos conjuntos de dados para que possamos criar modelos no Azure Machine Learning. Lembre-se de que o problema em que nos concentramos é: dado um conjunto de atributos de exemplo (valores de recursos da Col2 à Col40), podermos prever se Col1 é 0 (sem cliques) ou 1 (com clique).

Para reduzir nossos conjuntos de dados de treinamento e teste para 1% do tamanho original, usamos a função nativa RAND() do Hive. O seguinte script, [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) faz isso para o conjunto de dados de treinamento:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Isso resulta em:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

O script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) faz isso para dados de teste, dia\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Isso resulta em:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Por fim, o script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) faz isso para dados de teste, dia\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Isso resulta em:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Com isso, nós estamos prontos para usar nossos conjuntos de dados de treinamento e teste reduzidos para criar modelos no Azure Machine Learning.

Há um componente final importante antes de passarmos para o Azure Machine Learning, que é relacionado à tabela de contagem. Na próxima sub-seção, discutiremos este componente detalhadamente.

## <a name="count"></a> Uma breve discussão sobre a tabela de contagem
Como vimos, diversas variáveis categóricas têm uma dimensionalidade muito alta. Em nosso passo a passo, nós apresentamos uma técnica poderosa chamada [Aprendizado com contagens](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para codificar essas variáveis de maneira eficiente e robusta. Mais informações sobre essa técnica são disponibilizadas no link fornecido.

[!NOTE]
>Neste passo a passo, nos concentramos no uso de tabelas de contagem para produzir representações compactas de recursos categóricos de alta dimensionalidade. Essa não é a única maneira de codificar recursos categóricos. Para saber mais sobre outras técnicas, usuários interessados podem conferir [codificação one-hot](http://en.wikipedia.org/wiki/One-hot) e [hash de recursos](http://en.wikipedia.org/wiki/Feature_hashing).
>

Para criar tabelas de contagem com os dados de contagem, usamos os dados na pasta raw/count. Na seção de modelagem, mostramos aos usuários como criar essas tabelas de contagem para recursos categóricos do zero ou como usar uma tabela de contagem criada previamente para seus explorações. A seguir, quando falamos em "tabelas criadas previamente", nos referimos ao uso das tabelas de contagem que fornecemos. Fornecemos instruções detalhadas sobre como acessar essas tabelas na próxima seção.

## <a name="aml"></a> Criar um modelo com o Azure Machine Learning
Nosso processo de criação de modelo no Azure Machine Learning seguirá estas etapas:

1. [Obter os dados de tabelas do Hive no Azure Machine Learning](#step1)
2. [Criar o experimento: limpar os dados e criar recursos com tabelas de contagem](#step2)
3. [Criar, treinar e pontuar o modelo](#step3)
4. [Avaliar o modelo](#step4)
5. [Publicar o modelo como um serviço Web](#step5)

Agora, nós estamos prontos para criar modelos no Estúdio do Azure Machine Learning. Nossos dados reduzidos são salvos como tabelas de Hive do cluster. Nós usaremos o módulo **Importar Dados** do Azure Machine Learning para ler esses dados. As credenciais para acessar a conta de armazenamento deste cluster estão abaixo.

### <a name="step1"></a> Etapa 1: passar dados de tabelas do Hive para o Azure Machine Learning usando o módulo Importar Dados e selecioná-los para um teste de aprendizado de máquina
Comece selecionando **+NOVO** -> **EXPERIMENTO** -> **Experimento em Branco**. Depois, na caixa **Pesquisar** na parte superior esquerda, procure "Importar Dados". Arraste e solte o módulo **Importar Dados** na tela do experimento (a parte central da tela) para usar o módulo para acesso a dados.

Esta é a aparência do **Importar Dados** ao obter dados da tabela do Hive:

![Importar Dados obtém os dados](./media/hive-criteo-walkthrough/i3zRaoj.png)

Para o módulo **Importar Dados** , os valores dos parâmetros que são fornecidos no gráfico são somente exemplos do tipo de valores que você precisará fornecer. Aqui estão algumas diretrizes gerais sobre como preencher o conjunto de parâmetros para o módulo **Importar Dados** .

1. Escolha "Consulta de Hive" para **Fonte de dados**
2. Na caixa **Consulta de banco de dados Hive**, um simples SELECT * FROM <your\_database\_name.your\_table\_name> é suficiente.
3. **URI do servidor Hcatalog**: se o cluster é"abc", isso é simplesmente: https://abc.azurehdinsight.net
4. **Nome da conta de usuário do Hadoop**: o nome de usuário escolhido no momento da programação do cluster. (NÃO o nome de usuário do Acesso Remoto!)
5. **Senha da conta de usuário do Hadoop**: a senha para o nome de usuário escolhido no momento da programação do cluster. (NÃO a senha de Acesso Remoto!)
6. **Local dos dados de saída**: escolha "Azure"
7. **Nome da conta de armazenamento do Azure**: a conta de armazenamento associada ao cluster
8. **Chave da conta de armazenamento do Azure**: a chave da conta de armazenamento associada ao cluster.
9. **Nome do contêiner do Azure**: se o nome do cluster for "abc", normalmente costuma ser apenas "abc".

Após **Importar Dados** terminar de obter os dados (você verá a marca de seleção verde no Módulo), salve os dados como um Conjunto de Dados (com um nome da sua preferência). A aparência é a seguinte:

![Importar Dados salva os dados](./media/hive-criteo-walkthrough/oxM73Np.png)

Clique com o botão direito do mouse na porta de saída do módulo **Importar Dados** . Isso revela uma opção **Salvar como conjunto de dados** e uma opção **Visualizar**. A opção **Visualizar** , se clicada, exibe 100 linhas de dados, juntamente com um painel à direita que é útil para ver algumas estatísticas resumidas. Para salvar os dados, basta selecionar **Salvar como conjunto de dados** e seguir as instruções.

Para selecionar o conjunto de dados salvo para uso em um teste de aprendizado de máquina, localize os conjuntos de dados usando a caixa **Pesquisa** mostrada abaixo. Em seguida, basta digitar parcialmente o nome do conjunto de dados para acessá-lo e arrastar o conjunto de dados para o painel principal. Soltá-lo no painel principal o seleciona para uso na modelagem do aprendizado de máquina.

![Conjunto de dados Drage no painel principal](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Faça isso para os conjuntos de dados de treinamento e de teste. Além disso, lembre-se de usar o nome do banco de dados e das tabelas que você atribuiu para essa finalidade. Os valores usados na figura são somente para fins de ilustração.**
> 
> 

### <a name="step2"></a> Etapa 2: criar um experimento simples no Estúdio de Azure Machine Learning para predizer cliques/nenhum clique
Nosso experimento do AM do Azure tem esta aparência:

![Teste do Machine Learning](./media/hive-criteo-walkthrough/xRpVfrY.png)

Agora, vamos examinar os principais componentes nesse experimento. Como lembrete, primeiro precisamos arrastar nosso treinamento salvo e os conjuntos de dados de teste para a nossa tela de experimento.

#### <a name="clean-missing-data"></a>Limpar dados ausentes
O módulo **Limpar dados ausentes** faz o que o nome diz: limpa dados ausentes de maneiras que podem ser especificadas pelo usuário. Examinando este módulo, vemos isso:

![Limpar dados ausentes](./media/hive-criteo-walkthrough/0ycXod6.png)

Aqui, optamos por substituir todos os valores ausentes por um 0. Existem outras opções que podem ser vistas examinando os menus suspensos no módulo.

#### <a name="feature-engineering-on-the-data"></a>Engenharia de recurso nos dados
Pode haver milhões de valores exclusivos para alguns recursos categóricos de grandes conjuntos de dados. Usar métodos simples como codificação one-hot para representar recursos categóricos altamente dimensionais é totalmente impraticável. Neste passo a passo, demonstramos como usar recursos de contagem usando módulos internos de Azure Machine Learning para gerar representações compactas dessas variáveis categóricas de grande dimensão. O resultado final é um tamanho de modelo menor, tempos de treinamento mais rápidos e métricas de desempenho que são muito semelhantes a outras técnicas.

##### <a name="building-counting-transforms"></a>Criando transformações de contagem
Para criar recursos de contagem, usamos o módulo **Criar transformação de contagem** que está disponível no Azure Machine Learning. O módulo tem esta aparência:

![Módulo Compilar Transformação de Contagem](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![Módulo Compilar Transformação de Contagem](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT] 
> Na caixa **Contar colunas**, inserimos as colunas nas quais desejamos realizar contagens. Geralmente, essas são (conforme mencionado) colunas categóricas altamente dimensionais. No início, mencionamos que o conjunto de dados Criteo tem 26 colunas categóricas: de Col15 para Col40. Aqui, podemos contar todas elas e dar a elas seus índices (de 15 a 40 separados por vírgulas, como mostrado).
> 

Para usar o módulo no modo MapReduce (apropriado para grandes conjuntos de dados), precisamos de acesso a um cluster HDInsight Hadoop (aquele usado para a exploração de recurso também pode ser reutilizado para essa finalidade) e suas credenciais. Os valores anteriores ilustram a aparência dos valores preenchidos (substitua os valores fornecidos como ilustração por aqueles relevantes para seu próprio caso de uso).

![Parâmetros do módulo](./media/hive-criteo-walkthrough/05IqySf.png)

Na figura acima, vamos mostrar como inserir o local do blob de entrada. Esse local tem os dados reservados para a criação de tabelas de contagem.

Após a execução desse módulo, podemos salvar a transformação para mais tarde clicando com botão direito do mouse no módulo e selecionando a opção **Salvar como Transformação** :

![Opção “Salvar como transformação”](./media/hive-criteo-walkthrough/IcVgvHR.png)

Em nossa arquitetura de experimento mostrada acima, o conjunto de dados "ytransform2" corresponde exatamente a uma transformação de contagem salva. Para o restante desse experimento, assumimos que o leitor usou um módulo **Compilar transformação de contagem** em alguns dados para gerar contagens e usá-las para gerar recursos de contagem em conjuntos de dados de treinamento e de teste.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Escolhendo quais recursos de contagem incluir como parte dos conjuntos de dados de treinamento e de teste
Assim que tivermos uma transformação de contagem pronta, o usuário pode escolher quais recursos incluir nos seus conjuntos de dados de treinamento e de teste usando o módulo **Modificar parâmetros da tabela de contagem** . Vamos mostrar esse módulo aqui para fins de conclusão, mas, para manter a simplicidade, não use de fato no nosso experimento.

![Parâmetros Modificar Tabela de Contagem](./media/hive-criteo-walkthrough/PfCHkVg.png)

Nesse caso, como se vê, optamos por usar somente os log-possibilidades e ignorar a coluna de retirada. Também podemos definir parâmetros, como o limite da lixeira, quantos exemplos pseudo anteriores adicionar para suavização e se o ruído Laplaciano deve ser usado ou não. Todos esses são recursos avançados e deve-se observar que os valores padrão são um bom ponto de partida para usuários não familiarizados com esse tipo de geração de recurso.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformação de dados antes de gerar os recursos de contagem
Agora vamos nos concentrar em um ponto importante sobre como transformar nossos dados de treinamento e de teste antes de realmente gerar recursos de contagem. Observe que há dois módulos **Executar Script R** usados antes de podermos aplicar a transformação de contagem aos nossos dados.

![Executar Script R](./media/hive-criteo-walkthrough/aF59wbc.png)

Veja o primeiro script R:

![Primeiro script R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Nesse script R, renomeamos nossas colunas para nomes "Col1" a "Col40". Isso ocorre porque a transformação de contagem espera nomes nesse formato.

No segundo script R, podemos equilibrar a distribuição entre classes positivas e negativas (classes 1 e 0, respectivamente) reduzindo a classe negativa. O script R aqui mostra como fazer isso:

![Segundo script R](./media/hive-criteo-walkthrough/91wvcwN.png)

Nesse script R simples, usamos "pos\_neg\_ratio" para definir a quantidade de equilíbrio entre as classes positiva e negativa. Isso é importante, já que melhorar o desequilíbrio de classe normalmente tem benefícios de desempenho para problemas de classificação em que a distribuição de classe está distorcida (lembre-se de que em nosso caso, temos classes positivo 3,3% e 96,7% negativo).

##### <a name="applying-the-count-transformation-on-our-data"></a>Aplicando a transformação de contagem aos nossos dados
Por fim, podemos usar o módulo **Aplicar transformação** para aplicar as transformações de contagem aos nossos conjuntos de dados de treino e de teste. Este módulo usa a transformação da contagem salva como uma entrada e os conjuntos de dados de treinamento ou de teste como outra entrada e retorna dados com recursos de contagem. Isso é mostrado aqui:

![Aplicar o módulo de transformação](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Um trecho de como os recursos de contagem se parecem
É instrutivo observar a aparência dos recursos de contagem em nosso caso. Mostramos aqui um trecho disso:

![Recursos de contagem](./media/hive-criteo-walkthrough/FO1nNfw.png)

Nesse trecho, mostramos que para as colunas que pudemos contar, obtivemos as contagens e possibilidades de log além de qualquer retirada relevante.

Agora estamos prontos para criar um modelo de Azure Machine Learning usando esses conjuntos de dados transformados. Na próxima seção, vamos mostrar como isso pode ser feito.

### <a name="step3"></a>Etapa 3: Criar, treinar e pontuar o modelo

#### <a name="choice-of-learner"></a>Opção do aprendiz
Primeiro precisamos escolher um aprendiz. Vamos usar uma árvore de decisão aumentada, de duas classes, como nosso aprendiz. Aqui estão as opções padrão para esse aprendiz:

![Parâmetros da árvore de decisão aumentada de duas classes](./media/hive-criteo-walkthrough/bH3ST2z.png)

Para nosso experimento, vamos escolher os valores padrão. Podemos observar que os padrões são geralmente significativos e uma boa maneira de se obter linhas de base rápidas sobre o desempenho. Se desejar, depois de ter uma linha de base, você pode melhorar o desempenho varrendo parâmetros.

#### <a name="train-the-model"></a>Treinar o modelo
Para obter treinamento, podemos simplesmente invocar um módulo **Modelo de treinamento** . As duas entradas para ele são o aprendiz Árvore de Decisão Aumentada de Duas Classes e nosso conjunto de dados de treinamento. Isso é mostrado aqui:

![Módulo Treinar Modelo](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Pontuar o modelo
Assim que tivermos um modelo treinado, estamos prontos para avaliar o conjunto de dados de teste e seu desempenho. Podemos fazer isso usando o módulo **Modelo de pontuação** mostrado na figura a seguir, junto com um módulo **Avaliar modelo**:

![Módulo de Modelo de Pontuação](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> Etapa 4: Avaliar o modelo
Por fim, gostaríamos de analisar o desempenho do modelo. Normalmente, para problemas de classificação de duas classes (binária), uma boa medida é o AUC. Para visualizá-lo, vinculamos o módulo **Modelo de classificação** a um módulo **Modelo de avaliação**. Clicar em **Visualizar** no módulo **Modelo de avaliação** produz um gráfico semelhante ao seguinte exemplo:

![Avaliar o modelo de BDT do módulo](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Em problemas de classificação binária (ou de duas classes), uma boa medida da precisão de previsão é o AUC (área abaixo da curva). A seguir, mostramos nossos resultados usando esse modelo em nosso conjunto de dados de teste. Para obtê-los, clique com o botão direito do mouse na porta de saída do módulo **Modelo de avaliação** e selecione **Visualizar**.

![Módulo Visualizar modelo de avaliação](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> Etapa 5: Publicar o modelo como um serviço Web a ser consumido
A capacidade de publicar um modelo de Azure Machine Learning como serviços Web com um nível mínimo de confusão é um recurso valioso para torná-lo amplamente disponível. Depois disso, qualquer pessoa pode fazer chamadas para o serviço Web com dados de entrada para os quais precisem de previsões, e o serviço Web usa o modelo para retornar as previsões.

Para fazer isso, primeiro salvamos nosso modelo treinado como um objeto de Modelo Treinado. Isso é feito clicando com o botão direito do mouse no módulo **Modelo de treinamento** e usando a opção **Salvar como um Modelo Treinado**.

Em seguida, precisamos criar portas de entrada e saída para nosso serviço Web:

* uma porta de entrada usa dados da mesma forma como os dados para os quais precisamos de previsões
* uma porta de saída retorna os Rótulos Pontuados e as probabilidades associadas.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecionar algumas linhas de dados para a porta de entrada
Podemos, de maneira conveniente, usar uma **Transformação do tipo Apply SQL** para selecionar apenas 10 linhas para servirem como dados de porta de entrada. Selecione somente essas linhas de dados para a nossa porta de entrada usando a consulta SQL mostrada aqui:

![Dados na porta de entrada](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Serviço Web
Agora estamos prontos para executar um experimento pequeno que pode ser usado para publicar nosso serviço Web.

#### <a name="generate-input-data-for-webservice"></a>Gerar dados de entrada para o serviço Web
Como etapa inicial, como a tabela de contagem é grande, pegamos algumas linhas de dados de teste e geramos dados de saída por meio delas com recursos de contagem. Isso pode servir como o formato de dados de entrada para nosso serviço Web. Isso é mostrado aqui:

![Criar dados de entrada de BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Para o formato de dados de entrada, usamos a SAÍDA do módulo **Recursos de contagem** . Após o fim da execução do experimento, nós salvamos a saída do módulo **Recursos de contagem** como um conjunto de dados. Esse conjunto de dados é usado para os dados de entrada no serviço Web.
> 
> 

#### <a name="scoring-experiment-for-publishing-webservice"></a>Experimento de pontuação publicação do serviço Web
Primeiro, mostramos sua aparência. A estrutura essencial é um módulo do **Pontuar Modelo** que aceita o nosso objeto de modelo treinado e algumas linhas de dados de entrada que geramos nas etapas anteriores usando o módulo **Recursos de contagem**. Usamos "Selecionar Colunas do Conjunto de Dados" para projetar os Rótulos pontuados e as Probabilidades de pontuação.

![Projetar Colunas no Conjunto de Dados](./media/hive-criteo-walkthrough/kRHrIbe.png)

Observe como o módulo **Projetar Colunas no Conjunto de Dados** pode ser usado para “filtrar” dados de um conjunto de dados. Mostramos o conteúdo aqui:

![Filtragem com Selecionar Colunas no módulo Conjunto de Dados](./media/hive-criteo-walkthrough/oVUJC9K.png)

Para obter as portas de entrada e de saída azuis, basta clicar em **preparar serviço Web** no canto inferior direito. Executar esse experimento também nos permite publicar o serviço Web clicando no ícone **PUBLICAR SERVIÇO WEB** no canto inferior direito, mostrado aqui:

![PUBLICAR SERVIÇO WEB](./media/hive-criteo-walkthrough/WO0nens.png)

Após o serviço Web ser publicado, somos redirecionados para uma página parecida com esta:

![Painel de serviço Web](./media/hive-criteo-walkthrough/YKzxAA5.png)

Vemos dois links para serviços Web no lado esquerdo:

* O **REQUEST/RESPONSE** Service (ou RRS) é destinado a previsões únicas e é o que usamos neste workshop.
* O serviço **EXECUÇÃO EM LOTES** (BES) é usado para previsões em lotes e exige que os dados de entrada usados para fazer previsões residam no Armazenamento de Blob do Azure.

Clicar no link **SOLICITAÇÃO/RESPOSTA** nos leva a uma página que nos fornece um código gravado previamente em C#, python e R. Esse código pode ser usado de modo conveniente para fazer chamadas para o serviço Web. Observe que a chave da API nesta página deve ser usada para autenticação.

É conveniente copiar esse código python para uma nova célula no bloco de anotações IPython.

Aqui, mostramos um segmento de código Python com a chave de API correta.

![Código Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

Observe que substituímos a chave de API padrão com a chave de API de nossos serviços Web. Clicar em **Executar** nessa célula em um bloco de anotações IPython produz a seguinte resposta:

![Resposta do IPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Podemos ver que para os dois exemplos de teste sobre os quais perguntamos (na estrutura JSON do script Python), recebemos respostas no formulário "Scored Labels, Scored Probabilities". Observe que, neste caso, escolhemos os valores padrão que o código predefinido fornece (0 para todas as colunas numéricas e a cadeia de caracteres "value" para todas as colunas categóricas).

Isso conclui nosso passo a passo total mostrando como lidar com o conjunto de dados de grande dimensão usando o Azure Machine Learning. Começamos com um terabyte de dados, construímos um modelo de previsão e o implantamos como um serviço Web na nuvem.

