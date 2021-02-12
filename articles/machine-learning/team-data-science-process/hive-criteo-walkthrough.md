---
title: Usar o cluster Azure HDInsight Hadoop em um conjunto de dados de 1 TB-processo de ciência de dado de equipe
description: Usando o Processo de Ciência de Dados de Equipe para um cenário completo que emprega um cluster Hadoop do HDInsight para criar e implantar um modelo usando um conjunto de dados grande (1 TB) publicamente disponível
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
ms.openlocfilehash: e66bd0a4e56f63185d8361355d6cf8e0e29bc30b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305929"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>O processo de ciência de dados de equipe em ação – usando um cluster Azure HDInsight Hadoop em um conjunto de dados de 1 TB

Este passo a passo demonstra como usar o Processo de Ciência de Dados da Equipe em um cenário de ponta a ponta com um [cluster do Hadoop do Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, apresentar, projetar recursos e reduzir dados de exemplo de um dos conjuntos de dados [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) publicamente disponível. Ele usa o Azure Machine Learning para criar um modelo de classificação binária nesses dados. Também mostra como publicar um desses modelos como um serviço Web.

Também é possível usar um bloco de anotações do IPython para executar as tarefas apresentadas nesse passo a passo. Usuários que gostariam de testar essa abordagem devem consultar o tópico [Passo a passo da Criteo usando uma conexão ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Descrição do conjunto de dados da Criteo
O Criteo data é um conjunto de dados de previsão de clique que é de 370 GB de arquivos TSV compactados gzip (~ 1,3 TB descompactados), que abrangem mais de 4.300.000.000 registros. Ele é obtido a partir de 24 dias de cliques de dados disponibilizados pela [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Para a conveniência dos cientistas de dados, descompactamos os dados disponibilizados para experimentos.

Cada registro deste conjunto de dados contém 40 colunas:

* a primeira coluna é uma coluna de rótulos que indica se o usuário clica em um **anúncio** (valor 1) ou não clica (valor 0)
* as 13 colunas seguintes são numéricas e
* as últimas 26 colunas são colunas categóricas

As colunas são anônimas e usam uma série de nomes enumerados: "Col1" (para a coluna de rótulos) a "Col40" (para a última coluna categórica).

Este é um trecho das 20 primeiras colunas de duas observações (linhas) desse conjunto de dados:

> `Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20`
>
> `0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb`
>
> `0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb`

Há valores ausentes nas colunas numéricas e categóricas do conjunto de dados. É descrito um método simples para lidar com os valores ausentes. Detalhes adicionais dos dados são explorados aos armazená-los em tabelas do Hive.

**Definição:** *taxa de clickthrough (CTR):* essa métrica é a porcentagem de cliques nos dados. Neste conjunto de dados da Criteo, a CTR é de cerca de 3,3% ou 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exemplos de tarefas de previsão
Dois exemplos de problemas de previsão são abordados neste passo a passo:

1. **Classificação binária** : prevê se um usuário clicou em um anúncio:

   * Classe 0: não clicou
   * Classe 1: clicou
2. **Regressão** : prevê a probabilidade de um clique no anúncio por meio de características do usuário.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Configurar um cluster Hadoop do HDInsight para ciência de dados
> [!NOTE]
> Normalmente, essa etapa é uma tarefa de **administrador** .

Configure seu ambiente de Ciência de dados do Azure para a criação de soluções analíticas de previsão com clusters do HDInsight em três etapas:

1. [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md): esta conta de armazenamento é usada para armazenar dados no Armazenamento de Blob do Azure. Os dados usados em clusters do HDInsight são armazenados aqui.
2. [Personalizar clusters Hadoop do Azure HDInsight para ciência de dados](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md): esta etapa cria um cluster Hadoop do Azure HDInsight com o Anaconda Python 2.7 de 64 bits instalado em todos os nós. Há duas etapas importantes (descritas neste tópico) a serem executadas para personalizar o cluster do HDInsight.

   * Vincule a conta de armazenamento criada na etapa 1 com o cluster HDInsight quando ele for criado. Essa conta de armazenamento é usada para acessar dados que podem ser processados dentro do cluster.
   * Habilite o Acesso Remoto ao nó principal do cluster após sua criação. Lembre-se das credenciais de acesso remoto que você especificar aqui (diferentes das credenciais especificadas na criação do cluster): conclua os procedimentos a seguir.
3. [Criar um espaço de trabalho Azure Machine Learning Studio (clássico)](../classic/create-workspace.md): este Azure Machine Learning espaço de trabalho é usado para criar modelos de aprendizado de máquina após uma exploração inicial de dados e uma amostragem inativa no cluster HDInsight.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Obter e consumir dados de uma fonte de pública
O conjunto de dados da [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) pode ser acessado clicando no link, aceitando os termos de uso e fornecendo um nome. Um instantâneo é mostrado aqui:

![Aceitar os termos da Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Clique em **Continuar a Baixar** para saber mais sobre o conjunto de dados e sua disponibilidade.

Os dados residem em um local de [armazenamento de BLOBs do Azure](../../storage/blobs/storage-quickstart-blobs-dotnet.md) : wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/ . O "wasb" refere-se ao local do Armazenamento de Blob do Azure.

1. Os dados nesse armazenamento de BLOBs do Azure consistem em três subpastas de dados descompactados.

   1. A subpasta *raw/count/* contém os primeiros 21 dias de dados, de dia\_00 a dia\_20
   2. A subpasta *raw/train/* consiste de um dia de dados, dia\_21
   3. A subpasta *raw/test/* consiste de dois dias de dados, dia\_22 e dia\_23
2. Os dados de gzip brutos também estão disponíveis na pasta principal *RAW/* como day_NN. gz, em que NN vai de 00 a 23.

Uma abordagem alternativa para acessar, explorar e modelar esses dados que não requer nenhum download local é explicada mais adiante neste passo a passo, quando criamos tabelas de Hive.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Faça logon no nó principal do cluster
Para entrar no nó principal do cluster, use o [portal do Azure](https://ms.portal.azure.com) para localizar o cluster. Clique no ícone de elefante do HDInsight à esquerda e depois clique duas vezes no nome do cluster. Navegue até a guia **configuração** , clique duas vezes no ícone conectar na parte inferior da página e insira suas credenciais de acesso remoto quando solicitado, levando você ao cabeçalho do cluster.

Aqui está a aparência de um primeiro logon típico para o cluster cabeçalho:

![Entrar no cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

À esquerda, vemos a "Linha de Comando do Hadoop", que é nossa força para a exploração de dados. Observe duas URLs úteis: "Status do Hadoop Yarn" e "Nó de Nome do Hadoop". A URL do status de yarn mostra o andamento do trabalho e a URL de nó de nome fornece detalhes sobre a configuração do cluster.

Agora você está preparado e pronto para começar a primeira parte do passo a passo: explorar dados usando o Hive e preparar dados para o Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a> Criar banco de dados e tabelas do hive
Para criar tabelas do hive para nosso conjunto de Criteo, abra a *_linha de comando_* * do Hadoop na área de trabalho do nó principal e insira o diretório do hive inserindo o comando

```console
cd %hive_home%\bin
```

> [!NOTE]
> Execute todos os comandos do Hive neste passo a passo no prompt do diretório bin/do Hive. Isso soluciona automaticamente possíveis problemas de caminho. Os termos "Prompt do diretório do Hive", "prompt do diretório bin/ do Hive" e "Linha de Comando do Hadoop" são intercambiáveis.
>
> [!NOTE]
> Para executar qualquer consulta de Hive, é sempre possível usar os seguintes comandos:
>
> `cd %hive_home%\bin`
> `hive`

Após a REPL do Hive aparecer com um sinal "hive>", basta recortar e colar a consulta para executá-la.

O código a seguir cria um banco de dados "criteo" e, em seguida, gera quatro tabelas:

_ uma *tabela para gerar contagens* criadas nos dias do dia \_ 00 ao dia \_ 20,
* uma *tabela para ser usada como o conjunto de dados de treinamento* criada no dia\_21 e
* duas *tabelas para uso como conjuntos de dados de teste* criadas no dia\_22 e no dia\_23, respectivamente.

Divida o conjunto de dados de teste em duas tabelas diferentes, pois um dos dias é um feriado. O objetivo é determinar se o modelo pode detectar diferenças entre um feriado e um dia útil da taxa de clickthrough.

O script [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) é exibido aqui para fins de conveniência:

```hiveql
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
```

Todas essas tabelas são externas para que você possa apontar para seus locais de armazenamento de BLOBs do Azure (WASB).

**Há duas maneiras de executar a consulta ANY do Hive:**

* **Usando a linha de comando do hive repl** : a primeira é emitir um comando "Hive" e copiar e colar uma consulta na linha de comando do hive repl:

  ```console
  cd %hive_home%\bin
  hive
  ```

     Agora, na linha de comando REPL, cortar e colar a consulta a executa.
* **Salvando consultas em um arquivo e executando o comando** : a segunda é salvar as consultas em um arquivo '. HQL ' ( [exemplo&#95;hive&#95;criar&#95;criteo&#95;banco de dados&#95;e&#95;tabelas. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e, em seguida, emitir o seguinte comando para executar a consulta:

  ```console
  hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql
  ```

### <a name="confirm-database-and-table-creation"></a>Confirme a criação do banco de dados e da tabela
Em seguida, confirmamos a criação do banco de dados com o seguinte comando no prompt do diretório bin/ do Hive:

```console
hive -e "show databases;"
```

Isso fornece:

```output
criteo
default
Time taken: 1.25 seconds, Fetched: 2 row(s)
```

Isso confirma a criação do novo banco de dados, "criteo".

Para ver quais tabelas foram criadas, basta emitir o comando aqui no prompt do diretório bin/ do Hive:

```output
hive -e "show tables in criteo;"
```

Você então deve ver o seguinte resultado:

```ouput
criteo_count
criteo_test_day_22
criteo_test_day_23
criteo_train
Time taken: 1.437 seconds, Fetched: 4 row(s)
```

## <a name="data-exploration-in-hive"></a><a name="exploration"></a> Exploração de dados no Hive
Agora você está pronto para fazer uma exploração de dados básica no Hive. Comece contando o número de exemplos nas tabelas de dados de treinamento e teste.

### <a name="number-of-train-examples"></a>Número de exemplos de treinamento
O conteúdo de [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) é mostrado aqui:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_train;
```

Isso resulta em:

```output
192215183
Time taken: 264.154 seconds, Fetched: 1 row(s)
```

Também é possível emitir o seguinte comando no prompt do diretório bin/do Hive:

```console
hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql
```

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Número de exemplos de teste nos dois conjuntos de dados de teste
Agora conte o número de exemplos nos dois conjuntos de dados de teste. O conteúdo de [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) é mostrado aqui:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_22;
```

Isso resulta em:

```output
189747893
Time taken: 267.968 seconds, Fetched: 1 row(s)
```

Como de costume, você também pode chamar o script no prompt do diretório bin/ do Hive emitindo o comando:

```console
hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql
```

Por fim, examine o número de exemplos de teste do conjunto de dados de teste com base no dia\_23.

O comando para fazer isso é semelhante ao mostrado (consulte o [exemplo&#95;hive&#95;count&#95;criteo&#95;test&#95;Day&#95;23&#95;examples. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_23;
```

Isso fornece:

```output
178274637
Time taken: 253.089 seconds, Fetched: 1 row(s)
```

### <a name="label-distribution-in-the-train-dataset"></a>Distribuição de rótulos no conjunto de dados de treinamento
A distribuição de rótulos no conjunto de dados de treinamento é de interesse. Para ver isso, mostre o conteúdo de [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

```hiveql
SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;
```

Isso gera a distribuição de rótulos:

```output
1       6292903
0       185922280
Time taken: 459.435 seconds, Fetched: 2 row(s)
```

A porcentagem de rótulos positivos é de cerca de 3,3% (consistente com o conjunto de informações original).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuições de histograma de algumas variáveis numéricas no conjunto de dados de treinamento
Você pode usar a função nativa do Hive "histogram\_numeric" para descobrir como é a distribuição das variáveis numéricas. Eis o conteúdo de [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

```hiveql
SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
    (SELECT
    histogram_numeric(col2, 20) as col2_hist
    FROM
    criteo.criteo_train
    ) a
    LATERAL VIEW explode(col2_hist) exploded_table as hist;
```

Isso produz o seguinte:

```output
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
```

A combinação de EXIBIÇÃO LATERAL -explodir no Hive serve para produzir uma saída semelhante ao SQL em vez da lista comum. Nesta tabela, a primeira coluna corresponde ao centro do compartimento e a segunda à frequência de compartimento.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentis aproximados de algumas variáveis numéricas no conjunto de dados de treinamento
Outro aspecto que é de interesse com relação às variáveis numéricas é o cálculo de percentuais aproximados. O "percentile\_approx" nativo do Hive faz isso para nós. O conteúdo de [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) é:

```hiveql
SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;
```

Isso resulta em:

```output
1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
Time taken: 564.953 seconds, Fetched: 1 row(s)
```

A distribuição de percentis está fortemente relacionada à distribuição de histograma de qualquer variável numérica.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Encontre o número de valores exclusivos para algumas colunas categóricas no conjunto de dados de treinamento
Continuando a exploração de dados, encontre, para algumas colunas categóricas, o número de valores exclusivos que elas levam. Para fazer isso, mostre o conteúdo de [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

```hiveql
SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;
```

Isso resulta em:

```output
19011825
Time taken: 448.116 seconds, Fetched: 1 row(s)
```

Col15 tem em 19m valores exclusivos! Usar técnicas simples como "codificação one-hot" para codificar essas variáveis categóricas altamente dimensionais desse tipo é inviável. Em particular, foi explicada e demonstrada uma técnica eficaz e robusta chamada [Aprendizado com contagens](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) para lidar com esse problema de modo eficiente.

Por fim, examine o número de valores exclusivos de algumas outras colunas categóricas também. O conteúdo de [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) é:

```hiveql
SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
FROM criteo.criteo_train;
```

Isso resulta em:

```output
30935   15200   7349    20067   3
Time taken: 1933.883 seconds, Fetched: 1 row(s)
```

Mais uma vez, observe que, com exceção de Col20, todas as colunas têm muitos valores exclusivos.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Contagens de ocorrências conjuntas de pares de variáveis categóricas no conjunto de dados de treinamento

As distribuições de contagem de pares de variáveis categóricas também são de interesse. Isso pode ser determinado usando o código em [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

```hiveql
SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;
```

Reverta a ordem das contagens pela ocorrência e examine as 15 primeiras neste caso. Isso nos fornece:

```output
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
```

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a> Reduzir a resolução de conjuntos de dados para o Azure Machine Learning
Depois de ter explorado os conjuntos de dados e demonstrado como fazer esse tipo de exploração para quaisquer variáveis (incluindo combinações), reduza os conjuntos de dados para que seja possível criar modelos no Azure Machine Learning. Lembre-se de que o foco do problema é: dado um conjunto de atributos de exemplo (valores de recursos da Col2 à Col40), prever se Col1 é 0 (sem cliques) ou 1 (com clique).

Para reduzir os conjuntos de dados de treinamento e teste para 1% do tamanho original, use a função nativa RAND() do Hive. O seguinte script, [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) faz isso para o conjunto de dados de treinamento:

```hiveql
CREATE TABLE criteo.criteo_train_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

---Now downsample and store in this table

INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;
```

Isso resulta em:

```output
Time taken: 12.22 seconds
Time taken: 298.98 seconds
```

O script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) faz isso para dados de teste, dia\_22:

```hiveql
--- Now for test data (day_22)

CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;
```

Isso resulta em:

```output
Time taken: 1.22 seconds
Time taken: 317.66 seconds
```

Por fim, o script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) faz isso para dados de teste, dia\_23:

```hiveql
--- Finally test data day_23
CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;
```

Isso resulta em:

```output
Time taken: 1.86 seconds
Time taken: 300.02 seconds
```

Com isso, você está pronto para usar conjuntos de dados de treinamento e teste reduzidos para criar modelos no Azure Machine Learning.

Há um componente final importante antes de passar para o Azure Machine Learning, que se refere à tabela de contagem. Na próxima subseção, a tabela de contagem é discutida em alguns detalhes.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a> Uma breve discussão sobre a tabela de contagem
Como vimos, várias variáveis categóricas têm uma alta dimensionalidade. Em nosso passo a passo, apresentamos uma técnica eficaz chamada [Aprendizado com contagens](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) para codificar essas variáveis de maneira eficiente e robusta. Mais informações sobre essa técnica são disponibilizadas no link fornecido.

>[!NOTE]
>Neste passo a passo, o foco está no uso de tabelas de contagem para produzir representações compactas de recursos categóricos de grande dimensão. Essa não é a única maneira de codificar recursos categóricos. Para saber mais sobre outras técnicas, usuários interessados podem conferir [codificação one-hot](https://en.wikipedia.org/wiki/One-hot) e [hash de recursos](https://en.wikipedia.org/wiki/Feature_hashing).
>

Para criar tabelas de contagem nos dados de contagem, usamos os dados na pasta raw/count. Na seção de modelagem, é mostrado aos usuários como criar essas tabelas de contagem para recursos categóricos do zero ou como usar uma tabela de contagem criada previamente para suas explorações. A seguir, quando são mencionadas as "tabelas de contagem pré-criadas", estamos nos referindo às tabelas de contagem que foram fornecidas. Fornecemos instruções detalhadas sobre como acessar essas tabelas na próxima seção.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a> Criar um modelo com o Azure Machine Learning
Nosso processo de criação de modelo no Azure Machine Learning seguirá estas etapas:

1. [Obter os dados de tabelas do Hive no Azure Machine Learning](#step1)
2. [Criar o experimento: limpar os dados e torná-lo um recurso com tabelas de contagem](#step2)
3. [Criar, treinar e pontuar o modelo](#step3)
4. [Avaliar o modelo](#step4)
5. [Publicar o modelo como um serviço Web](#step5)

Agora você está pronto para criar modelos no Azure Machine Learning Studio. Nossos dados reduzidos são salvos como tabelas de Hive do cluster. Use o módulo **Importar Dados** do Azure Machine Learning para ler esses dados. As credenciais para acessar a conta de armazenamento deste cluster estão abaixo.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a> Etapa 1: passar dados de tabelas do Hive para o Azure Machine Learning usando o módulo Importar Dados e selecioná-los para um teste de aprendizado de máquina
Comece selecionando um experimento **+ novo**  ->  **experimento**  ->  **em branco**. Depois, na caixa **Pesquisar** na parte superior esquerda, procure "Importar Dados". Arraste e solte o módulo **Importar Dados** na tela do experimento (a parte central da tela) para usar o módulo para acesso a dados.

Esta é a aparência do **Importar Dados** ao obter dados da tabela do Hive:

![Importar Dados obtém os dados](./media/hive-criteo-walkthrough/i3zRaoj.png)

Para o módulo **Importar Dados** , os valores dos parâmetros que são fornecidos no gráfico são somente exemplos do tipo de valores que você precisará fornecer. Aqui estão algumas diretrizes gerais sobre como preencher o conjunto de parâmetros para o módulo **Importar Dados** .

1. Escolha "Consulta de Hive" para **Fonte de dados**
2. Na caixa **Consulta de banco de dados Hive** , um simples SELECT * FROM <your\_database\_name.your\_table\_name> é suficiente.
3. **URI do servidor Hcatalog** : se o cluster for "ABC", isso será simplesmente: https: \/ /ABC.azurehdinsight.net
4. **Nome da conta de usuário do Hadoop** : o nome de usuário escolhido no momento da programação do cluster. (NÃO o nome de usuário do Acesso Remoto!)
5. **Senha da conta de usuário do Hadoop** : a senha para o nome de usuário escolhido no momento da programação do cluster. (NÃO a senha de Acesso Remoto!)
6. **Local dos dados de saída** : escolha "Azure"
7. **Nome da conta de armazenamento do Azure** : a conta de armazenamento associada ao cluster
8. **Chave de conta de armazenamento do Azure** : a chave da conta de armazenamento associada ao cluster.
9. **Nome do contêiner do Azure** : se o nome do cluster for "abc", normalmente costuma ser apenas "abc".

Após **Importar Dados** terminar de obter os dados (você verá a marca de seleção verde no Módulo), salve os dados como um Conjunto de Dados (com um nome da sua preferência). A aparência é a seguinte:

![Importar Dados salva os dados](./media/hive-criteo-walkthrough/oxM73Np.png)

Clique com o botão direito do mouse na porta de saída do módulo **Importar Dados** . Isso revela uma opção **Salvar como conjunto de dados** e uma opção **Visualizar**. A opção **Visualizar** , se clicada, exibe 100 linhas de dados, juntamente com um painel à direita que é útil para ver algumas estatísticas resumidas. Para salvar os dados, basta selecionar **Salvar como conjunto de dados** e seguir as instruções.

Para selecionar o conjunto de dados salvo para uso em um teste de aprendizado de máquina, localize os conjuntos de dados usando a caixa **Pesquisa** mostrada abaixo. Em seguida, basta digitar parcialmente o nome do conjunto de dados para acessá-lo e arrastar o conjunto de dados para o painel principal. Soltá-lo no painel principal o seleciona para uso na modelagem do aprendizado de máquina.

![Arraste DataSet para o painel principal](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Faça isso para os conjuntos de dados de treinamento e de teste. Além disso, lembre-se de usar o nome do banco de dados e das tabelas que você atribuiu para essa finalidade. Os valores usados na figura são somente para fins de ilustração.\*\*
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a> Etapa 2: criar um experimento em Azure Machine Learning para prever cliques/não clique
Nosso experimento Azure Machine Learning Studio (clássico) é semelhante ao seguinte:

![Teste do Machine Learning](./media/hive-criteo-walkthrough/xRpVfrY.png)

Agora vamos examinar os principais componentes desse experimento. Arraste primeiro nossos conjuntos de testes de treinamento e teste salvos em nossa tela de experimento.

#### <a name="clean-missing-data"></a>Limpar dados ausentes
O módulo **Limpar dados ausentes** faz o que o nome diz: limpa dados ausentes de maneiras que podem ser especificadas pelo usuário. Examine este módulo para ver isto:

![Limpar dados ausentes](./media/hive-criteo-walkthrough/0ycXod6.png)

Aqui, escolha substituir todos os valores ausentes por um 0. Existem outras opções que podem ser vistas examinando os menus suspensos no módulo.

#### <a name="feature-engineering-on-the-data"></a>Engenharia de recurso nos dados
Pode haver milhões de valores exclusivos para alguns recursos categóricos de grandes conjuntos de dados. Usar métodos simples como codificação one-hot para representar recursos categóricos altamente dimensionais é totalmente impraticável. Este passo a passo demonstra como usar recursos de contagem usando módulos internos de Azure Machine Learning para gerar representações compactas dessas variáveis categóricas de grande dimensão. O resultado final é um tamanho de modelo menor, tempos de treinamento mais rápidos e métricas de desempenho que são comparáveis ao uso de outras técnicas.

##### <a name="building-counting-transforms"></a>Criando transformações de contagem
Para criar recursos de contagem, use o módulo **Criar transformação de contagem** disponível no Azure Machine Learning. O módulo tem esta aparência:

![Propriedades do módulo Criar Transformação de Contagem](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![Módulo Criar Transformação de Contagem](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Na caixa **Contar colunas** , insira as colunas em que você deseja realizar contagens. Geralmente, essas são (conforme mencionado) colunas categóricas altamente dimensionais. Lembre-se de que o conjunto de dados Criteo tem 26 colunas categóricas: da Col15 até a Col40. Aqui, contamos todas elas e apresentamos seus índices (de 15 a 40, separados por vírgulas, como mostrado).
>

Para usar o módulo no modo MapReduce (apropriado para grandes conjuntos de dados), precisamos de acesso a um cluster HDInsight Hadoop (aquele usado para a exploração de recurso também pode ser reutilizado para essa finalidade) e suas credenciais. Os valores anteriores ilustram a aparência dos valores preenchidos (substitua os valores fornecidos como ilustração por aqueles relevantes para seu próprio caso de uso).

![Parâmetros do módulo](./media/hive-criteo-walkthrough/05IqySf.png)

A figura anterior mostra como inserir o local de blob de entrada. Esse local tem os dados reservados para a criação de tabelas de contagem.

Após a execução desse módulo, salve a transformação para mais tarde clicando com o botão direito do mouse no módulo e selecionando a opção **Salvar como Transformação** :

![Opção “Salvar como transformação”](./media/hive-criteo-walkthrough/IcVgvHR.png)

Em nossa arquitetura de experimento mostrada acima, o conjunto de dados "ytransform2" corresponde exatamente a uma transformação de contagem salva. Para o restante desse experimento, presume-se que o leitor usou um módulo **Criar transformação de contagem** em alguns dados para gerar contagens e usá-las para gerar recursos de contagem em conjuntos de dados de treinamento e de teste.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Escolhendo quais recursos de contagem incluir como parte dos conjuntos de dados de treinamento e de teste
Assim que houver uma transformação de contagem pronta, o usuário poderá escolher quais recursos incluir nos seus conjuntos de dados de treinamento e de teste usando o módulo **Modificar parâmetros da tabela de contagem**. Para fins de integridade, esse módulo é mostrado aqui. Porém, para fins de simplicidade, não o usamos realmente em nosso experimento.

![Parâmetros Modificar Tabela de Contagem](./media/hive-criteo-walkthrough/PfCHkVg.png)

Nesse caso, como pode ser visto, as chances de log devem ser usadas e a coluna de recuo é ignorada. Você também pode definir parâmetros, como o limite da lixeira, quantos pseudoexemplos anteriores adicionar para suavização e se o ruído Laplaciano deve ser usado ou não. Todos esses são recursos avançados e deve-se observar que os valores padrão são um bom ponto de partida para usuários não familiarizados com esse tipo de geração de recurso.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformação de dados antes de gerar os recursos de contagem
Agora o foco está em um ponto importante sobre como transformar nossos dados de treinamento e de teste antes de realmente gerar recursos de contagem. Há dois módulos **Executar script R** usados antes que a transformação contagem seja aplicada aos nossos dados.

![Executar Script R](./media/hive-criteo-walkthrough/aF59wbc.png)

Veja o primeiro script R:

![Primeiro script R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Esse script R renomeia as colunas com nomes "Col1" para "Col40". Isso ocorre porque a transformação de contagem espera nomes nesse formato.

O segundo script R equilibra a distribuição entre classes positivas e negativas (classes 1 e 0, respectivamente) reduzindo a amostragem da classe negativa. O script R aqui mostra como fazer isso:

![Segundo script R](./media/hive-criteo-walkthrough/91wvcwN.png)

Neste script R simples, usamos "pos\_neg\_ratio" para definir a quantidade de equilíbrio entre as classes positiva e negativa. É importante fazer isso, já que melhorar o desequilíbrio de classe normalmente tem benefícios de desempenho para problemas de classificação em que a distribuição de classe está distorcida (lembre-se de que, neste caso, você tem 3,3% de classes positivas e 96,7% de negativas).

##### <a name="applying-the-count-transformation-on-our-data"></a>Aplicando a transformação de contagem aos nossos dados
Por fim, você pode usar o módulo **Aplicar Transformação** para aplicar as transformações de contagem aos conjuntos de dados de treino e de teste. Este módulo usa a transformação da contagem salva como uma entrada e os conjuntos de dados de treinamento ou de teste como outra entrada e retorna dados com recursos de contagem. Isso é mostrado aqui:

![Aplicar o módulo de transformação](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Um trecho de como os recursos de contagem se parecem
É instrutivo observar a aparência dos recursos de contagem em nosso caso. Aqui está um trecho disto:

![Recursos de contagem](./media/hive-criteo-walkthrough/FO1nNfw.png)

Esse trecho mostra que, para as colunas contadas, você recebe as chances de log e contagens, além de quaisquer retiradas relevantes.

Você agora está pronto para criar um modelo de Azure Machine Learning usando esses conjuntos de dados transformados. A próxima seção mostra como isso pode ser feito.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a>Etapa 3: Criar, treinar e pontuar o modelo

#### <a name="choice-of-learner"></a>Opção do aprendiz
Primeiro, você precisa escolher um aprendiz. Use uma árvore de decisão aprimorada de duas classes como nosso aprendiz. Aqui estão as opções padrão para esse aprendiz:

![Parâmetros da árvore de decisão aumentada de duas classes](./media/hive-criteo-walkthrough/bH3ST2z.png)

Para o teste, escolha os valores padrão. Os padrões são significativos e uma boa maneira de obter linhas de base rápidas sobre o desempenho. Se desejar, depois de ter uma linha de base, você pode melhorar o desempenho varrendo parâmetros.

#### <a name="train-the-model"></a>Treinar o modelo
Para treinamento, podemos simplesmente invocar um módulo **Treinar Modelo**. As duas entradas para ele são o aprendiz Árvore de Decisão Aumentada de Duas Classes e nosso conjunto de dados de treinamento. Isso é mostrado aqui:

![Módulo Treinar Modelo](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Pontuar o modelo
Assim que você tiver um modelo treinado, estará pronto para avaliar o conjunto de dados de teste e seu desempenho. Faça isso usando o módulo **Modelo de Pontuação** mostrado na figura a seguir, junto com um módulo **Modelo de Avaliação** :

![Módulo de Modelo de Pontuação](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a> Etapa 4: Avaliar o modelo
Por fim, você deve analisar o desempenho do modelo. Normalmente, para problemas de classificação de duas classes (binária), uma boa medida é o AUC. Para visualizar essa curva, conecte o módulo **modelo de Pontuação** a um módulo modelo de **avaliação** . Clicar em **Visualizar** no módulo **Modelo de avaliação** produz um gráfico semelhante ao seguinte exemplo:

![Avaliar o modelo de BDT do módulo](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Em problemas de classificação binária (ou de duas classes), uma boa medida da precisão de previsão é o AUC (área abaixo da curva). A seção a seguir mostra os resultados usando esse modelo em nosso conjunto de dados de teste. Clique com o botão direito do mouse na porta de saída do módulo **modelo de avaliação** e, em seguida, **visualize**.

![Módulo Visualizar modelo de avaliação](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a> Etapa 5: Publicar o modelo como um serviço Web a ser consumido
A capacidade de publicar um modelo de Azure Machine Learning como serviços Web com um nível mínimo de confusão é um recurso valioso para torná-lo amplamente disponível. Depois disso, qualquer pessoa pode fazer chamadas para o serviço Web com dados de entrada para os quais precisem de previsões, e o serviço Web usa o modelo para retornar as previsões.

Primeiro, salve nosso modelo treinado como um objeto de modelo treinado clicando com o botão direito do mouse no módulo **modelo de treinamento** e usando a opção **salvar como modelo treinado** .

Em seguida, crie portas de entrada e saída para o serviço Web:

* uma porta de entrada usa dados da mesma forma que os dados para os quais você precisa de previsões
* uma porta de saída retorna os Rótulos Pontuados e as probabilidades associadas.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecionar algumas linhas de dados para a porta de entrada
Podemos, de maneira conveniente, usar uma **Transformação do tipo Apply SQL** para selecionar apenas 10 linhas para servirem como dados de porta de entrada. Selecione somente essas linhas de dados para a nossa porta de entrada usando a consulta SQL mostrada aqui:

![Dados na porta de entrada](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Serviço Web
Agora você está pronto para executar um pequeno experimento que pode ser usado para publicar nosso serviço Web.

#### <a name="generate-input-data-for-webservice"></a>Gerar dados de entrada para o serviço Web
Como etapa inicial, considerando que a tabela de contagem é grande, pegue algumas linhas de dados de teste e gere dados de saída delas com recursos de contagem. Essa saída pode servir como o formato de dados de entrada para nosso WebService, como mostrado aqui:

![Criar dados de entrada de BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Para o formato de dados de entrada, usamos a SAÍDA do módulo **Recursos de Contagem**. Após o fim da execução do experimento, nós salvamos a saída do módulo **Recursos de contagem** como um conjunto de dados. Esse conjunto de dados é usado para os dados de entrada no serviço Web.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Experimento de pontuação publicação do serviço Web
Primeiro, a estrutura essencial é um módulo de **modelo de Pontuação** que aceita nosso objeto de modelo treinado e algumas linhas de dados de entrada que foram geradas nas etapas anteriores usando o módulo **Featurizer de contagem** . Use "Selecionar Colunas do Conjunto de Dados" para projetar os Rótulos pontuados e as Probabilidades de pontuação.

![Projetar Colunas no Conjunto de Dados](./media/hive-criteo-walkthrough/kRHrIbe.png)

Observe como o módulo **Projetar Colunas no Conjunto de Dados** pode ser usado para “filtrar” dados de um conjunto de dados. O conteúdo é mostrado aqui:

![Filtragem com Selecionar Colunas no módulo Conjunto de Dados](./media/hive-criteo-walkthrough/oVUJC9K.png)

Para obter as portas de entrada e de saída azuis, basta clicar em **preparar serviço Web** no canto inferior direito. Executar esse experimento também nos permite publicar o serviço Web clicando no ícone **PUBLICAR SERVIÇO WEB** no canto inferior direito, mostrado aqui:

![PUBLICAR SERVIÇO WEB](./media/hive-criteo-walkthrough/WO0nens.png)

Após o serviço Web ser publicado, somos redirecionados a uma página parecida com esta:

![Painel de serviço Web](./media/hive-criteo-walkthrough/YKzxAA5.png)

Vemos dois links para serviços Web no lado esquerdo:

* O RRS (ou Serviço de **SOLICITAÇÃO/RESPOSTA** ) é destinado a previsões únicas e é o que estamos neste workshop.
* O serviço **EXECUÇÃO EM LOTES** (BES) é usado para previsões em lotes e exige que os dados de entrada usados para fazer previsões residam no Armazenamento de Blob do Azure.

Clicar no link **SOLICITAÇÃO/RESPOSTA** nos leva a uma página que nos fornece um código gravado previamente em C#, python e R. Esse código pode ser usado de modo conveniente para fazer chamadas para o serviço Web. A chave de API nesta página precisa ser usada para autenticação.

É conveniente copiar esse código python para uma nova célula no bloco de anotações IPython.

Aqui está um segmento de código Python com a chave de API correta.

![Código Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

A chave de API padrão foi substituída pela chave de API do WebService. Clicar em **Executar** nessa célula em um bloco de anotações IPython produz a seguinte resposta:

![Resposta do IPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Para os dois exemplos de teste solicitados na estrutura JSON do script Python, você obtém respostas no formato "rótulos pontuados, probabilidades pontuadas". Neste caso, foram escolhidos os valores padrão que o código predefinido fornece (0 para todas as colunas numéricas e a cadeia de caracteres "value" para todas as colunas categóricas).

Concluindo, nosso passo a passos mostra como lidar com conjuntos de grandes escalas usando Azure Machine Learning. Você começou com um terabyte de dados, construiu um modelo de previsão e o implantou como um serviço Web na nuvem.