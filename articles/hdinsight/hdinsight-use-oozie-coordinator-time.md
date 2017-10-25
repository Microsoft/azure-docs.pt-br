---
title: Usar o coordenador do Oozie com o Hadoop baseado no tempo no HDInsight | Microsoft Docs
description: "Usar o Coordenador do Oozie com o Hadoop baseado no tempo no HDInsight, um serviço de big data. Saiba como definir fluxos de trabalho e coordenadores do Oozie e enviar trabalhos."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00c3a395-d51a-44ff-af2d-1f116c4b1c83
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 709541a88b2b15924b93c630693ad7a3f276e29c
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Usar o coordenador do Oozie com base no tempo com o Hadoop no HDInsight para definir fluxos de trabalho e coordenar trabalhos
Saiba como definir fluxos de trabalho e coordenadores e como disparar os trabalhos do coordenador baseado em tempo. É útil examinar o artigo [Usar o Oozie com o HDInsight][hdinsight-use-oozie] antes deste. Além do Oozie, você também pode agendar trabalhos usando o Azure Data Factory. Para conhecer a Azure Data Factory, confira [Usar o Pig e o Hive com o Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> Este artigo requer um cluster HDInsight baseado no Windows. Para obter informações sobre como usar o Oozie, incluindo trabalhos com base no tempo, em um cluster baseado no Linux, consulte [Usar o Oozie com Hadoop para definir e executar um fluxo de trabalho no HDInsight baseado em Linux](hdinsight-use-oozie-linux-mac.md)

## <a name="what-is-oozie"></a>O que é o Oozie
O Apache Oozie é um sistema de fluxo de trabalho/coordenação que gerencia trabalhos do Hadoop. Ele é integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para o Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

A imagem a seguir mostra o fluxo de trabalho que você irá implementar:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

O fluxo de trabalho contém duas ações:

1. Uma ação do Hive executa um script do HiveQL para contar as ocorrências de cada tipo de nível de log em um arquivo de log log4j. Cada log log4j consiste em uma linha de campos que contém um campo [LOG LEVEL] para mostrar o tipo e a gravidade, por exemplo:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    A saída do script do Hive é semelhante a:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight][hdinsight-use-hive].
2. Uma ação do Sqoop exporta a saída da ação do HiveQL para uma tabela no banco de dados SQL do Azure. Para saber mais sobre o Sqoop, confira [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Para obter as versões do Oozie com suporte em clusters HDInsight, consulte [Novidades nas versões de clusters fornecidas pelo HDInsight][hdinsight-versions].
>
>

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma estação de trabalho com o PowerShell do Azure**.

    > [!IMPORTANT]
    > O suporte do Azure PowerShell para gerenciar os recursos do HDInsight usando o Gerenciador de Serviços do Azure está **preterido** e será removido em 1º de janeiro de 2017. As etapas neste documento usam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga as etapas em [Instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar a versão mais recente do Azure PowerShell. Se você tiver scripts que precisam ser modificados para usar os novos cmdlets que funcionam com o Azure Resource Manager, confira [Migrando para as ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters de HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

* **Um cluster HDInsight**. Para saber mais sobre como criar um cluster HDInsight, confira [Criar cluster HDInsight][hdinsight-provision] ou [Introdução ao HDInsight][hdinsight-get-started]. Você precisará dos seguintes dados para percorrer o tutorial:

    <table border = "1">
    <tr><th>Propriedade do cluster</th><th>Nome de variável do Windows PowerShell</th><th>Valor</th><th>Descrição</th></tr>
    <tr><td>Nome do cluster HDInsight</td><td>$clusterName</td><td></td><td>O cluster HDInsight em que você executará este tutorial.</td></tr>
    <tr><td>Nome de usuário do cluster HDInsight</td><td>$clusterUsername</td><td></td><td>O nome do usuário do cluster HDInsight. </td></tr>
    <tr><td>Senha de usuário do cluster HDInsight </td><td>$clusterPassword</td><td></td><td>A senha de usuário do cluster HDInsight.</td></tr>
    <tr><td>Nome da conta de armazenamento do Azure</td><td>$storageAccountName</td><td></td><td>Uma conta de armazenamento do Azure disponível para o cluster HDInsight. Para este tutorial, use a conta de armazenamento padrão especificada durante o processo de provisionamento do cluster.</td></tr>
    <tr><td>Nome do contêiner de blob do Azure</td><td>$containerName</td><td></td><td>Para este exemplo, utilize o contêiner de armazenamento de blob do Azure usado para o sistema de arquivos do cluster HDInsight padrão. Por padrão, o contêiner tem o mesmo nome do cluster HDInsight.</td></tr>
    </table>
* **Um banco de dados SQL do Azure**. Você deve configurar uma regra de firewall para o servidor de Banco de Dados SQL para permitir o acesso a partir de sua estação de trabalho. Para saber mais sobre como criar um banco de dados SQL do Azure e configurar o firewall, confira [Introdução ao uso do banco de dados SQL do Azure][sqldatabase-get-started]. Este artigo fornece um script do Windows PowerShell para criar a tabela do Banco de Dados SQL do Azure necessária para este tutorial.

    <table border = "1">
    <tr><th>Propriedade de banco de dados SQL</th><th>Nome de variável do Windows PowerShell</th><th>Valor</th><th>Descrição</th></tr>
    <tr><td>Nome do servidor de banco de dados SQL</td><td>$sqlDatabaseServer</td><td></td><td>O servidor do Banco de Dados SQL para o qual o Sqoop exportará os dados. </td></tr>
    <tr><td>Nome de logon do banco de dados SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nome de logon do banco de dados SQL.</td></tr>
    <tr><td>Senha de logon do banco de dados SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Senha de logon do banco de dados SQL.</td></tr>
    <tr><td>Nome do banco de dados SQL</td><td>$sqlDatabaseName</td><td></td><td>O banco de dados SQL do Azure para o qual o Sqoop exportará dados. </td></tr>
    </table>

  > [!NOTE]
  > Por padrão, um banco de dados SQL do Azure permite conexões de serviços do Azure, como o Azure HDInsight. Se essa configuração de firewall estiver desabilitada, você deverá habilitá-la no Portal do Azure. Para saber mais sobre como criar um Banco de Dados SQL e configurar regras de firewall, confira [Criar e configurar o Banco de Dados SQL][sqldatabase-get-started].

> [!NOTE]
> Preencha os valores nas tabelas. Isso poderá ser útil para percorrer este tutorial.

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definir o fluxo de trabalho do Oozie e o script HiveQL relacionado
As definições de fluxos de trabalho do Oozie são escritas em hPDL (uma Linguagem de Definição de Processo XML). O nome do arquivo do fluxo de trabalho padrão é *workflow.xml*.  Você salvará o arquivo do fluxo de trabalho localmente e o implantará no cluster HDInsight usando o PowerShell do Azure posteriormente neste tutorial.

A ação do Hive no fluxo de trabalho chama um arquivo de script HiveQL. Esse arquivo de script contém três instruções HiveQL:

1. **A instrução DROP TABLE** exclui a tabela Hive log4j caso ela exista.
2. **A instrução CREATE TABLE** cria uma tabela externa Hive log4j apontando para o local do arquivo de log log4j;
3. **O local do arquivo de log log4j**. O delimitador de campo é ",". O delimitador de linha padrão é "\n". A tabela externa Hive é usada para evitar que o arquivo de dados seja removido do local original, caso você deseje executar o fluxo de trabalho do Oozie várias vezes.
4. **A instrução INSERT OVERWRITE** conta as ocorrências de cada tipo de nível de log da tabela Hive log4j e salva a saída em um local de armazenamento de blob do Azure.

> [!NOTE]
> Há um erro conhecido do caminho do Hive. Você encontrará esse problema ao enviar um trabalho do Oozie. As instruções para corrigir o problema podem ser encontradas na Wiki do TechNet [Erro HDInsight Hive: Não é possível renomear][technetwiki-hive-error].

**Para definir o arquivo de script HiveQL a ser chamado pelo fluxo de trabalho**

1. Crie um arquivo de texto com o seguinte conteúdo:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Existem três variáveis usadas no script:

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     O arquivo de definição do fluxo de trabalho (workflow.xml neste tutorial) irá passar esses valores para esse script HiveQL em tempo de execução.
2. Salve o arquivo como **C:\Tutorials\UseOozie\useooziewf.hql** usando a codificação ANSI (ASCII). (Use o Bloco de Notas se o seu editor de texto não fornecer essa opção.) Esse arquivo de script será implantado no cluster HDInsight posteriormente no tutorial.

**Para definir um fluxo de trabalho**

1. Crie um arquivo de texto com o seguinte conteúdo:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>
    ```

    Existem duas ações definidas no fluxo de trabalho. A ação de início é *RunHiveScript*. Se a ação for executada resultar em *OK*, a próxima ação será *RunSqoopExport*.

    O RunHiveScript possui várias variáveis. Você irá passar os valores ao enviar o trabalho do Oozie de sua estação de trabalho usando o PowerShell do Azure.

    Variáveis de fluxo de trabalho

    <table border = "1">
    <tr><th>Variáveis de fluxo de trabalho</th><th>Descrição</th></tr>
    <tr><td>${jobTracker}</td><td>Especifique a URL do controlador do trabalho do Hadoop. Use <strong>jobtrackerhost:9010</strong> no cluster HDInsight versões 3.0 e 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Especifique a URL do name node do Hadoop. Use o endereço wasb:// padrão do sistema de arquivos, por exemplo, <i>wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>.</td></tr>
    <tr><td>${queueName}</td><td>Especifica o nome da fila para a qual o trabalho será enviado. Use <strong>padrão</strong>.</td></tr>
    </table>

    Variáveis de ação do Hive

    <table border = "1">
    <tr><th>Variável de ação do Hive</th><th>Descrição</th></tr>
    <tr><td>${hiveDataFolder}</td><td>O diretório de origem do comando Hive Create Table.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>A pasta de saída da instrução INSERT OVERWRITE.</td></tr>
    <tr><td>${hiveTableName}</td><td>O nome da tabela Hive que faz referência aos arquivos de dados log4j.</td></tr>
    </table>

    Variáveis de ação do Sqoop

    <table border = "1">
    <tr><th>Variável de ação do Sqoop</th><th>Descrição</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Cadeia de conexão do Banco de Dados SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>A tabela do Banco de Dados SQL para onde os dados serão exportados.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>A pasta de saída para a instrução Hive INSERT OVERWRITE. Essa é a mesma pasta para a exportação do Sqoop (export-dir).</td></tr>
    </table>

    Para saber mais sobre o fluxo de trabalho do Oozie e como usar ações de fluxo de trabalho, confira a [documentação do Apache Oozie 4.0][apache-oozie-400] (para a versão 3.0 do cluster HDInsight) ou a [documentação do Oozie Apache 3.3.2][apache-oozie-332] (para a versão 2.1 do cluster HDInsight).

1. Salve o arquivo como **C:\Tutorials\UseOozie\workflow.xml** usando a codificação ANSI (ASCII). (Use o Bloco de Notas se o seu editor de texto não fornecer essa opção.)

**Para definir o coordenador**

1. Crie um arquivo de texto com o seguinte conteúdo:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
            <workflow>
                <app-path>${wfPath}</app-path>
            </workflow>
        </action>
    </coordinator-app>
    ```

    Há cinco variáveis usadas no arquivo de definição:

   | Variável | Descrição |
   | --- | --- |
   | ${coordFrequency} |Tempo de pausa do trabalho. A frequência sempre é expressa em minutos. |
   | ${coordStart} |Hora de início do trabalho. |
   | ${coordEnd} |Hora de término do trabalho. |
   | ${coordTimezone} |O Oozie processa trabalhos do coordenador em um fuso horário fixo sem horário de verão (geralmente representado pelo UTC). Esse fuso horário é chamado de "fuso horário de processamento do Oozie". |
   | ${wfPath} |O caminho do workflow.xml.  Se o nome do arquivo do fluxo de trabalho não for o nome do arquivo padrão (workflow.xml), você deverá especificá-lo. |
2. Salve o arquivo como **C:\Tutorials\UseOozie\coordinator.xml** usando a codificação ANSI (ASCII). (Use o Bloco de Notas se o seu editor de texto não fornecer essa opção.)

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Implantar o projeto Oozie e preparar-se para o tutorial
Você executará um script do PowerShell do Azure para realizar o seguinte:

* Copie o script do HiveQL (useoozie.hql) para o armazenamento de Blobs do Azure, wasb:///tutorials/useoozie/useoozie.hql.
* Copie workflow.xml para wasbs:///tutorials/useoozie/workflow.xml.
* Copie coordinator.xml para wasb:///tutorials/useoozie/coordinator.xml.
* Copie o arquivo de dados (/example/data/sample.log) para wasb:///tutorials/useoozie/data/sample.log.
* Criar uma tabela do Banco de Dados SQL para armazenar os dados de exportação do Sqoop. O nome da tabela é *log4jLogCount*.

**Compreender o armazenamento do HDInsight**

O HDInsight usa o armazenamento de blob do Azure para o armazenamento de dados. wasb:/ / é a implementação do HDFS (sistema de arquivos distribuído do Hadoop) no armazenamento de blobs do Azure pela Microsoft. Para obter mais informações, consulte [Usar o Armazenamento de Blobs do Azure com o HDInsight][hdinsight-storage].

Ao provisionar um cluster HDInsight, uma conta de Armazenamento do Azure e um contêiner específico dessa conta são designados como o sistema de arquivos padrão, exatamente como no HDFS. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de provisionamento. Para saber mais sobre como adicionar mais contas de armazenamento, confira [Provisionar clusters HDInsight][hdinsight-provision]. Para simplificar o script do PowerShell usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivos padrão, localizado em */tutoriais/useoozie*. Por padrão, esse contêiner tem o mesmo nome do cluster HDInsight.
A sintaxe do é:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]
> Somente a sintaxe *wasb://* tem suporte no cluster HDInsight versão 3.0. A antiga sintaxe *asv://* tem suporte em clusters HDInsight 1.6 e 2.1, mas não tem suporte em clusters HDInsight 3.0 ou 3.0.
>
> O caminho wasb:// é um caminho virtual. Para obter mais informações, consulte [Usar o Armazenamento de Blobs do Azure com o HDInsight][hdinsight-storage].

Um arquivo armazenado no contêiner do sistema de arquivos padrão pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (usando workflow.xml como um exemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

    tutorials/useoozie/workflow.xml

**Compreender a tabela interna e a tabela externa do Hive**

Existem algumas coisas que você precisa saber sobre a tabela interna e a tabela externa do Hive:

* O comando CREATE TABLE cria uma tabela interna, também conhecida como uma tabela gerenciada. O arquivo de dados deve estar localizado no contêiner padrão.
* O comando CREATE TABLE move o arquivo de dados para a pasta /hive/warehouse/<TableName> no contêiner padrão.
* O comando CREATE EXTERNAL TABLE cria uma tabela externa. O arquivo de dados pode estar localizado fora do contêiner padrão.
* O comando CREATE EXTERNAL TABLE não move o arquivo de dados.
* O comando CREATE EXTERNAL TABLE não permite nenhuma subpasta dentro da pasta especificada na cláusula LOCATION. Essa é a razão pela qual o tutorial faz uma cópia do arquivo sample.log.

Para obter mais informações, consulte [HDInsight: introdução às tabelas internas e externas do Hive][cindygross-hive-tables].

**Para preparar-se para o tutorial**

1. Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell_ISE** e clique em **ISE do Windows PowerShell**. Para obter mais informações, confira [Iniciar o Windows PowerShell no Windows 8 e no Windows][powershell-start]).
2. No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

    ```powershell
    Add-AzureAccount
    ```

    Será solicitado que você insira suas credenciais de conta do Azure. Esse método de adicionar uma conexão de assinatura expira e, depois de 12 horas, você precisará executar o cmdlet novamente.

   > [!NOTE]
   > Se você tiver várias assinaturas do Azure e a assinatura padrão não for a que você deseja usar, use o cmdlet <strong>Select-AzureSubscription</strong> para selecionar uma assinatura.

3. Copie o seguinte script no painel de script e defina as seis primeiras variáveis:

    ```powershell
    # WASB variables
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<BlobStorageContainerName>"

    # SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    # Oozie files for the tutorial
    $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
    $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
    $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    ```

    Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos](#prerequisites) deste tutorial.

4. Acrescente o seguinte ao script no painel de script:

    ```powershell
    # Create a storage context object
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    function uploadOozieFiles()
    {
        Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
        Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
        Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
    }

    function prepareHiveDataFile()
    {
        Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
        Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
    }

    function prepareSQLDatabase()
    {
        # SQL query string for creating log4jLogsCount table
        $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [Level] [nvarchar](10) NOT NULL,
                [Total] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
            [Level] ASC
            )
            )"

        #Create the log4jLogsCount table
        Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $cmdCreateLog4jCountTable
        $cmd.executenonquery()

        $conn.close()
    }

    # upload workflow.xml, coordinator.xml, and ooziewf.hql
    uploadOozieFiles;

    # make a copy of example/data/sample.log to example/data/log4j/sample.log
    prepareHiveDataFile;

    # create log4jlogsCount table on SQL database
    prepareSQLDatabase;
    ```

5. Clique em **Executar Script** ou pressione **F5** para executar o script. A saída será semelhante a:

    ![Saída de preparação do tutorial][img-preparation-output]

## <a name="run-the-oozie-project"></a>Executar o projeto Oozie
Atualmente, o PowerShell do Azure não fornece nenhum cmdlet para definir trabalhos do Oozie. Você pode usar o cmdlet **Invoke-RestMethod** para invocar os serviços Web do Oozie. A API de Serviços Web do Oozie é uma API REST HTTP JSON. Para saber mais sobre a API de Serviços Web do Oozie, confira a [documentação do Apache Oozie 4.0][apache-oozie-400] (para a versão 3.0 do cluster HDInsight) ou a [documentação do Oozie Apache 3.3.2][apache-oozie-332] (para a versão 2.1 do cluster HDInsight).

**Para enviar um trabalho do Oozie**

1. Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell_ISE** e clique em **ISE do Windows PowerShell**. Para obter mais informações, confira [Iniciar o Windows PowerShell no Windows 8 e no Windows][powershell-start]).
2. Copie o script a seguir no painel de script e defina as catorze primeiras variáveis (ignore a **$storageUri**).

    ```powershell
    #HDInsight cluster variables
    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterUserPassword>"

    #Azure Blob storage (WASB) variables
    $storageAccountName = "<StorageAccountName>"
    $storageContainerName = "<BlobContainerName>"
    $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

    #Azure SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"

    #Oozie WF/coordinator variables
    $coordStart = "2014-03-21T13:45Z"
    $coordEnd = "2014-03-21T13:45Z"
    $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
    $coordTimezone = "UTC"    #UTC/GMT

    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"  
    $sqlDatabaseTableName = "log4jLogsCount"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
    ```

    Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos](#prerequisites) deste tutorial.

    $coordstart e $coordend são a hora de início e de término do fluxo de trabalho. Para localizar o horário UTC/GMT, pesquise "hora utc" no bing.com. $coordFrequency é a frequência em minutos na qual você deseja executar o fluxo de trabalho.
3. Acrescente o texto a seguir ao script. Esta parte define a carga do Oozie:

    ```powershell
    #OoziePayload used for Oozie web service submission
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
            <name>nameNode</name>
            <value>$storageUrI</value>
        </property>

        <property>
            <name>jobTracker</name>
            <value>jobtrackerhost:9010</value>
        </property>

        <property>
            <name>queueName</name>
            <value>default</value>
        </property>

        <property>
            <name>oozie.use.system.libpath</name>
            <value>true</value>
        </property>

        <property>
            <name>oozie.coord.application.path</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>wfPath</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>coordStart</name>
            <value>$coordStart</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>$coordEnd</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>$coordFrequency</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>$coordTimezone</value>
        </property>

        <property>
            <name>hiveScript</name>
            <value>$hiveScript</value>
        </property>

        <property>
            <name>hiveTableName</name>
            <value>$hiveTableName</value>
        </property>

        <property>
            <name>hiveDataFolder</name>
            <value>$hiveDataFolder</value>
        </property>

        <property>
            <name>hiveOutputFolder</name>
            <value>$hiveOutputFolder</value>
        </property>

        <property>
            <name>sqlDatabaseConnectionString</name>
            <value>&quot;$sqlDatabaseConnectionString&quot;</value>
        </property>

        <property>
            <name>sqlDatabaseTableName</name>
            <value>$SQLDatabaseTableName</value>
        </property>

        <property>
            <name>user.name</name>
            <value>admin</value>
        </property>

    </configuration>
    "@
    ```

   > [!NOTE]
   > A principal diferença ao comparar com o arquivo de carga útil de envio do fluxo de trabalho é a variável **oozie.coord.application.path**. Ao enviar um trabalho de fluxo de trabalho, use **oozie.wf.application.path** em vez disso.

4. Acrescente o texto a seguir ao script. Esta parte verifica o status do serviço web do Oozie:

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

        if($oozieServerSatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
        }
    }
    ```

5. Acrescente o texto a seguir ao script. Esta parte cria um trabalho Oozie:

    ```powershell
    function createOozieJob()
    {
        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        return $oozieJobId
    }
    ```

   > [!NOTE]
   > Ao enviar um trabalho de fluxo de trabalho, você deve fazer outra chamada do serviço Web para iniciar o trabalho depois que o trabalho for criado. Nesse caso, o trabalho do coordenador é disparado por tempo. O trabalho será iniciado automaticamente.

6. Acrescente o texto a seguir ao script. Esta parte verifica o status do trabalho do Oozie:

    ```powershell
    function checkOozieJobStatus($oozieJobId)
    {
        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
        if($JobStatus -notmatch "SUCCEEDED")
        {
            Write-Host "Check logs at http://headnode0:9014/cluster for detais."
        }
    }
    ```

7. (Opcional) Acrescente o texto a seguir ao script.

    ```powershell
    function listOozieJobs()
    {
        Write-Host "Listing Oozie jobs..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

        write-host "Job ID                                   App Name        Status      Started                         Ended"
        write-host "----------------------------------------------------------------------------------------------------------------------------------"
        foreach($job in $response.workflows)
        {
            Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
        }
    }

    function ShowOozieJobLog($oozieJobId)
    {
        Write-Host "Showing Oozie job info..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
        write-host $response
    }

    function killOozieJob($oozieJobId)
    {
        Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
    }
    ```

8. Acrescente ao script o seguinte:

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

Remova os sinais # se desejar executar as funções adicionais.

9. Se seu cluster HDinsight for versão 2.1, substitua "https://$clusterName.azurehdinsight.net:443/oozie/v2/" por "https://$clusterName.azurehdinsight.net:443/oozie/v1/". A versão 2.1 do cluster HDInsight não oferece suporte à versão 2 dos serviços web.
10. Clique em **Executar Script** ou pressione **F5** para executar o script. A saída será semelhante a:

     ![Saída do fluxo de trabalho da execução do tutorial][img-runworkflow-output]
11. Conecte-se ao Banco de Dados SQL para ver os dados exportados.

**Para verificar o log de erros do trabalho**

Para solucionar problemas de um fluxo de trabalho, o arquivo de log Oozie pode ser encontrado em C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log do headnode do cluster. Para saber mais sobre o RDP, veja [Administração de clusters HDInsight usando o portal do Azure][hdinsight-admin-portal].

**Para executar o tutorial novamente**

Para executar novamente o fluxo de trabalho, execute o seguinte procedimento:

* Exclua o arquivo de saída do script do Hive.
* Exclua os dados na tabela log4jLogsCount.

Este é um exemplo de script do Windows PowerShell que você pode usar:

```powershell
$storageAccountName = "<AzureStorageAccountName>"
$containerName = "<ContainerName>"

#SQL database variables
$sqlDatabaseServer = "<SQLDatabaseServerName>"
$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
$sqlDatabaseName = "<SQLDatabaseName>"
$sqlDatabaseTableName = "log4jLogsCount"

Write-host "Delete the Hive script output file ..." -ForegroundColor Green
$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
$conn.open()
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.connection = $conn
$cmd.commandtext = "delete from $sqlDatabaseTableName"
$cmd.executenonquery()

$conn.close()
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como definir um fluxo de trabalho do Oozie, um coordenador do Oozie e como executar um trabalho de coordenador do Oozie usando o PowerShell do Azure. Para saber mais, consulte os seguintes artigos:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Usar o armazenamento de Blobs do Azure com o HDInsight][hdinsight-storage]
* [Administrar clusters HDInsight usando o Azure PowerShell][hdinsight-admin-powershell]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Use o Sqoop com o HDInsight][hdinsight-use-sqoop]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

