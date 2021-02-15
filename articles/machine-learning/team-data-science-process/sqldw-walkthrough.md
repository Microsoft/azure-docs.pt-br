---
title: Criar e implantar um modelo usando o Azure Synapse Analytics – processo de ciência de dados de equipe
description: Crie e implante um modelo de aprendizado de máquina usando o Azure Synapse Analytics com um conjunto de informações publicamente disponível.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b638cb2b33f24220e7ceb852402862c707cc7bc6
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315993"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>O processo de ciência de dados de equipe em ação: usando o Azure Synapse Analytics
Neste tutorial, mostraremos como criar e implantar um modelo de aprendizado de máquina usando o Azure Synapse Analytics para um conjunto de informações publicamente disponível – o conjunto de informações de [táxis de NYC](https://www.andresmh.com/nyctaxitrips/) . O modelo de classificação binária construído prevê se uma gorjeta é paga ou não por uma corrida.  Os modelos incluem classificação multiclasse (se houver uma gorjeta) e regressão (a distribuição para os valores de gorjeta pagos).

O procedimento segue o fluxo de trabalho [TDSP (Processo de Ciência de Dados de Equipe)](./index.yml) . Mostramos como configurar um ambiente de ciência de dados, como carregar os dados no Azure Synapse Analytics e como usar a análise de Synapse do Azure ou um notebook IPython para explorar os dados e os recursos de engenharia para modelar. Em seguida, mostraremos como compilar e implantar um modelo com o Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>O conjunto de dados Corridas de Táxi de NYC
Os dados de Corridas de Táxi de NYC são formados por cerca de 20 GB de arquivos CSV compactados (aproximadamente 48 GB descompactados) que incluem mais de 173 milhões de corridas individuais, com tarifas pagas por cada corrida. Cada registro de corrida inclui os locais e horários de retirada e chegada, o número de licença de hack (motorista) anônimo e o número de Medallion (ID exclusiva do táxi). Os dados abrangem todas as corridas no ano de 2013 e são fornecidos nos dois conjuntos de dados a seguir para cada mês:

1. O arquivo **trip_data.csv** contém detalhes da corrida, como o número de passageiros, pontos de saída e chegada, duração e quilometragem da corrida. Aqui estão alguns exemplos de registros:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. O arquivo **trip_fare.csv** contém detalhes sobre as tarifas pagas em cada corrida, como tipo de pagamento, valor da tarifa, custos adicionais e impostos, gorjetas e pedágios e o valor total pago. Aqui estão alguns exemplos de registros:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

A **chave exclusiva** para unir trip\_data e trip\_fare é composta pelos três campos a seguir:

* medallion,
* hack\_license e
* pickup\_datetime.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Resolver três tipos de tarefas de previsão
Formulamos três problemas de previsão com base em *tip\_amount* para ilustrar três tipos de tarefas de modelagem:

1. **Classificação binária** : para prever se uma gorjeta foi paga ou não por uma corrida, ou seja, um *\_ valor Tip* maior que $0 é um exemplo positivo, enquanto um *\_ valor Tip* de $0 é um exemplo negativo.
2. **Classificação multiclasse** : prever o intervalo da gorjetas pagas pela corrida. Dividimos *tip\_amount* em cinco compartimentos ou classes:

`Class 0 : tip_amount = $0`

`Class 1 : tip_amount > $0 and tip_amount <= $5`

`Class 2 : tip_amount > $5 and tip_amount <= $10`

`Class 3 : tip_amount > $10 and tip_amount <= $20`

`Class 4 : tip_amount > $20`

3. **Tarefa de regressão** : prever a quantidade de gorjeta paga por uma corrida.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Configurar o ambiente de ciência de dados do Azure para análise avançada
Para configurar o ambiente de Ciência de Dados do Azure, execute estas etapas:

**Crie sua própria conta de armazenamento de blobs do Azure.**

* Ao provisionar seu próprio armazenamento de blobs do Azure, escolha uma localização geográfica para ele mais próxima possível do **Centro-Sul dos EUA** , que é onde estão armazenados os dados da NYC Taxi. Os dados serão copiados usando o AzCopy do contêiner de armazenamento de blobs público para um contêiner em sua própria conta de armazenamento. Quanto mais próximo seu armazenamento de blobs do Azure estiver do Centro-Sul dos EUA, mais rápido esta tarefa (Etapa 4) será concluída.
* Para criar sua própria conta de armazenamento do Azure, siga as etapas descritas em [sobre as contas de armazenamento do Azure](../../storage/common/storage-account-create.md). Lembre-se de anotar os valores das seguintes credenciais de conta de armazenamento, pois eles serão necessários mais tarde neste passo a passo.

  * **Nome da conta de armazenamento**
  * **Chave da conta de armazenamento**
  * **Nome do Contêiner** (no qual você deseja armazenar os dados no armazenamento de blobs do Azure)

**Provisione sua instância do Azure Synapse Analytics.**
Siga a documentação em [criar e consultar uma análise de Synapse do Azure no portal do Azure](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) para provisionar uma instância do Azure Synapse Analytics. Certifique-se de fazer notações nas seguintes credenciais do Azure Synapse Analytics que serão usadas em etapas posteriores.

* **Nome do servidor** : \<server Name> . Database.Windows.net
* **Nome do SQLDW (Banco de Dados)**
* **Nome de usuário**
* **Senha**

**Instale o Visual Studio e o SQL Server Data Tools.** Para obter instruções, consulte [introdução ao Visual Studio 2019 para o Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Conecte-se à sua análise de Synapse do Azure com o Visual Studio.** Para obter instruções, consulte as etapas 1 & 2 em [conectar-se à análise de SQL no Azure Synapse Analytics](../../synapse-analytics/sql/connect-overview.md).

> [!NOTE]
> Execute a seguinte consulta SQL no banco de dados que você criou em sua análise de Synapse do Azure (em vez da consulta fornecida na etapa 3 do tópico conectar) para **criar uma chave mestra**.
>
>

```sql
BEGIN TRY
       --Try to create the master key
    CREATE MASTER KEY
END TRY
BEGIN CATCH
       --If the master key exists, do nothing
END CATCH;
```

**Crie um workspace de Azure Machine Learning em sua assinatura do Azure.** Para obter instruções, confira [Criar um workspace do Azure Machine Learning](../classic/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Carregar os dados no Azure Synapse Analytics
Abra um console de comando do Windows PowerShell. Execute os seguintes comandos do PowerShell para baixar os arquivos de script SQL de exemplo que compartilhamos com você no GitHub para um diretório local que você especifica com o parâmetro *-DESTDIR*. Você pode alterar o valor do parâmetro *-DestDir* para qualquer diretório local. Se *-DestDir* não existir, ele será criado pelo script do PowerShell.

> [!NOTE]
> Talvez seja necessário **Executar como Administrador** ao executar o seguinte script do PowerShell se o *DestDir* precisar de privilégio de Administrador para criação ou gravação.
>
>

```azurepowershell
$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
$wc = New-Object System.Net.WebClient
$wc.DownloadFile($source, $ps1_dest)
.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'
```

Após a execução bem-sucedida, o diretório de trabalho atual mudará para *-DestDir*. Você deverá ver uma tela como a mostrada abaixo:

![Alterações de diretório de trabalho atual][19]

Em seu *-DestDir* , execute o seguinte script do PowerShell no modo de administrador:

```azurepowershell
./SQLDW_Data_Import.ps1
```

Quando o script do PowerShell for executado pela primeira vez, você receberá uma solicitação para inserir as informações de sua conta de armazenamento de BLOBs do Azure e do Azure. Ao concluir a primeira execução deste script do PowerShell, as credenciais inseridas serão gravadas em um arquivo de configuração SQLDW.conf no diretório de trabalho atual. A futura execução desse arquivo de script do PowerShell terá a opção de ler todos os parâmetros necessários desse arquivo de configuração. Se você precisar alterar alguns parâmetros, escolha inserir os parâmetros na tela ao receber uma solicitação por meio da exclusão desse arquivo de configuração e inserção dos valores de parâmetros conforme solicitado ou alterar os valores de parâmetro editando o arquivo SQLDW.conf em seu diretório *-DestDir* .

> [!NOTE]
> Para evitar conflitos de nome de esquema com aqueles que já existem em sua análise de Synapse do Azure Azure, ao ler parâmetros diretamente do arquivo SQLDW. conf, um número aleatório de 3 dígitos é adicionado ao nome do esquema do arquivo SQLDW. conf como o nome de esquema padrão para cada execução. O script do PowerShell pode solicitar um nome de esquema. Esse nome pode ser especificado a critério do usuário.
>
>

Esse arquivo de **script do PowerShell** conclui as seguintes tarefas:

* **Baixa e instala o AzCopy** , caso ele ainda não esteja instalado

  ```azurepowershell
  $AzCopy_path = SearchAzCopy
  if ($AzCopy_path -eq $null){
         Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
      InstallAzCopy
      $AzCopy_path = SearchAzCopy
  }
      $env_path = $env:Path
      for ($i=0; $i -lt $AzCopy_path.count; $i++){
          if ($AzCopy_path.count -eq 1){
              $AzCopy_path_i = $AzCopy_path
          } else {
              $AzCopy_path_i = $AzCopy_path[$i]
          }
          if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
              Write-Host $AzCopy_path_i 'not in system path, add it...'
              [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
              $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
              $env_path = $env:Path
          }
  ```

* **Copia dados para sua conta de armazenamento de blobs particular** com o blob público com AzCopy

  ```azurepowershell
  Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
  $start_time = Get-Date
  AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
  $end_time = Get-Date
  $time_span = $end_time - $start_time
  $total_seconds = [math]::Round($time_span.TotalSeconds,2)
  Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
  Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
  ```

* **Carrega dados usando o polybase (executando LoadDataToSQLDW. Sql) para sua análise de Synapse do Azure** de sua conta de armazenamento de BLOBs particular com os comandos a seguir.

  * Criar um esquema

    ```sql
    EXEC (''CREATE SCHEMA {schemaname};'');
    ```

  * Criar uma credencial com escopo de banco de dados

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
    WITH IDENTITY = ''asbkey'' ,
    Secret = ''{StorageAccountKey}''
    ```

  * Criar uma fonte de dados externa para um blob de armazenamento do Azure

    ```sql
    CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;

    CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;
    ```

  * Criar um formato de arquivo externo para um arquivo csv. Os dados não são compactados e os campos são separados pelo caractere de pipe.

    ```sql
    CREATE EXTERNAL FILE FORMAT {csv_file_format}
    WITH
    (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS
        (
            FIELD_TERMINATOR ='','',
            USE_TYPE_DEFAULT = TRUE
        )
    )
    ;
    ```

  * Criar tabelas externas de tarifas e corridas para o conjunto de dados Táxi de NYC na conta de Armazenamento de Blobs do Azure.

    ```sql
    CREATE EXTERNAL TABLE {external_nyctaxi_fare}
    (
        medallion varchar(50) not null,
        hack_license varchar(50) not null,
        vendor_id char(3),
        pickup_datetime datetime not null,
        payment_type char(3),
        fare_amount float,
        surcharge float,
        mta_tax float,
        tip_amount float,
        tolls_amount float,
        total_amount float
    )
    with (
        LOCATION    = ''/nyctaxifare/'',
        DATA_SOURCE = {nyctaxi_fare_storage},
        FILE_FORMAT = {csv_file_format},
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 12
    )

      CREATE EXTERNAL TABLE {external_nyctaxi_trip}
      (
             medallion varchar(50) not null,
             hack_license varchar(50)  not null,
             vendor_id char(3),
             rate_code char(3),
             store_and_fwd_flag char(3),
             pickup_datetime datetime  not null,
             dropoff_datetime datetime,
             passenger_count int,
             trip_time_in_secs bigint,
             trip_distance float,
             pickup_longitude varchar(30),
             pickup_latitude varchar(30),
             dropoff_longitude varchar(30),
             dropoff_latitude varchar(30)
      )
      with (
          LOCATION    = ''/nyctaxitrip/'',
          DATA_SOURCE = {nyctaxi_trip_storage},
          FILE_FORMAT = {csv_file_format},
          REJECT_TYPE = VALUE,
          REJECT_VALUE = 12
      )
    ```

    - Carregar dados de tabelas externas no armazenamento de BLOBs do Azure para o Azure Synapse Analytics

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_fare}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_fare}
      ;

      CREATE TABLE {schemaname}.{nyctaxi_trip}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_trip}
      ;
      ```

    - Criar um exemplo de tabela de dados (NYCTaxi_Sample) e inserir dados nela escolhendo consultas SQL nas tabelas de corridas e tarifas. (Algumas etapas deste passo a passos precisam usar esta tabela de exemplo.)

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_sample}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      (
          SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
          tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
          tip_class = CASE
                  WHEN (tip_amount = 0) THEN 0
                  WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                  WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                  WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                  ELSE 4
              END
          FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
          WHERE datepart("mi",t.pickup_datetime) = 1
          AND t.medallion = f.medallion
          AND   t.hack_license = f.hack_license
          AND   t.pickup_datetime = f.pickup_datetime
          AND   pickup_longitude <> ''0''
          AND   dropoff_longitude <> ''0''
      )
      ;
      ```

A localização geográfica de suas contas de armazenamento afeta os tempos de carregamento.

> [!NOTE]
> Dependendo da localização geográfica da sua conta de armazenamento de BLOBs particular, o processo de copiar dados de um blob público para sua conta de armazenamento particular pode levar cerca de 15 minutos, ou ainda mais, e o processo de carregar dados de sua conta de armazenamento para a análise de Synapse do Azure Azure pode levar 20 minutos ou mais.
>
>

Você precisará decidir o que fazer se tiver arquivos de origem e destino duplicados.

> [!NOTE]
> Se os arquivos .csv a serem copiados do armazenamento de blobs públicos para sua conta de armazenamento de blobs particular já existirem em sua conta de armazenamento de blob particular, o AzCopy perguntará se você deseja substituí-los. Se não quiser substituí-los, digite **n** quando for solicitado. Se quiser substituir **todos** os arquivos, digite **a** quando for solicitado. Você também pode inserir **y** para substituí os arquivos .csv individualmente.
>
>

![Saída do AzCopy][21]

Você pode usar seus próprios dados. Se os dados estiverem em seu computador local em seu aplicativo real, você ainda poderá usar o AzCopy para carregar dados locais para seu armazenamento de blobs do Azure particular. Você só precisará alterar o local de **Origem** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, no comando AzCopy do arquivo de script do PowerShell para um diretório local que contenha seus dados.

> [!TIP]
> Se os dados já estiverem em seu armazenamento de BLOBs do Azure privado em seu aplicativo real, você poderá ignorar a etapa AzCopy no script do PowerShell e carregar diretamente os dados para a análise de Synapse do Azure Azure. Isso exigirá mais edições do script para ajustá-lo para o formato de seus dados.
>
>

Esse script do PowerShell também conecta as informações do Azure Synapse Analytics aos arquivos de exemplo de exploração de dados SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb e SQLDW_Explorations_Scripts. py para que esses três arquivos estejam prontos para serem testados imediatamente após a conclusão do script do PowerShell.

Após a execução bem-sucedida, você verá uma tela parecida com a seguinte:

![Saída de uma execução bem-sucedida do script][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Exploração de dados e engenharia de recursos no Azure Synapse Analytics
Nesta seção, executaremos a exploração de dados e a geração de recursos executando consultas SQL no Azure Synapse Analytics diretamente usando o **Visual Studio Data Tools**. Todas as consultas SQL usadas nesta seção podem ser encontradas no exemplo de script chamado *SQLDW_Explorations.sql*. Esse arquivo já foi baixado em seu diretório local pelo script do PowerShell. Você também pode recuperá-lo do [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Mas o arquivo no GitHub não tem as informações do Azure Synapse Analytics conectadas.

Conecte-se à sua análise de Synapse do Azure usando o Visual Studio com o nome de logon e a senha do Azure Synapse Analytics e abra o pesquisador de **objetos do SQL** para confirmar se o banco de dados e as tabelas foram importados. Recupere o arquivo *SQLDW_Explorations.sql*.

> [!NOTE]
> Para abrir um editor de consultas do PDW (Parallel Data Warehouse), use o comando **Nova Consulta** com seu PDW selecionado no **Pesquisador de Objetos do SQL**. O editor de consulta SQL padrão não tem suporte do PDW.
>
>

Aqui estão os tipos de tarefas de exploração de dados e de geração de recursos executadas nesta seção:

* Explorar as distribuições de dados de alguns campos em períodos diferentes.
* Investigar a qualidade dos dados dos campos de longitude e latitude.
* Gerar rótulos de classificação binária e multiclasse com base em **tip\_amount**.
* Gerar recursos e computar/comparar as distâncias de viagem.
* Unir as duas tabelas e extrair uma amostra aleatória que será usada para compilar modelos.

### <a name="data-import-verification"></a>Verificação de importação de dados
Essas consultas fornecem uma verificação rápida do número de linhas e colunas nas tabelas que foram preenchidas anteriormente usando a importação em massa paralela do Polybase,

--Relatar o número de linhas na tabela <nyctaxi_trip> sem verificação de tabela

   ```sql
   SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
   ```

--Relatar o número de colunas na tabela <nyctaxi_trip>

   ```sql
   SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'
   ```

**Saída:** o resultado deve ser 173.179.759 linhas e 14 colunas.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: distribuição de corridas por licença
Este exemplo de consulta identifica os medalhões (números de táxi) com mais de 100 corridas dentro de um determinado período. A consulta aproveitaria o acesso à tabela particionada, já que é condicionada pelo esquema de partição de **pickup\_datetime**. Consultar o conjunto de dados completo também usará a tabela particionada e/ou a verificação de índice.

```sql
SELECT medallion, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

**Saída:** A consulta deve retornar uma tabela com linhas especificando o 13.369 medalhões (táxis) e o número de corridas concluídas em 2013. A última coluna contém o número de viagens concluídas.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploração: distribuição de corridas por medallion e hack_license
Este exemplo identifica os medalhões (números de táxi) e números de hack_license (motoristas) com mais de 100 corridas dentro de um determinado período.

```sql
SELECT medallion, hack_license, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

**Saída:** a consulta deve retornar uma tabela com 13.369 linhas especificando as 13.369 IDs de carro/motoristas que concluíram mais que 100 corridas em 2013. A última coluna contém o número de viagens concluídas.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: verificar registros com longitude e/ou latitude incorretos
Este exemplo investiga se qualquer um dos campos longitude e/ou latitude contém um valor inválido (graus radianos devem estar entre -90 e 90), ou tiver coordenadas (0, 0).

```sql
SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

**Saída:** a consulta retorna 837.467 corridas que têm campos de longitude e/ou latitude inválidos.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: distribuição de corridas com gorjeta versus sem gorjeta
Este exemplo localiza o número de corridas que receberam gorjetas em comparação com aquelas que não receberam em um determinado período (ou no conjunto de dados completo, se envolver o ano inteiro conforme configurado aqui). Essa distribuição reflete a distribuição de rótulo binário a ser usado posteriormente para modelagem de classificação binária.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM <schemaname>.<nyctaxi_fare>
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

**Saída:** a consulta deve retornar as seguintes frequências de gorjeta para o ano de 2013: 90.447.622 com gorjeta e 82.264.709 sem gorjeta.

### <a name="exploration-tip-classrange-distribution"></a>Exploração: distribuição de classe/intervalo de gorjetas
Esse exemplo calcula a distribuição dos intervalos de gorjetas em um determinado período de tempo (ou no conjunto de dados completo se abrangendo todo o ano). Essa distribuição de classes de rótulo será usada posteriormente para modelagem de classificação multiclasse.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

**Saída:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: calcular e comparar a distância da corrida
Este exemplo converte a longitude e a latitude de pickup e dropoff em pontos de geografia SQL, calcula a distância da viagem usando a diferença de pontos de geografia SQL e retorna uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados às coordenadas válidas apenas usando a consulta de avaliação de qualidade de dados abordada anteriormente.

```sql
/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO

SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
```

### <a name="feature-engineering-using-sql-functions"></a>Engenharia de recursos usando funções SQL
Às vezes, as funções SQL podem ser uma opção eficiente para a engenharia de recursos. Neste passo a passo, definimos uma função SQL para calcular a distância direta entre os locais de saída e chegada. Você pode executar os scripts SQL a seguir no **Visual Studio Data Tools**.

Este é o script SQL que define a função de distância.

```sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function calculate the direct distance between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO
```

Veja um exemplo para chamar essa função a fim de gerar recursos em sua consulta SQL:

--Consulta de exemplo para chamar a função para criar recursos

   ```sql
SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
   ```

**Saída:** esta consulta gera uma tabela (com 2.803.538 linhas) com latitudes e longitudes de saída e chegada e as distâncias diretas correspondentes em milhas. Estes são os resultados das três primeiras linhas:

| (Número da linha) | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Preparar dados para criação de modelo
A consulta a seguir une as tabelas **nyctaxi\_trip** e **nyctaxi\_fare** , gera um rótulo de classificação binária **tipped** , um rótulo de classificação de multiclasse **tip\_class** e extrai um exemplo do conjunto de dados totalmente unido. A amostragem é feita recuperando um subconjunto das viagens com base na hora de saída.  Essa consulta pode ser copiada e colada diretamente no módulo importar [dados][Import-data] do [Azure Machine Learning Studio (clássico)](https://studio.azureml.net) para ingestão direta de dados da instância do banco do dados SQL no Azure. A consulta exclui registros com coordenadas incorretas (0, 0).

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
WHERE datepart("mi",t.pickup_datetime) = 1
AND   t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

Quando você estiver pronto para prosseguir para o Azure Machine Learning, você pode:

1. Salve a consulta SQL final para extrair e obter amostras dos dados e copiar e colar a consulta diretamente em um módulo importar dados][Import-data] no Azure Machine Learning ou
2. Persista os dados de amostra e projetados que você planeja usar para a criação de modelos em uma nova tabela do Azure Synapse Analytics e use a nova tabela no módulo [importar dados][Import-data] no Azure Machine Learning. O script do PowerShell na etapa anterior fez essa tarefa para você. Você pode ler diretamente dessa tabela no módulo Importar Dados.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Exploração de dados e engenharia de recursos no IPython Notebook
Nesta seção, realizaremos exploração de dados e geração de recursos usando consultas Python e SQL em relação à análise de Synapse do Azure criada anteriormente. Um exemplo de notebook IPython chamado **SQLDW_Explorations.ipynb** e um arquivo de script Python **SQLDW_Explorations_Scripts.py** foram baixados no diretório local. Eles também estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Esses dois arquivos são idênticos em scripts Python. O arquivo de script Python é fornecido a você caso você não tenha um servidor do Notebook IPython. Esses dois de exemplo de arquivo Python são criados no **Python 2.7**.

As informações necessárias do Azure Synapse Analytics no notebook IPython de exemplo e no arquivo de script Python baixados para seu computador local foram conectadas pelo script do PowerShell anteriormente. Eles são executáveis sem qualquer modificação.

Se você já tiver configurado um espaço de trabalho Azure Machine Learning, poderá carregar diretamente o exemplo de notebook IPython no serviço do notebook IPython do AzureML e começar a executá-lo. Estas são as etapas para carregar o serviço do notebook IPython do AzureML:

1. Faça logon em seu espaço de trabalho do Azure Machine Learning, clique em **estúdio "** na parte superior e clique em **blocos de anotações** no lado esquerdo da página da Web.

    ![Clique em Studio e NOTEBOOKS][22]
2. Clique em **novo** no canto inferior esquerdo da página da Web e selecione **Python 2**. Em seguida, forneça um nome para o notebook e clique na marca de seleção para criar o novo Notebook IPython em branco.

    ![Clique em NOVO e em seguida, selecione Python 2][23]
3. Clique no símbolo **Jupyter** no canto superior esquerdo do novo notebook ipython.

    ![Clique no símbolo do Jupyter][24]
4. Arraste e solte o exemplo de Notebook IPython na página de **árvore** de seu serviço Notebook IPython do AzureML e clique em **Carregar**. Em seguida, o exemplo de Notebook IPython será carregado no serviço de Notebook IPython do AzureML.

    ![Clique em carregar][25]

Para executar o exemplo de Notebook IPython ou o arquivo de script Python, os seguintes pacotes Python serão necessários. Se você estiver usando o serviço de Notebook IPython do AzureML, esses pacotes já foram pré-instalados.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

Ao criar soluções analíticas avançadas em Azure Machine Learning com dados grandes, aqui está a sequência recomendada:

* Leia em uma pequena amostra dos dados em um quadro de dados na memória.
* Execute algumas visualizações e explorações usando os dados de amostrados.
* Experimente a engenharia de recursos usando os dados amostrados.
* Para exploração de dados maior, manipulação de dados e engenharia de recursos, use o Python para emitir consultas SQL diretamente na análise de Synapse do Azure.
* Decida o tamanho do exemplo adequado para criação do modelo do Azure Machine Learning.

A seguir estão alguns exemplos de exploração de dados, visualização de dados e engenharia de recursos. É possível encontrar mais explorações de dados no Notebook IPython de exemplo e no arquivo de script de Python de exemplo.

### <a name="initialize-database-credentials"></a>Inicializar as credenciais de banco de dados
Inicialize as configurações de conexão de banco de dados nas seguintes variáveis:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database driver>
```

### <a name="create-database-connection"></a>Criar conexão de banco de dados
Veja a cadeia de conexão que cria a conexão com o banco de dados.

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Relatar o número de linhas e colunas na tabela <nyctaxi_trip>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Número total de linhas = 173179759
* Número total de colunas = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Relatar o número de linhas e colunas na tabela <nyctaxi_fare>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Número total de linhas = 173179759
* Número total de colunas = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Leia-em uma amostra de dados pequena do banco de dado do Azure Synapse Analytics

```sql
t0 = time.time()

query = '''
    SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

O tempo para ler a tabela de exemplo é 14,096495 segundos.
Número de linhas e colunas recuperadas = (1000, 21).

### <a name="descriptive-statistics"></a>Estatísticas descritivas
Agora você está pronto para explorar os dados amostrados. Começamos observando algumas estatísticas descritivas para **trip\_distance** (ou qualquer outro campo escolhido a ser especificado).

```sql
df1['trip_distance'].describe()
```

### <a name="visualization-box-plot-example"></a>Visualização: exemplo de gráfico da caixa
Em seguida, analisamos a caixa para a distância de viagem para visualizar os quantis.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Saída de plotagem da caixa][1]

### <a name="visualization-distribution-plot-example"></a>Visualização: exemplo de gráfico de distribuição
Plotagens para visualização da distribuição e um histograma para os exemplos de distâncias de corridas.

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Saída de gráfico de distribuição][2]

### <a name="visualization-bar-and-line-plots"></a>Visualização: gráficos de barra e linha
Neste exemplo, podemos compartimentalizar a distância da viagem em cinco compartimentos e visualizar os resultados de compartimentalização.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Podemos plotar a distribuição de compartimentos acima em um gráfico de barras ou linhas:

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Saída de plotagem de gráfico de barra][3]

e

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```

![Saída de plotagem de linha][4]

### <a name="visualization-scatterplot-examples"></a>Visualização: exemplo de plotagem de dispersão
Mostramos o gráfico de dispersão entre **trip\_time\_in\_secs** e **trip\_distance** para ver se há alguma correlação

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Saída de dispersão de relação entre a hora e distância][6]

Da mesma forma, é possível verificar a relação entre **rate\_code** e **trip\_distance**.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Saída de dispersão de relação entre a hora e distância][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Exploração de dados em exemplos de dados usando consultas SQL no notebook IPython
Nesta seção, exploraremos distribuições de dados usando os dados de amostra que são persistidos na nova tabela que criamos acima. Explorações semelhantes podem ser executadas usando as tabelas originais.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploração: relatar o número de linhas e colunas na tabela de exemplo

```sql
nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
print 'Number of rows in sample = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
print 'Number of columns in sample = %d' % ncols.iloc[0,0]
```

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploração: distribuição de corridas com gorjeta e sem gorjeta

```sql
query = '''
SELECT tipped, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tipped
    '''

    pd.read_sql(query, conn)
```

#### <a name="exploration-tip-class-distribution"></a>Exploração: distribuição de classe de gorjetas

```sql
query = '''
    SELECT tip_class, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tip_class
'''

tip_class_dist = pd.read_sql(query, conn)
```

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Exploração: plotar a distribuição de gorjetas por classe

```sql
tip_class_dist['tip_freq'].plot(kind='bar')
```

![Plotar nº 26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: distribuição diária de corridas

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: distribuição de corridas por licença

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploração: distribuição de corridas por medalhão e carteira de habilitação

```sql
query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-time-distribution"></a>Exploração: distribuição de horário das corridas

```sql
query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distance-distribution"></a>Exploração: distribuição da distância das corridas

```sql
query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-payment-type-distribution"></a>Exploração: distribuição do tipo de pagamento

```sql
query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
pd.read_sql(query,conn)
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verificar a forma final da tabela apresentada

```sql
query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
pd.read_sql(query,conn)
```

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Compilar modelos no Azure Machine Learning
Agora estamos prontos para continuar a criação de modelos e implantação de modelo no [Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos para serem usados em qualquer um dos problemas de previsão identificados anteriormente, ou seja:

1. **Classificação binária** : para prever se uma gorjeta foi paga ou não por uma viagem.
2. **Classificação multiclasse** : prever o intervalo de gorjetas pagas, de acordo com as classes definidas anteriormente.
3. **Tarefa de regressão** : prever a quantidade de gorjeta paga por uma corrida.

Para iniciar o exercício de modelagem, faça logon no seu espaço de trabalho **Azure Machine Learning (clássico)** . Se você ainda não criou um espaço de trabalho do Machine Learning, consulte [criar um Azure Machine Learning Studio (clássico) espaço de trabalho](../classic/create-workspace.md).

1. Para começar a usar o Azure Machine Learning, consulte [o que é Azure Machine Learning Studio (clássico)?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Faça logon no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net).
3. A Home Page Machine Learning Studio (clássica) fornece uma infinidade de informações, vídeos, tutoriais, links para a referência de módulos e outros recursos. Para obter mais informações sobre Azure Machine Learning, consulte o [Azure Machine Learning centro de documentação](https://azure.microsoft.com/documentation/services/machine-learning/).

Um teste de treinamento típico é formado pelas seguintes etapas:

1. Crie um experimento **+ novo** .
2. Obtenha os dados em Azure Machine Learning Studio (clássico).
3. Pré-processe, transforme e manipule os dados conforme necessário.
4. Gerar recursos conforme necessário.
5. Dividir os dados em conjuntos de dados de treinamento/validação/teste (ou conjuntos de dados separados para cada um desses).
6. Selecionar um ou mais algoritmos de aprendizado de máquina dependendo do problema de aprendizado a ser resolvido. Por exemplo, classificação binária, classificação multiclasse, regressão.
7. Treinar um ou mais modelos usando o conjunto de dados de treinamento.
8. Pontuar o conjunto de dados de validação usando os modelos treinados.
9. Avaliar os modelos para computar a métrica relevante para o problema de aprendizado.
10. Ajuste os modelos e selecione o melhor modelo a ser implantado.

Neste exercício, já exploramos e projetamos os dados no Azure Synapse Analytics e decidimos o tamanho da amostra para ingerir no Azure Machine Learning Studio (clássico). Este é o procedimento para compilar um ou mais dos modelos de previsão:

1. Obtenha os dados em Azure Machine Learning Studio (clássico) usando o módulo [importar dados][Import-data] , disponível na seção **entrada e saída de dados** . Para saber mais, veja a página de referência do módulo [Importar dados][import-data].

    ![Dados de Importação de AM do Azure][17]
2. Selecione **Banco de Dados SQL do Azure** como a **Fonte de dados** no painel **Propriedades**.
3. Insira o nome de DNS do banco de dados no campo **Nome do servidor de banco de dados** . Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Insira o **Nome do banco de dados** no campo correspondente.
5. Insira o *Nome de usuário do SQL* em **Nome de conta do usuário do servidor** e a *senha* em **Senha da conta de usuário do servidor**.
7. Na área de texto Editar **consulta de banco de dados** , Cole a consulta que extrai os campos de banco de dados necessários (incluindo quaisquer campos computados, como os rótulos) e, em seguida, repara os data até o tamanho de amostra desejado.

Um exemplo de um experimento de classificação binária com a leitura de dados diretamente do banco de dados do Azure Synapse Analytics está na figura abaixo (Lembre-se de substituir os nomes das tabelas nyctaxi_trip e nyctaxi_fare pelo nome do esquema e pelos nomes de tabela que você usou no seu tutorial). Experimentos semelhantes podem ser construídos por meio de classificação multiclasse e problemas de regressão.

![Treino do AM do Azure][10]

> [!IMPORTANT]
> Nos exemplos de modelagem de extração de dados e consulta de amostragem fornecidos nas seções anteriores, **todos os rótulos para os três exercícios de modelagem são incluídos na consulta**. Uma etapa importante (obrigatória) em cada um dos exercícios modelagem é **excluir** os rótulos desnecessários para os dois problemas e qualquer outro **vazamento de destino**. Por exemplo, ao usar a classificação binária, use o rótulo **tipped** e exclua os campos **tip\_class** , **tip\_amount** e **total\_amount**. Esses últimos são vazamentos de destino, já que eles indicam a gorjeta paga.
>
> Para excluir as colunas desnecessárias ou vazamentos de destino, você pode usar o módulo [Selecionar Colunas do Conjunto de Dados][select-columns] ou [Editar Metadados][edit-metadata]. Para saber mais, veja as páginas de referência [Selecionar Colunas no Conjunto de Dados][select-columns] e [Editar Metadados][edit-metadata].
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Implantar modelos no Azure Machine Learning
Quando o modelo estiver pronto, você pode implantá-lo facilmente como um serviço Web diretamente do experimento. Para obter mais informações sobre como implantar os serviços Web do AM do Azure, veja [Implantar um serviço Web do Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).

Para implantar um novo serviço Web, você precisa:

1. Criar um experimento de pontuação.
2. Implantar o serviço Web.

Para criar um teste de pontuação por meio de um teste de treinamento **Concluído** , clique em **CRIAR TESTE DE PONTUAÇÃO** na barra de ação inferior.

![Pontuação do Azure][18]

O Azure Machine Learning tentará criar um experimento de pontuação com base nos componentes do experimento de treinamento. Em especial, ele vai:

1. Salvar o modelo treinado e remover os módulos de treinamento de modelo.
2. Identificar uma **porta de entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identificar uma **porta de saída** lógica para representar o esquema de saída do serviço Web.

Quando o experimento de pontuação for criado, examine os resultados e ajuste conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada ou a consulta por um que exclua os campos de rótulo, pois esses campos de rótulo não serão mapeados para o esquema ao chamar o serviço. Também é uma boa prática reduzir o tamanho do conjunto de dados de entrada e/ou da consulta para alguns registros, o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum excluir todos os campos de entrada e incluir apenas os **Rótulos Pontuados** e **Probabilidades Pontuadas** na saída usando o módulo [Selecionar Colunas do Conjunto de Dados][select-columns].

Veja na figura abaixo um exemplo de teste de pontuação. Quando estiver pronto para implantar, clique no botão **PUBLICAR SERVIÇO WEB** na barra de ação inferior.

![Publicação do AM do Azure][11]

## <a name="summary"></a>Resumo
Vamos recapitular o que fizemos neste tutorial passo a passo: você criou um ambiente de ciência de dados do Azure, trabalhou com um grande conjunto de dados público, passando pelo Processo de Ciência de Dados de Equipe, desde a aquisição dos dados até o treinamento de modelo e, em seguida, até a implantação de um serviço Web do Azure Machine Learning.

### <a name="license-information"></a>Informações de licença
Este passo a passo do exemplo, os scripts que o acompanham e os IPython Notebooks são compartilhados pela Microsoft sob a licença MIT. Verifique o arquivo LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

## <a name="references"></a>Referências
- [Página de download do Andrés Monroy NYC táxi TRIPS](https://www.andresmh.com/nyctaxitrips/)
- [FRUSTRAndo os dados de corrida de táxi de NYC por Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [Pesquisa e estatísticas de NYC táxi e limusines Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data