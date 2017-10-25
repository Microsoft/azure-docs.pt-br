---
title: Executar um trabalho do Hadoop usando o Azure Cosmos DB e o HDInsight | Microsoft Docs
description: Saiba como executar um trabalho simples do Hive, Pig e MapReduce com o Azure Cosmos DB e o Azure HDInsight.
services: cosmos-db
author: dennyglee
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 06f0ea9d-07cb-4593-a9c5-ab912b62ac42
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/08/2017
ms.author: denlee
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 427864fc4e494c19fcda4cfd454a9923499f6337
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="Azure Cosmos DB-HDInsight"></a>Executar um trabalho do Apache Hive, Pig ou Hadoop usando o Azure Cosmos DB e o HDInsight
Este tutorial mostra como executar trabalhos do [Apache Hive][apache-hive], [Apache Pig][apache-pig] e [Apache Hadoop][apache-hadoop] MapReduce no Azure HDInsight com o conector para Hadoop do Cosmos DB. O conector para Hadoop do Cosmos DB permite que o Cosmos DB atue como uma fonte e um coletor para trabalhos do Hive, Pig e MapReduce. Este tutorial usará o Cosmos DB como a fonte de dados e o destino para trabalhos do Hadoop.

Depois de concluir este tutorial, você poderá responder às seguintes perguntas:

* Como fazer para carregar dados do Cosmos DB usando um trabalho do Hive, Pig ou MapReduce?
* Como fazer para armazenar dados no Cosmos DB usando um trabalho do Hive, Pig ou MapReduce?

Recomendamos começar assistindo ao vídeo a seguir, no qual executamos um trabalho do Hive usando o Cosmos DB e o HDInsight.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Use-Azure-DocumentDB-Hadoop-Connector-with-Azure-HDInsight/player]
>
>

Depois, volte a este artigo, no qual você receberá todos os detalhes de como executar trabalhos analíticos nos dados do Cosmos DB.

> [!TIP]
> Este tutorial presume que você tenha experiência anterior com o uso do Apache Hadoop, Hive e/ou Pig. Se você estiver começando a usar o Apache Hadoop, Hive e Pig, recomendamos visitar a [Documentação do Apache Hadoop][apache-hadoop-doc]. Este tutorial também pressupõe que você tenha experiência anterior com o Cosmos DB e tenha uma conta do Cosmos DB. Se estiver conhecendo o Cosmos DB agora ou ainda não tiver uma conta do Cosmos DB, confira nossa página [Introdução][getting-started].
>
>

Não tem tempo para fazer o tutorial e quer apenas a amostra completa de scripts do PowerShell para Hive, Pig e MapReduce? Sem problemas, elas estão [aqui][hdinsight-samples]. O download contém também arquivos hql, pig e java para essas amostras.

## <a name="NewestVersion"></a>Versão Mais Recente
<table border='1'>
    <tr><th>Versão do Conector para Hadoop</th>
        <td>1.2.0</td></tr>
    <tr><th>URI do script</th>
        <td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</td></tr>
    <tr><th>Data da Modificação</th>
        <td>26/04/2016</td></tr>
    <tr><th>Versões do HDInsight para as quais há suporte</th>
        <td>3.1, 3.2</td></tr>
    <tr><th>Log de alterações</th>
        <td>Azure Cosmos DB Java SDK atualizado para 1.6.0</br>
            Suporte adicionado para coleções particionadas como uma fonte e coletor</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial, certifique-se de ter o seguinte:

* Uma conta do Cosmos DB, um banco de dados e uma coleção com documentos. Para obter mais informações, consulte [Introdução ao Cosmos DB][getting-started]. Importe dados de exemplo para sua conta do Cosmos DB com a [ferramenta de importação do Cosmos DB][import-data].
* Produtividade. Leituras e gravações do HDInsight serão contabilizadas de acordo com suas unidades de solicitação alocadas para suas coleções.
* Capacidade para um procedimento armazenado adicional dentro de cada coleção de saída. Os procedimentos armazenados são usados para transferir os documentos resultantes.
* Capacidade para os documentos resultantes dos trabalhos de Hive, Pig ou MapReduce.
* [*Opcional*] Capacidade para uma coleção adicional.

> [!WARNING]
> Para evitar a criação de uma nova coleção durante um dos trabalhos, você pode imprimir os resultados no StdOut, salvar a saída no seu contêiner WASB ou especificar um coleção que já existe. Caso você especifique uma coleção existente, novos documentos serão criados dentro da coleção e os documentos existentes serão afetados somente se houver conflitos entre as *IDs*. **O conector substitui automaticamente documentos existentes com conflitos de ID**. Você pode desativar esse recurso definindo a opção de upsert como falsa. Se upsert estiver definido como falso e houver um conflito, o trabalho do Hadoop falhará, retornando um erro de conflito de ID.
>
>

## <a name="ProvisionHDInsight"></a>Etapa 1: criar um novo cluster HDInsight
Este tutorial usa a Ação de Script do Portal do Azure para personalizar o cluster HDInsight. Nesse tutorial, usaremos o Portal do Azure para criar o cluster HDInsight. Para sabe como usar cmdlets do PowerShell ou o SDK do .NET do HDInsight, veja o artigo [Personalizar clusters HDInsight usando Ação de Script][hdinsight-custom-provision].

1. Entre no [Portal do Azure][azure-portal].
2. Clique em **+Novo** na parte superior do painel de navegação esquerdo e procure por **HDInsight** na barra de pesquisa na folha Novo.
3. O **HDInsight** publicado pela **Microsoft** aparecerá na parte superior dos Resultados. Clique nele, em seguida, clique em **Criar**.
4. Na folha Criar Novo Cluster do HDInsight, insira seu **Nome do Cluster** e selecione a **Assinatura** com a qual você deseja provisionar o recurso.

    <table border='1'>
        <tr><td>Nome do cluster</td><td>Nome do cluster.<br/>
O nome DNS deve começar e terminar com um número alfanumérico e pode conter traços.<br/>
O campo deve ser uma cadeia com 3 a 63 caracteres.</td></tr>
        <tr><td>Nome da assinatura</td>
            <td>Se você tiver mais de uma Assinatura do Azure, selecione a assinatura que hospedará seu cluster HDInsight. </td></tr>
    </table>
5. Clique em **Selecionar Tipo de Cluster** e defina as seguintes propriedades para os valores especificados.

    <table border='1'>
        <tr><td>Tipo de cluster</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Nível do cluster</td><td><strong>Standard</strong></td></tr>
        <tr><td>Sistema operacional</td><td><strong>Windows</strong></td></tr>
        <tr><td>Versão</td><td>última versão</td></tr>
    </table>

    Agora, clique em **SELECT**.

    ![Fornecer detalhes do cluster HDInsight inicial do Hadoop][image-customprovision-page1]
6. Clique em **Credenciais** para definir seu logon e credenciais de acesso remoto. Escolha o **Nome de Usuário de Logon do Cluster** e a **Senha de Logon do Cluster**.

    Se você quiser ter um acesso remoto em seu cluster, selecione *sim* na parte inferior da folha e forneça um nome de usuário e senha.
7. Clique em **Fonte de Dados** para definir o local principal para o acesso a dados. Escolha o **Método de Seleção** e especifique uma conta de armazenamento já existente ou crie uma nova.
8. Na mesma folha, especifique um **Contêiner Padrão** e um **Local**. Clique em **SELECT**.

   > [!NOTE]
   > Selecione um local próximo à região da conta do Cosmos DB para ter um melhor desempenho
   >
   >
9. Clique em **Preços** para selecionar o número e o tipo de nós. Você poderá manter a configuração padrão e dimensionar o número de nós de Trabalho mais tarde.
10. Clique em **Configuração Opcional** e, em seguida, em **Ações de Script** na folha Configuração Opcional.

     Nas Ações de Script, digite as informações a seguir para personalizar o cluster HDInsight.

     <table border='1'>
         <tr><th>Propriedade</th><th>Valor</th></tr>
         <tr><td>Nome</td>
             <td>Especifique um nome para a ação de script.</td></tr>
         <tr><td>URI do script</td>
             <td>Especifique o URI para o script que é chamado para personalizar o cluster.</br></br>
Insira: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
         <tr><td>Principal</td>
             <td>Clique na caixa de seleção para executar o script do PowerShell no nó Principal.</br></br>
             <strong>Marque essa caixa de seleção</strong>.</td></tr>
         <tr><td>Trabalho</td>
             <td>Clique na caixa de seleção para executar o script do PowerShell no nó Trabalho.</br></br>
             <strong>Marque essa caixa de seleção</strong>.</td></tr>
         <tr><td>Zookeeper</td>
             <td>Clique na caixa de seleção para executar o script do PowerShell no nó Zookeeper.</br></br>
             <strong>Não é necessário</strong>.
             </td></tr>
         <tr><td>parâmetros</td>
             <td>Especifique os parâmetros, se exigido pelo script.</br></br>
             <strong>Nenhum parâmetro necessário</strong>.</td></tr>
     </table>
11. Criar um novo **Grupo de Recursos** ou use um Grupo de Recursos existente em sua Assinatura do Azure.
12. Agora, marque **Fixar no painel** para controlar sua implantação e clique em **Criar**!

## <a name="InstallCmdlets"></a>Etapa 2: instalar e configurar o Azure PowerShell
1. Instale o PowerShell do Azure. As instruções podem ser encontradas [aqui][powershell-install-configure].

   > [!NOTE]
   > Como alternativa, só para consultas Hive, você pode usar o Editor de Hive online do HDInsight. Para fazer isso, entre no [Portal do Azure][azure-portal] e clique em **HDInsight** no painel esquerdo para exibir uma lista dos clusters HDInsight. Clique no cluster no qual deseja executar consultas Hive e clique em **Console de Consulta**.
   >
   >
2. Abra o Ambiente de Script Integrado do PowerShell do Azure:

   * Em um computador com Windows 8 ou Windows Server 2012 ou posterior, você pode usar a Pesquisa interna. Na tela Inicial, digite **powershell ise** e clique em **Inserir**.
   * Em um computador com uma versão anterior ao Windows 8 ou ao Windows Server 2012, use o menu Iniciar. No menu Iniciar, digite **Prompt de Comando** na caixa de pesquisa e, na lista de resultados, clique em **Prompt de Comando**. No Prompt de Comando, digite **powershell_ise** e clique em **Inserir**.
3. Adicione sua conta do Azure.

   1. No Painel do Console, digite **Add-AzureAccount** e clique em **Inserir**.
   2. Digite o endereço de email associado à sua assinatura do Azure e clique em **Continuar**.
   3. Digite a senha de sua assinatura do Azure.
   4. Clique em **Entrar**.
4. O diagrama a seguir identifica as partes importantes do seu Ambiente de Script de PowerShell do Azure.

    ![Diagrama do PowerShell do Azure][azure-powershell-diagram]

## <a name="RunHive"></a>Etapa 3: Executar um trabalho do Hive usando o Cosmos DB e o HDInsight
> [!IMPORTANT]
> Todas as variáveis indicadas entre < > devem ser preenchidas usando suas configurações.
>
>

1. Defina as seguintes variáveis no seu Painel de Script do PowerShell.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"
2. <p>Vamos começar pela cadeia de caracteres de consulta. Vamos escrever uma consulta do Hive que usa todas as IDs exclusivas (_rid) e os carimbos de data/hora (_ts) gerados pelo sistema de documentos de uma coleção do Azure Cosmos DB, registra todos os documentos por minuto e armazena os resultados em uma nova coleção do Azure Cosmos DB.</p>

    <p>Primeiro, vamos criar uma tabela Hive por meio da coleção do Azure Cosmos DB. Adicione o seguinte trecho de código ao painel de Script do PowerShell <strong>após</strong> o trecho de código do n.º 1. Inclua o parâmetro opcional DocumentDB.query para limitar os documentos a somente _ts e _rid.</p>

   > [!NOTE]
   > **Ter nomeado o DocumentDB.inputCollections não foi um erro.** Sim, nós podemos adicionar várias coleções como entrada: </br>
   >
   >

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.

        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

1. Em seguida, vamos criar uma tabela Hive para a coleção de saída. As propriedades do documento de saída serão o mês, dia, hora, minuto e o número total de ocorrências.

   > [!NOTE]
   > **Mais uma vez, ter nomeado o DocumentDB.outputCollections não foi um erro.** Sim, nós podemos adicionar várias coleções como saída: </br>
   > '*DocumentDB.outputCollections*' = '*\<Nome da Coleção de Saída do DocumentDB 1\>*,*\<Nome da Coleção de Saída do DocumentDB 2\>*' </br> Os nomes de coleção são separados sem espaços, usando apenas uma única vírgula. </br></br>
   > Os documentos são distribuídos por round robin entre várias coleções. Um lote de documentos será armazenado em uma coleção, um segundo lote de documentos será armazenado na coleção seguinte e assim por diante.
   >
   >

       # Create a Hive table for the output data to DocumentDB.
       $queryStringPart2 = "drop table DocumentDB_analytics; " +
                             "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                             "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                             "tblproperties ( " +
                                 "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                 "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                 "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                 "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "
2. Por fim, vamos classificar os documentos por mês, dia, hora e minuto e inserir os resultados na tabela Hive de saída.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "
3. Adicione o trecho de script a seguir para criar uma definição de trabalho Hive por meio da consulta anterior.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Você também pode usar a opção -File para especificar um arquivo de script HiveQL no HDFS.
4. Adicione o trecho a seguir para salvar a hora de início e enviar o trabalho Hive.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
5. Adicione o seguinte para aguardar a conclusão do trabalho Hive.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
6. Adicione o seguinte para imprimir a saída padrão e os horários de início e fim.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
7. **Execute** seu novo script! **Clique** no botão de execução verde.
8. Confira os resultados. Faça logon no [Portal do Azure][azure-portal].

   1. Clique em <strong>Procurar</strong> no painel do lado esquerdo. </br>
   2. Clique em <strong>tudo</strong> na parte superior direita no painel de navegação. </br>
   3. Localize e clique em <strong>Contas do Azure Cosmos DB</strong>. </br>
   4. Em seguida, encontre sua <strong>Conta do Azure Cosmos DB</strong>, depois o <strong>Banco de dados do Azure Cosmos DB</strong> e sua <strong>Coleção do Azure Cosmos DB</strong> associada à coleção de saída especificada no trabalho na consulta de Hive.</br>
   5. Por fim, clique em <strong>Gerenciador de Documentos</strong> abaixo de <strong>Ferramentas de Desenvolvedor</strong>.</br></p>

   Você verá os resultados da consulta Hive.

   ![Resultados da consulta de Hive][image-hive-query-results]

## <a name="RunPig"></a>Etapa 4: Executar um trabalho do Pig usando o Cosmos DB e o HDInsight
> [!IMPORTANT]
> Todas as variáveis indicadas entre < > devem ser preenchidas usando suas configurações.
>
>

1. Defina as seguintes variáveis no seu Painel de Script do PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"
2. <p>Vamos começar pela cadeia de caracteres de consulta. Vamos escrever uma consulta Pig que usa todas as IDs exclusivas (_rid) e os carimbos de data/hora (_ts) gerados pelo sistema de documentos de uma coleção do Azure Cosmos DB, registra todos os documentos por minuto e armazena os resultados em uma nova coleção do Azure Cosmos DB.</p>
    <p>Primeiro, carregue documentos do Cosmos DB no HDInsight. Adicione o seguinte trecho de código ao painel de Script do PowerShell <strong>após</strong> o trecho de código do n.º 1. Adicione uma consulta do DocumentDB ao parâmetro de consulta opcional do DocumentDB para limitar os documentos a somente _ts e _rid.</p>

   > [!NOTE]
   > Sim, nós podemos adicionar várias coleções como entrada: </br>
   > '*\<Nome da Coleção de Entrada do DocumentDB 1\>*,*\<Nome da Coleção de Entrada do DocumentDB 2\>*'</br> Os nomes de coleção são separados sem espaços, usando apenas uma única vírgula. </b>
   >
   >

    Os documentos são distribuídos por round robin entre várias coleções. Um lote de documentos será armazenado em uma coleção, um segundo lote de documentos será armazenado na coleção seguinte e assim por diante.

        # Load data from Cosmos DB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
3. Em seguida, vamos classificar os documentos por mês, dia, hora, minuto e o número total de ocorrências.

       # GROUP BY minute and COUNT entries for each.
       $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                           "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                           "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "
4. Por fim, vamos armazenar os resultados em nossa nova coleção de saída.

   > [!NOTE]
   > Sim, nós podemos adicionar várias coleções como saída: </br>
   > '\<Nome da Coleção de Saída do DocumentDB 1\>, \<Nome da Coleção de Saída do DocumentDB 2\>'</br> Os nomes de coleção são separados sem espaços, usando apenas uma única vírgula.</br>
   > Os documentos serão distribuídos em round robin entre os vários documentos. Um lote de documentos será armazenado em uma coleção, um segundo lote de documentos será armazenado na coleção seguinte e assim por diante.
   >
   >

        # Store output data to Cosmos DB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "
5. Adicione o trecho de script a seguir para criar uma definição de trabalho Pig por meio da consulta anterior.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Você também pode usar a opção -File para especificar um arquivo script Pig no HDFS.
6. Adicione o trecho a seguir para salvar a hora de início e enviar o trabalho Pig.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition
7. Adicione o seguinte para aguardar a conclusão do trabalho Pig.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600
8. Adicione o seguinte para imprimir a saída padrão e os horários de início e fim.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
9. **Execute** seu novo script! **Clique** no botão de execução verde.
10. Confira os resultados. Faça logon no [Portal do Azure][azure-portal].

    1. Clique em <strong>Procurar</strong> no painel do lado esquerdo. </br>
    2. Clique em <strong>tudo</strong> na parte superior direita no painel de navegação. </br>
    3. Localize e clique em <strong>Contas do Azure Cosmos DB</strong>. </br>
    4. Em seguida, localizar seu <strong>Conta do Azure Cosmos DB</strong>, depois, <strong>banco de dados do Azure Cosmos DB</strong> e sua <strong>coleção do Azure Cosmos DB</strong> associados à coleção de saída especificada na consulta de Pig.</br>
    5. Por fim, clique em <strong>Gerenciador de Documentos</strong> abaixo de <strong>Ferramentas de Desenvolvedor</strong>.</br></p>

    Você verá os resultados da consulta Pig.

    ![Resultados da consulta de Pig][image-pig-query-results]

## <a name="RunMapReduce"></a>Etapa 5: executar um trabalho MapReduce usando o Azure Cosmos DB e o HDInsight
1. Defina as seguintes variáveis no seu Painel de Script do PowerShell.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name
2. Executaremos um trabalho MapReduce que computa o número de ocorrências para cada propriedade do Documento por meio de sua coleção do Azure Cosmos DB. Adicione este trecho de script **após** o trecho acima.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

   > [!NOTE]
   > TallyProperties-v01.jar é fornecido com a instalação personalizada do Conector para Hadoop do Cosmos DB.
   >
   >
3. Adicione o comando a seguir para enviar o trabalho MapReduce.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Além da definição do trabalho MapReduce, você também deve fornecer o nome do cluster HDInsight onde você deseja executar o trabalho MapReduce e as credenciais. O Start-AzureHDInsightJob é uma chamada assincronizada. Para verificar a conclusão do trabalho, use o cmdlet *AzureHDInsightJob de espera* .
4. Adicione o seguinte comando para verificar se há erros na execução do trabalho MapReduce.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
5. **Execute** seu novo script! **Clique** no botão de execução verde.
6. Confira os resultados. Faça logon no [Portal do Azure][azure-portal].

   1. Clique em <strong>Procurar</strong> no painel do lado esquerdo.
   2. Clique em <strong>tudo</strong> na parte superior direita no painel de navegação.
   3. Localize e clique em <strong>Contas do Azure Cosmos DB</strong>.
   4. Em seguida, localizar seu <strong>Conta do Azure Cosmos DB</strong>, depois, <strong>banco de dados do Azure Cosmos DB</strong> e sua <strong>coleção do Azure Cosmos DB</strong> associados à coleção de saída especificada no trabalho do MapReduce.
   5. Por fim, clique em <strong>Gerenciador de Documentos</strong> abaixo de <strong>Ferramentas de Desenvolvedor</strong>.

      Você verá os resultados do trabalho MapReduce.

      ![Resultados da consulta de MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Próximas etapas
Parabéns! Você acaba de executar seus primeiros trabalhos do Hive, Pig e MapReduce usando o Azure Cosmos DB e o HDInsight.

Nós abrimos o código-fonte do nosso Conector do Hadoop. Se estiver interessado, você poderá contribuir com o [GitHub][github].

Para saber mais, consulte os seguintes artigos:

* [Desenvolver um aplicativo Java com o DocumentDB][documentdb-java-application]
* [Desenvolver programas Java MapReduce para Hadoop no HDInsight][hdinsight-develop-deploy-java-mapreduce]
* [Introdução ao uso do Hadoop com o Hive no HDInsight para analisar o uso de fone de celular][hdinsight-get-started]
* [Usar o MapReduce com o HDInsight][hdinsight-use-mapreduce]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Personalizar os clusters HDInsight usando a Ação de Script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[import-data]: import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0
