---
title: "Analisar dados de atraso de voo com o Hadoop no HDInsight – Azure | Microsoft Docs"
description: Saiba como usar um script do Windows PowerShell para criar um cluster HDInsight, executar um trabalho do Hive, executar um trabalho do Sqoop e excluir o cluster.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00e26aa9-82fb-4dbe-b87d-ffe8e39a5412
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 77790136c9bd3a4e3f7dcabea2fbe0bcffb6eafe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analisar dados de atraso de voo usando o Hadoop no HDInsight
O Hive fornece um meio para executar trabalhos MapReduce do Hadoop por meio de uma linguagem de script semelhante à SQL, chamada *[HiveQL][hadoop-hiveql]*, que pode ser aplicada para resumo, consulta e análise de grandes volumes de dados.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight baseado em Windows. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Para conhecer as etapas que funcionam em um cluster baseado em Linux, confira [Analisar dados de atraso de voos usando o Hive no HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

Um dos maiores benefícios do Azure HDInsight é a separação do armazenamento e computação de dados. O HDInsight usa o armazenamento de blob do Azure para o armazenamento de dados. Um trabalho típico envolve três partes:

1. **Armazenamento de dados no repositório de blob do Azure.**  Por exemplo, os dados climáticos, dados do sensor, logs da Web e, nesse caso, os dados de atraso do voo são salvos no armazenamento de blobs do Azure.
2. **Executar trabalhos.** Quando for a hora de processar os dados, execute um script do Windows PowerShell (ou um aplicativo cliente) para criar um cluster HDInsight, executar trabalhos e excluir o cluster. Os trabalhos salvam dados de saída no armazenamento de blob do Azure. Os dados de saída são retidos mesmo depois que o cluster é excluído. Dessa forma, você só paga pelo que consumiu.
3. **Recupere a saída do armazenamento do blob**ou, neste tutorial, exporte os dados para um banco de dados SQL do Azure.

O diagrama a seguir ilustra o cenário e a estrutura deste tutorial:

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

Observe que os números no diagrama correspondem aos títulos das seções. **M** significa o processo principal. **A** significa o conteúdo no apêndice.

A maior parte do tutorial mostra como usar um script do Windows PowerShell para realizar as seguintes tarefas:

* Crie um cluster HDInsight.
* Executar um trabalho do Hive no cluster para calcular a média de atrasos nos aeroportos. Os dados de atraso de voo são armazenados na conta de Armazenamento de Blob do Azure.
* Executar um trabalho do Sqoop para exportar a saída do trabalho do Hive para um banco de dados SQL do Azure.
* Excluir o cluster do HDInsight.

Nos apêndices, você pode encontrar instruções de carregamento dos dados de atraso de voos, de criação/carregamento de cadeias de consulta do Hive e de preparação do banco de dados SQL do Azure para o trabalho do Sqoop.

> [!NOTE]
> As etapas deste documento são específicas de clusters HDInsight baseados no Windows. Para conhecer as etapas que funcionam em um cluster baseado em Linux, confira [Analisar dados de atraso de voos usando o Hive no HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter os seguintes itens:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Uma estação de trabalho com o PowerShell do Azure**.

    > [!IMPORTANT]
    > O suporte do Azure PowerShell para gerenciar os recursos do HDInsight usando o Gerenciador de Serviços do Azure está **preterido** e foi removido em 1º de janeiro de 2017. As etapas neste documento usam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga as etapas em [Instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar a versão mais recente do Azure PowerShell. Se você tiver scripts que precisam ser modificados para usar os novos cmdlets que funcionam com o Azure Resource Manager, confira [Migrando para as ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters de HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

**Arquivos usados neste tutorial**

Este tutorial usa o desempenho pontual de dados de voos de companhias aéreas do [Research and Innovative Technology Administration, Bureau of Transportation Statistics ou RITA][rita-website].
Uma cópia dos dados foi carregada para um contêiner de armazenamento de Blobs do Azure com a permissão de acesso de Blob Público.
Uma parte do script do PowerShell copia os dados do contêiner de blob público para o contêiner de blob padrão do cluster. O script do HiveQL também é carregado no mesmo contêiner do Blob.
Se quiser saber como obter/carregar os dados em sua própria conta de Armazenamento e como criar/carregar o arquivo de script do HiveQL, consulte o [Apêndice A](#appendix-a) e o [Apêndice B](#appendix-b).

A tabela a seguir lista os arquivos usados neste tutorial:

<table border="1">
<tr><th>Arquivos</th><th>Descrição</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>O arquivo de script HiveQL usado pelo o trabalho do Hive. Este script foi carregado em uma conta de Armazenamento de Blob do Azure com acesso público. O <a href="#appendix-b">Apêndice B</a> contém instruções para preparar e carregar este arquivo em sua própria conta de Armazenamento de Blobs do Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Dados de entrada para o trabalho do Hive. Os dados foram carregados em uma conta de armazenamento de Blob do Azure com acesso público. O <a href="#appendix-a">Apêndice A</a> contém instruções para obter e carregar os dados em sua própria conta de Armazenamento de Blobs do Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>O caminho de saída para o trabalho do Hive. O contêiner padrão é usado para armazenar os dados de saída.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>A pasta de status do trabalho do Hive no contêiner padrão.</td></tr>
</table>

## <a name="create-cluster-and-run-hivesqoop-jobs"></a>Criar cluster e executar trabalhos do Hive/Sqoop
O MapReduce do Hadoop é um processamento em lote. A maneira mais econômica de executar um trabalho do Hive é criar um cluster para o trabalho e excluir o trabalho após ele ser concluído. O script a seguir aborda todo o processo.
Para obter mais informações sobre como criar um cluster HDInsight e executar trabalhos do Hive, consulte [Criar clusters do Hadoop no HDInsight][hdinsight-provision] e [Usar o Hive com o HDInsight][hdinsight-use-hive].

**Para executar as consultas do Hive usando o Azure PowerShell**

1. Crie um banco de dados SQL do Azure para a saída do trabalho do Sqoop usando as instruções fornecidas no [Apêndice C](#appendix-c).
2. Abra o ISE do Windows PowerShell e execute o seguinte script:

    ```powershell
    $subscriptionID = "<Azure Subscription ID>"
    $nameToken = "<Enter an Alias>"

    ###########################################
    # You must configure the follwing variables
    # for an existing Azure SQL Database
    ###########################################
    $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
    $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
    $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
    $existingSqlDatabaseName = "<Azure SQL Database name>"

    $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files.
    $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different

    ###########################################
    # (Optional) configure the following variables
    ###########################################

    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2"

    $HDInsightClusterName = $namePrefix + "hdi"
    $httpUserName = "admin"
    $httpPassword = "<Enter the Password>"

    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $HDInsightClusterName # use the cluster name

    $existingSqlDatabaseTableName = "AvgDelays"
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"

    $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql"

    $jobStatusFolder = "/tutorials/flightdelays/jobstatus"

    ###########################################
    # Login
    ###########################################
    try{
        $acct = Get-AzureRmSubscription
    }
    catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionID $subscriptionID

    ###########################################
    # Create a new HDInsight cluster
    ###########################################

    # Create ARM group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the default storage account
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $existingDefaultBlobContainerName

    ###########################################
    # Prepare the HiveQL script and source data
    ###########################################

    # Create the temp location
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
    #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload data to default container

    $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"

    Invoke-Expression -Command:$azcopycmd

    ###########################################
    # Submit the Hive job
    ###########################################
    Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
    $response = Invoke-AzureRmHDInsightHiveJob `
                    -Files $hqlScriptFile `
                    -DefaultContainer $defaultBlobContainerName `
                    -DefaultStorageAccountName $defaultStorageAccountName `
                    -DefaultStorageAccountKey $defaultStorageAccountKey `
                    -StatusFolder $jobStatusFolder

    write-Host $response

    ###########################################
    # Submit the Sqoop job
    ###########################################
    $exportDir = "wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                    -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ResourceGroupName $resourceGroupName `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -HttpCredential $httpCredential `
        -WaitTimeoutInSeconds 3600 `
        -Job $sqoopJob.JobId

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -DefaultContainer $existingDefaultBlobContainerName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError

    ###########################################
    # Delete the cluster
    ###########################################
    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName
    ```
3. Conecte-se ao banco de dados SQL e consulte a média dos atrasos de voo por cidade na tabela AvgDelays:

    ![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]

- - -

## <a id="appendix-a"></a>Apêndice A - Carregar os dados de atraso de voo para o armazenamento de Blob do Azure
Carregar o arquivo de dados e os arquivos de script HiveQL (consulte o [Apêndice B](#appendix-b)) exige algum planejamento. A ideia é armazenar os arquivos de dados e o arquivo do HiveQL antes de criar um cluster do HDInsight e executar o trabalho do Hive. Você tem duas opções:

* **Usar a mesma conta de armazenamento do Azure que será usada pelo cluster do HDInsight como sistema de arquivos padrão.** Como o cluster do HDInsight terá a chave de acesso da conta de armazenamento, você não precisará fazer alterações adicionais.
* **Use uma conta de armazenamento do Azure diferente do sistema de arquivos padrão do cluster do HDInsight.** Se esse for o caso, modifique a parte de criar do script do Windows PowerShell encontrado em [Criar cluster HDInsight e executar trabalhos do Hive/Sqoop](#runjob) para incluir a conta de armazenamento como uma conta de armazenamento adicional. Para obter instruções, consulte [Criar clusters do Hadoop no HDInsight][hdinsight-provision]. O cluster do HDInsight então conhecerá a chave de acesso da conta de armazenamento.

> [!NOTE]
> O caminho do armazenamento Blob para o arquivo de dados é embutido em código no arquivo de script do HiveQL. É necessário atualizá-lo de acordo.

**Para baixar os dados de voos**

1. Navegue para [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].
2. Na página, selecione os valores a seguir:

    <table border="1">
    <tr><th>Nome</th><th>Valor</th></tr>
    <tr><td>Filtrar por ano</td><td>2013 </td></tr>
    <tr><td>Filtrar por período</td><td>Janeiro</td></tr>
    <tr><td>Campos</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (limpar todos os outros campos)</td></tr>
    </table>
3. Clique em **Baixar**.
4. Descompacte o arquivo na pasta **C:\Tutorials\FlightDelay\2013Data**. Cada arquivo é um arquivo CSV e tem aproximadamente 60 GB de tamanho.
5. Renomeie o arquivo com o nome do mês ao qual seus dados se referem. Por exemplo, o arquivo que contém os dados de janeiro seria nomeado *January.csv*.
6. Repita as etapas 2 e 5 para baixar um arquivo para cada um dos 12 meses de 2013. Você precisará de pelo menos um arquivo para executar o tutorial.

**Para carregar os dados de atraso de voo para o armazenamento de Blob do Azure**

1. Prepare os parâmetros:

    <table border="1">
    <tr><th>Nome de variável</th><th>Observações</th></tr>
    <tr><td>$storageAccountName</td><td>A conta de armazenamento do Azure para a qual você deseja carregar os dados.</td></tr>
    <tr><td>$blobContainerName</td><td>O contêiner de Blob para o qual você deseja carregar os dados.</td></tr>
    </table>
2. Abra o ISE do PowerShell do Azure.
3. Cole o seguinte script no painel de script:

    ```powershell
    [CmdletBinding()]
    Param(

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #Region - Variables
    $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
    $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
    #EndRegion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #Region - Copy the file from local workstation to Azure Blob storage
    if (test-path -Path $localFolder)
    {
        foreach ($item in Get-ChildItem -Path $localFolder){
            $fileName = "$localFolder\$item"
            $blobName = "$destFolder/$item"

            Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
        }
    }
    else
    {
        Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
    }

    # List the uploaded files on HDInsight
    Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
    #EndRegion
    ```
4. Pressione **F5** para executar o script.

Se você optar por usar um método diferente para carregar os arquivos, verifique se o caminho do arquivo é tutorials/flightdelay/data. A sintaxe para acessar os arquivos é:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

O caminho tutorials/flightdelay/data é a pasta virtual que você criou quando carregou os arquivos. Verifique se há 12 arquivos, um para cada mês.

> [!NOTE]
> Você precisa atualizar a consulta do Hive para ler por meio do novo local.
>
> Você precisa configurar a permissão de acesso do contêiner para ser pública ou para associar a conta de armazenamento ao cluster do HDInsight. Caso contrário, a cadeia de consulta do Hive não conseguirá acessar os arquivos de dados.

- - -

## <a id="appendix-b"></a>Apêndice B - Criar e carregar o script HiveQL
Usando o PowerShell do Azure, você pode executar várias instruções HiveQL uma por vez ou empacotar a instrução HiveQL em um arquivo de script. Esta seção mostra como criar um script HiveQL e carregá-lo no armazenamento de Blob do Azure usando o PowerShell do Azure. O Hive exige que os scripts de HiveQL sejam armazenados no armazenamento de Blob do Azure.

O script HiveQL executará o seguinte:

1. **Remova a tabela delays_raw**, caso a tabela já exista.
2. **Crie a tabela externa do Hive delays_raw** apontando para a localização do Armazenamento de Blobs com os arquivos de atraso de voo. Esta consulta especifica que os campos são delimitados por "," e que as linhas são finalizadas por "\n". Isso representa um problema quando os valores dos campos contêm vírgulas porque o Hive não pode diferenciar entre uma vírgula, que é um campo delimitado, e uma que faz parte de um valor do campo (que é o caso nos valores de campo de ORIGIN\_CITY\_NAME and DEST\_CITY\_NAME). Para resolver isso, a consulta cria colunas TEMP para bloquear dados que estão divididos incorretamente em colunas.
3. **Remova a tabela de atrasos**, caso a tabela já exista.
4. **Crie a tabela de atrasos**. É útil limpar os dados antes de continuar o processamento. Esta consulta cria uma nova tabela, *delays* por meio da tabela delays_raw. Observe que as colunas TEMP (conforme mencionado anteriormente) não são copiadas e que a função **substring** é usada para remover marcas de aspas dos dados.
5. **Calcula a média de atraso de tempo e agrupa os resultados por nome de cidade.** Ela também produzirá os resultados para o armazenamento de Blob. Observe que a consulta removerá apóstrofos dos dados e excluirá linhas em que o valor de **weather_delay** é nulo. Isso é necessário porque o Sqoop, usado mais adiante neste tutorial, não trata desses valores normalmente por padrão.

Para obter uma lista completa dos comandos HiveQL, consulte [Linguagem de Definição de Dados do Hive][hadoop-hiveql]. Cada comando HiveQL deve terminar com um ponto e vírgula.

**Para criar um arquivo de script HiveQL**

1. Prepare os parâmetros:

    <table border="1">
    <tr><th>Nome de variável</th><th>Observações</th></tr>
    <tr><td>$storageAccountName</td><td>A conta de armazenamento do Azure para a qual você deseja carregar o script do HiveQL.</td></tr>
    <tr><td>$blobContainerName</td><td>O contêiner de Blob para o qual você deseja carregar o script do HiveQL.</td></tr>
    </table>
2. Abra o ISE do PowerShell do Azure.
3. Copie e cole o seguinte script no painel de script:

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Blob storage variables
        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #region - Define variables
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    # The HiveQL script file is exported as this file before it's uploaded to Blob storage
    $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"

    # The HiveQL script file will be uploaded to Blob storage as this blob name
    $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"

    # These two constants are used by the HiveQL script file
    #$srcDataFolder = "tutorials/flightdelay/data"
    $dstDataFolder = "/tutorials/flightdelay/output"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #region - Validate the file and file path

    # Check if a file with the same file name already exists on the workstation
    Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
    if (test-path $hqlLocalFileName){

        $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'

        if ($isDelete.ToLower() -ne "y")
        {
            Exit
        }
    }

    # Create the folder if it doesn't exist
    $folder = split-path $hqlLocalFileName
    if (-not (test-path $folder))
    {
        Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

        new-item $folder -ItemType directory
    }
    #end region

    #region - Write the Hive script into a local file
    Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                -ForegroundColor Green

    $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

    $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
            "YEAR string, " +
            "FL_DATE string, " +
            "UNIQUE_CARRIER string, " +
            "CARRIER string, " +
            "FL_NUM string, " +
            "ORIGIN_AIRPORT_ID string, " +
            "ORIGIN string, " +
            "ORIGIN_CITY_NAME string, " +
            "ORIGIN_CITY_NAME_TEMP string, " +
            "ORIGIN_STATE_ABR string, " +
            "DEST_AIRPORT_ID string, " +
            "DEST string, " +
            "DEST_CITY_NAME string, " +
            "DEST_CITY_NAME_TEMP string, " +
            "DEST_STATE_ABR string, " +
            "DEP_DELAY_NEW float, " +
            "ARR_DELAY_NEW float, " +
            "CARRIER_DELAY float, " +
            "WEATHER_DELAY float, " +
            "NAS_DELAY float, " +
            "SECURITY_DELAY float, " +
            "LATE_AIRCRAFT_DELAY float) " +
        "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
        "LINES TERMINATED BY '\n' " +
        "STORED AS TEXTFILE " +
        "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"

    $hqlDropDelays = "DROP TABLE delays;"

    $hqlCreateDelays = "CREATE TABLE delays AS " +
        "SELECT YEAR AS year, " +
            "FL_DATE AS flight_date, " +
            "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
            "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
            "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
            "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
            "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
            "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
            "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
            "DEST_AIRPORT_ID AS dest_airport_id, " +
            "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
            "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
            "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
            "DEP_DELAY_NEW AS dep_delay_new, " +
            "ARR_DELAY_NEW AS arr_delay_new, " +
            "CARRIER_DELAY AS carrier_delay, " +
            "WEATHER_DELAY AS weather_delay, " +
            "NAS_DELAY AS nas_delay, " +
            "SECURITY_DELAY AS security_delay, " +
            "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
        "FROM delays_raw;"

    $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
        "SELECT regexp_replace(origin_city_name, '''', ''), " +
            "avg(weather_delay) " +
        "FROM delays " +
        "WHERE weather_delay IS NOT NULL " +
        "GROUP BY origin_city_name;"

    $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

    $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
    #endregion

    #region - Upload the Hive script to the default Blob container
    Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green

    # Create a storage context object
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Upload the file from local workstation to Blob storage
    Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
    #endregion

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

    A seguir estão as variáveis usadas no script:

   * **$hqlLocalFileName** - o script salva o arquivo de script HiveQL localmente antes de carregá-lo no armazenamento de Blob. Esse é o nome do arquivo. O valor padrão é <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
   * **$hqlBlobName** - Esse é o nome do blob do arquivo de script HiveQL usado no armazenamento de Blobs do Azure. O valor padrão é C:tutorials/flightdelay/flightdelays.hql. Como o arquivo será escrito diretamente no armazenamento de Blobs do Azure, NÃO há "/" no início do nome do blob. Se você quiser acessar o arquivo por meio do armazenamento de Blob, precisará adicionar uma "/" no início do nome do arquivo.
   * **$srcDataFolder** e **$dstDataFolder** - = "tutorials/flightdelay/data" = "tutorials/flightdelay/output"

- - -
## <a id="appendix-c"></a>Apêndice C - Preparar o banco de dados SQL do Azure para a saída do trabalho do Sqoop
**Para preparar o Banco de Dados SQL (mescle o script do Sqoop a este)**

1. Prepare os parâmetros:

    <table border="1">
    <tr><th>Nome de variável</th><th>Observações</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>O nome do servidor de banco de dados SQL do Azure. Não insira nada para criar um novo servidor.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>O nome de logon para o servidor de banco de dados SQL do Azure. Se $sqlDatabaseServerName for um servidor existente, o logon e a senha de logon serão usados para autenticação no servidor. Caso contrário, eles serão usados para criar um novo servidor.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>A senha de logon para o servidor de banco de dados SQL do Azure.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Esse valor é usado somente quando você estiver criando um novo servidor de banco de dados do Azure.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>O banco de dados SQL usado para criar a tabela AvgDelays para o trabalho do Sqoop. Deixar em branco criará um banco de dados chamado HDISqoop. O nome da tabela para a saída do trabalho Sqoop é AvgDelays. </td></tr>
    </table>
2. Abra o ISE do PowerShell do Azure.
3. Copie e cole o seguinte script no painel de script:

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Resource group variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
        [String]$resourceGroupName,

        # SQL database server variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseServer,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user.")]
        [String]$sqlDatabaseLogin,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user password.")]
        [String]$sqlDatabasePassword,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the region to create the Database in.")]
        [String]$sqlDatabaseLocation,   #For example, West US.

        # SQL database variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
    )

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Constants and variables

    # IP address REST service used for retrieving external IP address and creating firewall rules
    [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
    [String]$fireWallRuleName = "FlightDelay"

    # SQL database variables
    [String]$sqlDatabaseMaxSizeGB = 10

    #SQL query string for creating AvgDelays table
    [String]$sqlDatabaseTableName = "AvgDelays"
    [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
                [origin_city_name] ASC
            )
            )"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #region - Create and validate Azure resouce group
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
    }

    #EndRegion

    #region - Create and validate Azure SQL database server
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
    }

    #endregion

    #region - Create and validate Azure SQL database

    try {
        Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
    }

    #endregion

    #region -  Execute an SQL command to create the AvgDelays table

    Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = $sqlCreateAvgDelaysTable
    $cmd.executenonquery()

    $conn.close()

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

   > [!NOTE]
   > O script usa um serviço de transferência de estado representacional REST, http://bot.whatismyipaddress.com, para recuperar seu endereço IP externo. O endereço IP é usado para criar uma regra de firewall para seu servidor do Banco de Dados SQL.

    A seguir estão algumas das variáveis usadas no script:

   * **$ipAddressRestService** – O valor padrão é http://bot.whatismyipaddress.com. Trata-se de um serviço REST de endereço IP público para obter seu endereço IP externo. Você pode usar outros serviços se desejar. O endereço IP externo recuperado por meio do serviço será usado para criar uma regra de firewall para seu servidor do banco de dados SQL do Azure, para que você possa acessar o banco de dados por meio da estação de trabalho (usando o script do Windows PowerShell).
   * **$fireWallRuleName** - esse é o nome da regra de firewall para o servidor de banco de dados SQL do Azure. O nome padrão é <u>FlightDelay</u>. Você pode renomeá-lo se desejar.
   * **$sqlDatabaseMaxSizeGB** - Esse valor é usado apenas ao criar um novo servidor de banco de dados SQL do Azure. O valor padrão é 10GB. 10GB são suficientes para este tutorial.
   * **$sqlDatabaseName** - Esse valor é usado apenas ao criar um novo banco de dados SQL do Azure. O valor padrão é HDISqoop. Se você renomeá-lo, deverá atualizar o script do Windows PowerShell do Sqoop de acordo.
4. Pressione **F5** para executar o script.
5. Valide a saída do script. Tenha certeza de que o script foi executado com sucesso.

## <a id="nextsteps"></a> Próximas etapas
Agora você compreende como carregar um arquivo para o armazenamento de Blob do Azure, como preencher uma tabela Hive usando os dados do armazenamento de Blob do Azure, como executar consultas do Hive e como usar o Sqoop para exportar dados do HDFS para o banco de dados SQL do Azure. Para saber mais, consulte os seguintes artigos:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Oozie com o HDInsight][hdinsight-use-oozie]
* [Use o Sqoop com o HDInsight][hdinsight-use-sqoop]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
