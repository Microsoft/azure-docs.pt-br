---
title: Criar e implantar um modelo em uma VM do SQL Server – Processo de Ciência de Dados de Equipe
description: Criar e implantar um modelo de machine learning usando o SQL Server em uma VM do Azure com um conjunto de dados disponível publicamente.
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
ms.openlocfilehash: 580181aaaea975ee07bcec8108297079c5373b92
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007402"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>O Processo de Ciência de Dados de Equipe em ação: usando o SQL Server
Neste tutorial, você obtém um passo a passo sobre como criar e implantar um modelo de aprendizado de máquina usando o SQL Server e um conjunto de dados disponível publicamente – [Corridas de Táxi em NYC](https://www.andresmh.com/nyctaxitrips/). O procedimento segue um fluxo de trabalho de ciência de dados padrão: ingerir e explorar os dados, projetar recursos para facilitar o aprendizado e, em seguida, criar e implantar um modelo.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Descrição do conjunto de NYC de viagens de táxi
Os dados de corrida de táxi de NYC são cerca de 20 GB de arquivos CSV compactados (aproximadamente 48 GB descompactados), que abrangem mais de 173 milhões viagens individuais e as tarifas pagas por cada viagem. Cada registro de viagem inclui o local e a hora da coleta e da retirada, o número de licença anônimo do hack (motorista) e o número do medalhão (identificação exclusiva do táxi). Os dados abrangem todas as corridas no ano de 2013 e são fornecidos nos dois conjuntos de dados a seguir para cada mês:

1. O CSV 'trip_data' contém detalhes da corrida, como o número de passageiros, pontos de saída e chegada, duração e quilometragem da corrida. Aqui estão alguns exemplos de registros:
   
    `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. O CSV 'trip_fare' contém detalhes sobre as tarifas pagas em cada corrida, como tipo de pagamento, valor da tarifa, custos adicionais e impostos, gorjetas e pedágios, e o valor total pago. Aqui estão alguns exemplos de registros:
   
    `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

A chave exclusiva para unir trip\_data e trip\_fare é composta pelos campos: medallion, hack\_license e pickup\_datetime.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exemplos de tarefas de previsão
Formularemos três problemas de previsão com base em *tip\_amount*, sendo eles:

* Classificação binária: preveja se uma gorjeta foi paga ou não por uma corrida, ou seja, *um \_ valor Tip* maior que $0 é um exemplo positivo, enquanto um *\_ valor Tip* de $0 é um exemplo negativo.
* Classificação multiclasse: prever o intervalo da gorjetas pagas pela corrida. Dividimos *tip\_amount* em cinco compartimentos ou classes:

   `Class 0 : tip_amount = $0`

   `Class 1 : tip_amount > $0 and tip_amount <= $5`

   `Class 2 : tip_amount > $5 and tip_amount <= $10`

   `Class 3 : tip_amount > $10 and tip_amount <= $20`

   `Class 4 : tip_amount > $20`

* Tarefa de regressão: prever o valor da gorjeta paga por uma corrida.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Configurando o ambiente de ciência de dados do Azure para análise avançada
Como indicado no guia [Planejar seu ambiente](plan-your-environment.md) , há várias opções para trabalhar com o conjunto de dados de Corridas de Táxi em NYC no Azure:

* Trabalhar com os dados em blobs do Azure e depois modelá-los no Azure Machine Learning
* Carregar os dados para um banco de dados SQL Server e depois modelá-los no Azure Machine Learning

Neste tutorial, demonstraremos a importação em massa paralela dos dados para um SQL Server, exploração de dados, engenharia de recursos e amostragem vertical usando SQL Server Management Studio, bem como usando o notebook IPython. [Scripts de exemplo](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [notebooks IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) são compartilhados no GitHub. Um IPython Notebook de exemplo para trabalhar com dados em blobs do Azure também está disponível no mesmo local.

Para configurar seu ambiente de Ciência de Dados do Azure:

1. [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md)
2. [Criar um workspace de Azure Machine Learning](../classic/create-workspace.md)
3. [Provisione uma Máquina Virtual de Ciência de Dados](../data-science-virtual-machine/overview.md), que fornece um SQL Server e um servidor do IPython Notebook.
   
   > [!NOTE]
   > Os scripts de exemplo e IPython Notebook serão baixados para a máquina virtual de Ciência de Dados durante o processo de instalação. Quando o script de pós-instalação da VM for concluído, os exemplos estarão na biblioteca de documentos da VM:  
   > 
   > * Scripts de exemplo: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Exemplo de IPython Notebooks: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   where `<user_name>` é o nome de logon do Windows da VM. Vamos nos referir às pastas de exemplo como **Scripts de Exemplo** e **Notebook IPython de Exemplo**.
   > 
   > 

Dependendo do tamanho do conjunto de dados, do local da fonte de dados e do ambiente de destino do Azure selecionado, esse cenário será semelhante ao [Cenário \#5: Conjunto de dados grande em arquivos locais, SQL Server de destino na VM do Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Obter os dados de fonte pública
Para obter o conjunto de dados [Corridas de Táxi em NYC](https://www.andresmh.com/nyctaxitrips/) do seu local público, você pode usar qualquer um dos métodos descritos em [Mover dados bidirecionalmente no Armazenamento de Blobs do Azure](move-azure-blob.md) para copiar os dados em sua nova máquina virtual.

Para copiar os dados usando AzCopy:

1. Faça logon na sua VM (máquina virtual)
2. Crie um novo diretório no disco de dados da VM (Observação: não use o disco temporário fornecido com a VM como um disco de dados).
3. Em uma janela de Prompt de Comando, execute a seguinte linha de comando Azcopy, substituindo <path_to_data_folder> pela pasta de dados criada em (2):

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

    Quando o AzCopy for concluído, um total de 24 arquivos CSV zipados (12 de trip\_data e 12 de trip\_fare) devem estar na pasta de dados.
4. Descompacte os arquivos baixados. Observe a pasta em que os arquivos descompactados estão. Essa pasta será mencionada como <path\_to\_data\_files\>>.

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Importação de dados em massa para o Banco de Dados do SQL Server
O desempenho de carregar/transferir grandes quantidades de dados para um banco de dados SQL e consultas subsequentes pode ser melhorado usando *tabelas e exibições particionadas*. Nesta seção, seguiremos as instruções descritas em [Importação de dados em massa paralela usando tabelas de partição do SQL](parallel-load-sql-partitioned-tables.md) para criar um novo banco de dados e carregar os dados em tabelas particionadas paralelas.

1. Após conectar à sua VM, inicie o **SQL Server Management Studio**.
2. Conecte-se usando a Autenticação do Windows.
   
    ![Conexão SSMS][12]
3. Se você ainda não tiver alterado o modo de autenticação do SQL Server e criado um novo usuário de logon do SQL, abra o arquivo de script chamado **change\_auth.sql** na pasta **Scripts de Exemplo**. Altere o nome de usuário padrão e a senha. Clique em **executar** na barra de ferramentas para executar o script.
   
    ![Executar Script][13]
4. Verifique e/ou altere o banco de dados padrão do SQL Server e pastas de log padrão para garantir que bancos de dados recém-criados serão armazenadas em um Disco de Dados. A imagem de VM SQL Server que é otimizada para cargas de armazenamento de dados é pré-configurada com discos de dados e de log. Se sua VM não incluía um Disco de Dados e você adicionou novos discos rígidos virtuais durante o processo de configuração da VM, altere as pastas padrão da seguinte maneira:
   
   * Clique com o botão direito do mouse no nome do SQL Server no painel esquerdo e clique em **Propriedades**.
     
       ![Propriedades do SQL Server][14]
   * Selecione **Configurações de Banco de Dados** na lista **Selecionar uma página** à esquerda.
   * Verifique e/ou altere os **Locais padrão de banco de dados** para os locais de **Disco de Dados** de sua escolha. Esse local é onde os novos bancos de dados residem se criados com as configurações padrão.
     
       ![Padrões de banco de dados SQL][15]  
5. Para criar um novo banco de dados e um conjunto de grupos de arquivos para conter as tabelas particionadas, abra o script de exemplo **create\_db\_default.sql**. O script criará um novo banco de dados denominado **TaxiNYC** e 12 grupos de arquivos no local de dados padrão. Cada grupo de arquivos conterá um mês de dados trip\_data e trip\_fare. Modifique o nome do banco de dados, se desejado. Clique em **executar** para executar o script.
6. Em seguida, crie duas tabelas de partição, uma para trip\_data e outra para trip\_fare. Abra o script de exemplo **create\_partitioned\_table.sql**, que vai:
   
   * Criar uma função de partição para dividir os dados por mês.
   * Criar um esquema de partição para mapear dados de cada mês para outro grupo de arquivos.
   * Crie duas tabelas de partição mapeadas para o esquema de partição: **nyctaxi\_trip** manterá os dados de trip\_data e **nyctaxi\_fare** os dados de trip\_fare.
     
     Clique em **executar** para executar o script e criar as tabelas particionadas.
7. Na pasta **Scripts de Exemplo** , há dois exemplos de scripts de PowerShell fornecidos para demonstrar importações paralelas de dados em massa para tabelas do SQL Server.
   
   * **bcp\_parallel\_generic.ps1** é um script genérico para importação paralela de dados em massa em uma tabela. Modifique esse script para definir as variáveis de entrada e de destino, conforme indicado nas linhas de comentário no script.
   * **bcp\_parallel\_nyctaxi.ps1** é uma versão pré-configurada do script genérico e pode ser usada para carregar ambas as tabelas dos dados das Corridas de Taxi de NYC.  
8. Clique com o botão direito no nome do script **bcp\_parallel\_nyctaxi.ps1** e clique em **Editar** para abri-lo no PowerShell. Examine as variáveis predefinidas e modifique-as de acordo com o nome do banco de dados selecionado, a pasta de dados de entrada, a pasta de log de destino e os caminhos para os arquivos de formato de exemplo **nyctaxi_trip.xml** e **nyctaxi\_fare.xml** (fornecidos na pasta **Scripts de Exemplo**).
   
    ![Importação em massa de dados][16]
   
    Você também pode selecionar o modo de autenticação, o padrão é a Autenticação do Windows. Clique na seta verde na barra de ferramentas para executar. O script iniciará 24 operações de importação em massa paralelas, 12 para cada tabela particionada. Você pode monitorar o progresso da importação de dados abrindo a pasta de dados padrão do SQL Server conforme definido acima.
9. O script do PowerShell informa o início e término. Quando todas as importações em massa forem concluídas, a hora de término é relatada. Verifique a pasta de log de destino para verificar se as importações em massa foram bem-sucedidas, ou seja, se não há erros relatados na pasta de log de destino.
10. O banco de dados agora está pronto para exploração, engenharia de recursos e outras operações conforme desejado. Como as tabelas são particionadas de acordo com o campo de **\_ data de retirada** , as consultas que incluem condições de **retirada de \_ data/hora** na cláusula **Where** se beneficiarão do esquema de partição.
11. No **SQL Server Management Studio**, explore o script de exemplo fornecido **sample\_queries.sql**. Para executar qualquer uma das consultas de exemplo, realce as linhas de consulta e clique em **executar** na barra de ferramentas.
12. Os dados de Corridas de Táxi em NYC são carregados em duas tabelas separadas. Para melhorar as operações de associação, é altamente recomendável indexá-las. O exemplo de script **create\_partitioned\_index.sql** cria índices particionados na chave de associação composta **medallion, hack\_license e pickup\_datetime**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Exploração de dados e engenharia de recursos no SQL Server
Nesta seção, realizaremos exploração de dados e geração de recursos executando consultas SQL diretamente no **SQL Server Management Studio** usando o banco de dados do SQL Server criado anteriormente. Um script de exemplo chamado **sample\_queries.sql** é fornecido na pasta **Scripts de Exemplo**. Modifique o script para alterar o nome do banco de dados, se for diferente do padrão: **TaxiNYC**.

Neste exercício, você vai:

* Conectar-se ao **SQL Server Management Studio** usando a Autenticação do Windows ou Autenticação do SQL e o nome de logon e senha do SQL.
* Explorar as distribuições de dados de alguns campos em períodos diferentes.
* Investigar a qualidade dos dados dos campos de longitude e latitude.
* Gerar rótulos de classificação binária e multiclasse com base em **tip\_amount**.
* Gerar recursos e computar/comparar as distâncias de viagem.
* Unir as duas tabelas e extrair uma amostra aleatória que será usada para compilar modelos.

Quando você estiver pronto para prosseguir para o Azure Machine Learning, você pode:  

1. Salve a consulta SQL final para extrair e obter amostras dos dados e copiar e colar a consulta diretamente em um módulo [importar dados][import-data] no Azure Machine Learning ou
2. Mantenha os dados de exemplo projetados que você planeja usar para criar modelos em uma nova tabela de banco de dados e use a nova tabela no módulo [Importar Dados][import-data] no Azure Machine Learning.

Nesta seção, salvaremos a consulta final para extrair e obter amostras dos dados. O segundo método é demonstrado na seção [Exploração de dados e engenharia de recursos no IPython Notebook](#ipnb) .

Para uma verificação rápida do número de linhas e colunas nas tabelas preenchidas anteriormente usando a importação em massa paralela,

- Relatar o número de linhas na tabela nyctaxi_trip sem verificação de tabela: `SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')`
- Relatar o número de colunas na tabela nyctaxi_trip: `SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'`

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: distribuição de corridas por licença
Este exemplo identifica o medalhão (número do táxi) com mais de 100 corridas dentro de um determinado período de tempo. A consulta aproveitaria o acesso à tabela particionada, já que é condicionada pelo esquema de partição de **pickup\_datetime**. Consultar o conjunto de dados completo também usará a tabela particionada e/ou a verificação de índice.

```sql
SELECT medallion, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploração: distribuição de corridas por medallion e hack_license

```sql
SELECT medallion, hack_license, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: verificar registros com longitude e/ou latitude incorretos
Este exemplo investiga se qualquer um dos campos longitude e/ou latitude contém um valor inválido (graus radianos devem estar entre -90 e 90), ou tiver coordenadas (0, 0).

```sql
SELECT COUNT(*) FROM nyctaxi_trip
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: distribuição de corridas com gorjeta versus sem gorjeta
Este exemplo localiza o número de corridas que receberam gorjetas em comparação com aquelas que não receberam em um determinado período de tempo (ou no conjunto de dados completo se abrangendo todo o ano). Essa distribuição reflete a distribuição de rótulo binário a ser usado posteriormente para modelagem de classificação binária.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM nyctaxi_fare
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

#### <a name="exploration-tip-classrange-distribution"></a>Exploração: distribuição de classe/intervalo de gorjetas
Esse exemplo calcula a distribuição dos intervalos de gorjetas em um determinado período de tempo (ou no conjunto de dados completo se abrangendo todo o ano). Essa distribuição das classes de rótulo será usada posteriormente para modelagem de classificação multiclasse.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: calcular e comparar distância de viagem
Este exemplo converte a longitude e a latitude de pickup e dropoff em pontos de geografia SQL, calcula a distância da viagem usando a diferença de pontos de geografia SQL e retorna uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados às coordenadas válidas apenas usando a consulta de avaliação de qualidade de dados abordada anteriormente.

```sql
SELECT
pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
,trip_distance
,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
FROM nyctaxi_trip
tablesample(0.01 percent)
WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

#### <a name="feature-engineering-in-sql-queries"></a>Engenharia de recursos em consultas SQL
As consultas de exploração de geração de rótulos e conversão de geografia também podem ser usadas para gerar rótulos/recursos removendo a parte da contagem. Exemplos de engenharia de recursos SQL adicionais são fornecidos na seção [Exploração de dados e engenharia de recursos no IPython Notebook](#ipnb) . É mais eficiente executar as consultas de geração de recursos no conjunto de dados completo ou em um grande subconjunto dela usando consultas SQL que são executadas diretamente na instância do banco de dados SQL Server. As consultas podem ser executadas em **SQL Server Management Studio**, Notebook ipython ou qualquer ferramenta de desenvolvimento ou ambiente que possa acessar o banco de dados localmente ou remotamente.

#### <a name="preparing-data-for-model-building"></a>Preparando dados para criação de modelo
A consulta a seguir une as tabelas **nyctaxi\_trip** e **nyctaxi\_fare**, gera um rótulo de classificação binária **tipped**, um rótulo de classificação de multiclasse **tip\_class** e extrai uma amostra aleatória de 1% do conjunto de dados totalmente unido. Essa consulta pode ser copiada e colada diretamente no módulo [Azure Machine Learning Studio](https://studio.azureml.net) [importar dados][import-data] para a ingestão direta de dados da instância de banco de SQL Server no Azure. A consulta exclui registros com coordenadas incorretas (0, 0).

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_trip t, nyctaxi_fare f
TABLESAMPLE (1 percent)
WHERE t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Exploração de dados e engenharia de recursos no IPython Notebook
Nesta seção, realizaremos a exploração de dados e a geração de recursos executando consultas SQL e Python no banco de dados do SQL Server criado anteriormente. Um IPython Notebook de exemplo chamado **machine-Learning-data-science-process-sql-story.ipynb** é fornecido na pasta **Notebook IPython de Exemplo**. Este caderno também está disponível no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Ao trabalhar com Big Data, siga esta sequência recomendada:

* Leia em uma pequena amostra dos dados em um quadro de dados na memória.
* Execute algumas visualizações e explorações usando os dados de amostrados.
* Experimente a engenharia de recursos usando os dados amostrados.
* Para exploração de dados maiores, manipulação de dados e engenharia de recursos, use o Python para emitir consultas SQL diretamente no banco de dados do SQL Server na VM do Azure.
* Decida o tamanho da amostra a ser usado para criação de modelo do Azure Machine Learning.

Quando estiver pronto para prosseguir para o Azure Machine Learning, você pode:  

1. Salve a consulta SQL final para extrair e obter amostras dos dados e copiar e colar a consulta diretamente em um módulo [importar dados][import-data] no Azure Machine Learning. Esse método é demonstrado na seção [Criando modelos no Azure Machine Learning](#mlmodel) .    
2. Mantenha os dados de exemplo projetados que você planeja usar para criar modelos em uma nova tabela de banco de dados e use a nova tabela no módulo do [Importar Dados][import-data].

A seguir estão alguns exemplos de exploração de dados, visualização de dados e engenharia de recursos. Para obter mais exemplos, consulte o IPython Notebook SQL de exemplo na pasta **IPython Notebook de Exemplo** .

#### <a name="initialize-database-credentials"></a>Inicializar as credenciais de banco de dados
Inicialize as configurações de conexão de banco de dados nas seguintes variáveis:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database server>
```

#### <a name="create-database-connection"></a>Criar conexão de banco de dados

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Relatar o número de linhas e colunas na tabela nyctaxi_trip

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('nyctaxi_trip')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('nyctaxi_trip')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Número total de linhas = 173179759  
* Número total de colunas = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Leitura de uma pequena amostra de dados do Banco de Dados do SQL Server

```sql
t0 = time.time()

query = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (0.05 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

O tempo para ler a tabela de exemplo é 6,492000 segundos  
Número de linhas e colunas recuperadas = (84952, 21)

#### <a name="descriptive-statistics"></a>Estatísticas Descritivas
Agora está tudo pronto para explorar os dados amostrados. Começamos observando as estatísticas descritivas para o campo **trip\_distance** (ou qualquer outro):

```sql
df1['trip_distance'].describe()
```

#### <a name="visualization-box-plot-example"></a>Visualização: exemplo de plotagem da caixa
Em seguida, analisamos a caixa para a distância de viagem para visualizar os quantis

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Plotar nº 1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualização: exemplo de plotagem de distribuição

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Plotar nº 2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualização: plotagens de barra e linha
Neste exemplo, podemos compartimentalizar a distância da viagem em cinco compartimentos e visualizar os resultados de compartimentalização.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Podemos plotar a distribuição de compartimentos acima em um gráfico de barras ou linhas abaixo

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Plotar nº 3][3]

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```
![Plotar nº 4][4]

#### <a name="visualization-scatterplot-example"></a>Visualização: exemplo de plotagem de dispersão
Mostramos o gráfico de dispersão entre **trip\_time\_in\_secs** e **trip\_distance** para ver se há alguma correlação

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Plotar nº 6][6]

Da mesma forma, é possível verificar a relação entre **rate\_code** e **trip\_distance**.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Plotar nº 8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Redução de amostragem dos dados no SQL
Ao preparar os dados para a criação de modelo no [Azure Machine Learning Studio](https://studio.azureml.net), você pode decidir se a **consulta SQL deve ser usada diretamente no módulo Importar Dados** ou se mantém os dados de exemplo projetados em uma nova tabela, que pode se usada no módulo [Importar Dados][import-data] com um simples **SELECT * FROM <nome\_da\_nova\_tabela>**.

Nesta seção, criaremos uma nova tabela para manter os dados de amostra e de engenharia. Um exemplo de uma consulta direta de SQL para criação de modelos é fornecido na seção [Exploração de dados e engenharia de recursos no SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Criar uma tabela de exemplo e preenchê-la com 1% das tabelas unidas. Descartar a tabela primeiro se ela existir.
Nesta seção, unimos as tabelas **nyctaxi\_trip** e **nyctaxi\_fare**, extraímos uma amostra aleatória de 1% e mantemos os dados de amostra em uma nova tabela chamada **nyctaxi\_one\_percent**:

```sql
cursor = conn.cursor()

drop_table_if_exists = '''
    IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
'''

nyctaxi_one_percent_insert = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
    INTO nyctaxi_one_percent
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
'''

cursor.execute(drop_table_if_exists)
cursor.execute(nyctaxi_one_percent_insert)
cursor.commit()
```

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploração de dados usando consultas SQL em IPython Notebook
Nesta seção, exploraremos distribuições de dados usando os dados de amostra 1% que são persistidos na nova tabela que criamos acima. Explorações semelhantes podem ser executadas usando as tabelas originais, opcionalmente usando **TABLESAMPLE** para limitar o exemplo de exploração ou limitando os resultados a um determinado período de tempo usando as partições de **retirada \_ DateTime** , conforme ilustrado na seção [exploração de dados e de recursos na SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: distribuição diária de corridas

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: distribuição de corridas por licença

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Geração de recursos usando consultas SQL no IPython Notebook
Nesta seção, geraremos novos rótulos e recursos usando consultas SQL de forma direta, operando em uma tabela de amostra de 1% criada na seção anterior.

#### <a name="label-generation-generate-class-labels"></a>Geração de rótulo: gerar rótulos de classe
No exemplo a seguir, geramos dois conjuntos de rótulos a serem utilizados para modelagem:

1. Rótulos de classe binária **tipped** (prevendo se uma gorjeta será fornecida)
2. Rótulos multiclasse **tip\_class** (prevendo o compartimento ou intervalo da gorjeta)

```sql   
    nyctaxi_one_percent_add_col = '''
        ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
    '''
   
    cursor.execute(nyctaxi_one_percent_add_col)
    cursor.commit()
   
    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET
           tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
           tip_class = CASE WHEN (tip_amount = 0) THEN 0
                            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                            ELSE 4
                        END
    '''
   
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()
```

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Engenharia de recurso: recursos de contagem de colunas categóricas
Este exemplo transforma um campo de categoria em um campo numérico, substituindo cada categoria pela contagem de suas ocorrências nos dados.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B AS
    (
        SELECT medallion, hack_license,
            SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
            SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
        FROM nyctaxi_one_percent
        GROUP BY medallion, hack_license
    )

    UPDATE nyctaxi_one_percent
    SET nyctaxi_one_percent.cmt_count = B.cmt_count,
        nyctaxi_one_percent.vts_count = B.vts_count
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion AND A.hack_license = B.hack_license
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Engenharia de recurso: recursos de compartimento para colunas numéricas
Este exemplo transforma um campo numérico contínuo em intervalos de categorias predefinidos, ou seja, transforma o campo numérico em um campo categórico.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
    (
        SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
        NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
    )

    UPDATE nyctaxi_one_percent
    SET trip_time_bin = B.BinNumber
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion
    AND A.hack_license = B.hack_license
    AND A.pickup_datetime = B.pickup_datetime
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Recurso de engenharia: extrair recursos de local de latitude/longitude decimal
Este exemplo divide a representação decimal de um campo de latitude e/ou longitude em vários campos de região de granularidade diferente, como país/região, cidade, cidade, bloco, etc. Os novos campos geográficos não são mapeados para locais reais. Para saber mais sobre o mapeamento de locais de codificação geográfica, veja [Serviços REST do Bing Mapas](/bingmaps/rest-services/locations/find-a-location-by-point).

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent
    ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
        l5 varchar(3), l6 varchar(3), l7 varchar(3)
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    UPDATE nyctaxi_one_percent
    SET l1=round(pickup_longitude,0)
        , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
        , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
        , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
        , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
        , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
        , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verificar a forma final da tabela apresentada

```sql
query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
pd.read_sql(query,conn)
```

Agora estamos prontos para continuar a criação de modelos e implantação de modelo no [Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos para qualquer problemas de previsão identificados anteriormente, ou seja:

1. Classificação binária: prever se uma gorjeta foi ou não paga em uma corrida.
2. Classificação multiclasse: prever o intervalo da gorjeta paga, de acordo com as classes definidas anteriormente.
3. Tarefa de regressão: prever o valor da gorjeta paga por uma corrida.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Criando modelos no Azure Machine Learning
Para iniciar o exercício de modelagem, faça logon no seu workspace do Azure Machine Learning. Se ainda não tiver criado um workspace do Machine Learning, consulte [Criar um workspace do Azure Machine Learning](../classic/create-workspace.md).

1. Para ver os primeiros passos no Azure Machine Learning, consulte [O que é o Azure Machine Learning Studio?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net).
3. A página inicial do Estúdio fornece uma grande quantidade de informações, vídeos, tutoriais e links para a Referência de Módulos e outros recursos. Para saber mais sobre o Azure Machine Learning, confira o [Centro de Documentação do Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Um teste de treinamento típico é formado pelas seguintes etapas:

1. Crie um experimento **+ novo** .
2. Transmitir dados para o Azure Machine Learning.
3. Pré-processe, transforme e manipule os dados conforme necessário.
4. Gerar recursos conforme necessário.
5. Dividir os dados em conjuntos de dados de treinamento/validação/teste (ou conjuntos de dados separados para cada um desses).
6. Selecionar um ou mais algoritmos de aprendizado de máquina dependendo do problema de aprendizado a ser resolvido. Por exemplo, classificação binária, classificação multiclasse, regressão.
7. Treinar um ou mais modelos usando o conjunto de dados de treinamento.
8. Pontuar o conjunto de dados de validação usando os modelos treinados.
9. Avaliar os modelos para computar a métrica relevante para o problema de aprendizado.
10. Ajuste os modelos e selecione o melhor modelo a ser implantado.

Neste exercício, já exploramos e definimos os dados no SQL Server e escolhemos o tamanho da amostra para ingestão no Azure Machine Learning. Para criar um ou mais dos modelos de previsão, decidimos:

1. Transmita os dados para o Azure Machine Learning usando o módulo [Importar Dados][import-data], disponível na seção **Entrada e Saída de Dados**. Para obter mais informações, consulte a página de referência do módulo [importar dados][import-data] .
   
    ![Importar Dados no Azure Machine Learning][17]
2. Selecione **Banco de Dados SQL do Azure** como a **Fonte de dados** no painel **Propriedades**.
3. Insira o nome de DNS do banco de dados no campo **Nome do servidor de banco de dados** . Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Insira o **Nome do banco de dados** no campo correspondente.
5. Insira o **Nome de usuário do SQL** em **Nome de conta do usuário do servidor** e a **senha** em **Senha da conta de usuário do servidor**.
7. Na área de texto Editar **consulta de banco de dados** , Cole a consulta que extrai os campos de banco de dados necessários (incluindo quaisquer campos computados, como os rótulos) e, em seguida, repara os data até o tamanho de amostra desejado.

Um exemplo de um experimento de classificação binária lendo dados diretamente do banco de dados do SQL Server é mostrado na figura abaixo. Experimentos semelhantes podem ser construídos por meio de classificação multiclasse e problemas de regressão.

![Treinamento do Azure Machine Learning][10]

> [!IMPORTANT]
> Nos exemplos de modelagem de extração de dados e consulta de amostragem fornecidos nas seções anteriores, **todos os rótulos para os três exercícios de modelagem são incluídos na consulta**. Uma etapa importante (obrigatória) em cada um dos exercícios modelagem é **excluir** os rótulos desnecessários para os dois problemas e qualquer outro **vazamento de destino**. Por exemplo, ao usar a classificação binária, use o rótulo **tipped** e exclua os campos **tip\_class**, **tip\_amount** e **total\_amount**. Esses últimos são vazamentos de destino, já que eles indicam a gorjeta paga.
> 
> Para excluir as colunas desnecessárias e/ou vazamentos de destino, é possível usar o módulo [Selecionar Colunas do Conjunto de Dados][select-columns] ou [Editar Metadados][edit-metadata]. Para saber mais, veja as páginas de referência [Selecionar Colunas no Conjunto de Dados][select-columns] e [Editar Metadados][edit-metadata].
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Implantando modelos no Azure Machine Learning
Quando o modelo estiver pronto, você pode implantá-lo facilmente como um serviço Web diretamente do experimento. Para obter mais informações sobre como implantar os serviços Web do Azure Machine Learning, veja [Implantar um serviço Web do Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).

Para implantar um novo serviço Web, você precisa:

1. Criar um experimento de pontuação.
2. Implantar o serviço Web.

Para criar um teste de pontuação por meio de um teste de treinamento **Concluído**, clique em **CRIAR TESTE DE PONTUAÇÃO** na barra de ação inferior.

![Pontuação do Azure][18]

O Azure Machine Learning tentará criar um experimento de pontuação com base nos componentes do experimento de treinamento. Em especial, ele vai:

1. Salvar o modelo treinado e remover os módulos de treinamento de modelo.
2. Identificar uma **porta de entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identificar uma **porta de saída** lógica para representar o esquema de saída do serviço Web.

Quando o experimento de pontuação for criado, revise-o e ajuste conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada e/ou a consulta por um que exclua os campos de rótulo, já que esses rótulos não estarão disponíveis no esquema quando o serviço for chamado. Também é uma boa prática reduzir o tamanho do conjunto de dados de entrada e/ou da consulta para alguns registros, o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum excluir todos os campos de entrada e incluir apenas os **Rótulos Pontuados** e **Probabilidades Pontuadas** na saída usando o módulo [Selecionar Colunas do Conjunto de Dados][select-columns].

Um exemplo de experimento de pontuação é mostrado na figura abaixo. Quando estiver pronto para implantar, clique no botão **PUBLICAR SERVIÇO WEB** na barra de ação inferior.

![Publicação do Azure Machine Learning][11]

Para recapitular, neste tutorial passo a passo, você criou um ambiente de ciência de dados do Azure, trabalhou com um grande conjunto de dados públicos até a aquisição dos dados para o treinamento de modelo e a implantação de um serviço Web do Azure Machine Learning.

### <a name="license-information"></a>Informações de Licença
Este passo a passo do exemplo, os scripts que o acompanham e os IPython Notebooks são compartilhados pela Microsoft sob a licença MIT. Verifique o arquivo LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

### <a name="references"></a>Referências
•   [Página de download de Viagens de Táxi de NYC, de Andrés Monroy](https://www.andresmh.com/nyctaxitrips/)  
•    [Frustrando os dados de corrida de táxi de NYC por Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [Pesquisa e estatísticas de NYC táxi e limusines Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data