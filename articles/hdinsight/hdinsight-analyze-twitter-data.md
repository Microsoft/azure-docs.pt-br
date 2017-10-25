---
title: "Analisar dados do Twitter com o Hadoop no HDInsight – Azure | Microsoft Docs"
description: "Saiba como usar o Hive para analisar dados do Twitter com Hadoop no HDInsight para encontrar a frequência de uso de uma determinada palavra."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 78e4ea33-9714-424d-ac07-3d60ecaebf2e
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: e678d5d332b7fbbfeeebbea86efd7931bda15bb1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analisar dados do Twitter usando o Hive no HDInsight
Sites sociais são uma das forças principais para a adoção de big data. APIs públicas fornecidas por sites, como o Twitter, são uma fonte útil de dados para analisar e compreender as tendências populares.
Neste tutorial, você obterá tweets usando o API de streaming do Twitter e, em seguida, usará o Apache Hive no HDInsight do Azure para obter uma lista de usuários do Twitter que enviaram mais tweets contendo uma determinada palavra.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight baseado em Windows. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Para obter as etapas específicas para um cluster baseado em Linux, confira [Analisar dados do Twitter usando o Hive no HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma estação de trabalho** com o PowerShell do Azure instalado e configurado.

    Para executar scripts do Windows PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Executar scripts do Windows PowerShell][powershell-script].

    Antes de executar scripts do Windows PowerShell, verifique se você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

    ```powershell
    Login-AzureRmAccount
    ```

    Se você tiver várias assinaturas do Azure, use o seguinte cmdlet para definir a assinatura atual:

    ```powershell
    Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>
    ```

    > [!IMPORTANT]
    > O suporte do Azure PowerShell para gerenciar os recursos do HDInsight usando o Gerenciador de Serviços do Azure está **preterido** e foi removido em 1º de janeiro de 2017. As etapas neste documento usam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga as etapas em [Instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar a versão mais recente do Azure PowerShell. Se você tiver scripts que precisam ser modificados para usar os novos cmdlets que funcionam com o Azure Resource Manager, confira [Migrando para as ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters de HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

* **Um cluster Azure HDInsight**. Para obter informações sobre como provisionar um cluster, consulte [Introdução ao HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision]. Você precisará do nome do cluster posteriormente no tutorial.

A tabela a seguir lista os arquivos usados neste tutorial:

| Arquivos | Descrição |
| --- | --- |
| /tutorials/twitter/data/tweets.txt |Os dados de origem para o trabalho do Hive. |
| /tutorials/twitter/output |A pasta de saída para o trabalho do Hive. O nome do arquivo de saída do trabalho do Hive padrão é **000000_0**. |
| tutorials/twitter/twitter.hql |O arquivo de script HiveQL. |
| /tutorials/twitter/jobstatus |O status do trabalho do Hadoop. |

## <a name="get-twitter-feed"></a>Obter feed do Twitter
Neste tutorial, você usará as [APIs de streaming do Twitter][twitter-streaming-api]. A API de streaming específica do Twitter que você usará é [statuses/filter][twitter-statuses-filter].

> [!NOTE]
> Um arquivo contendo 10.000 tweets e o arquivo de script do Hive (abordado na próxima seção) foram carregados em um contêiner de Blob público. Você pode ignorar esta seção se quiser usar os arquivos carregados.

[Dados de tweets](https://dev.twitter.com/docs/platform-objects/tweets) são armazenados no formato JSON (JavaScript Object Notation) que contém uma estrutura aninhada complexa. Em vez de escrever várias linhas de código usando uma linguagem de programação convencional, você pode transformar essa estrutura aninhada em uma tabela do Hive para que possa ser consultada por uma linguagem semelhante à SQL chamada HiveQL.

O Twitter usa OAuth para oferecer acesso autorizado à sua API. OAuth é um protocolo de autenticação que permite ao usuários aprovar os aplicativos para atuar em seu nome sem compartilhar sua senha. Mais informações podem ser encontradas em [oauth.net](http://oauth.net/) ou no excelente [Guia para iniciantes do OAuth](http://hueniverse.com/oauth/) no Hueniverse.

A primeira etapa para usar OAuth é criar um novo aplicativo no site do desenvolvedor do Twitter.

**Para criar um aplicativo do Twitter**

1. Entre em [https://apps.twitter.com/](https://apps.twitter.com/). Clique no link **Inscreva-se agora** se você não tem uma conta do Twitter.
2. Clique em **Criar Novo Aplicativo**.
3. Digite o **Nome**, a **Descrição** e o **Site**. Você pode fazer uma URL para o campo **Site** . A tabela a seguir mostra alguns valores de exemplo para usar:

   | Campo | Valor |
   | --- | --- |
   |  Nome |MyHDInsightApp |
   |  Descrição |MyHDInsightApp |
   |  Site |http://www.myhdinsightapp.com |
4. Marque **Sim, eu concordo** e, em seguida, clique em **Criar seu aplicativo do Twitter**.
5. Clique na guia **Permissões** . A permissão padrão é **Somente leitura**. Isso é suficiente para este tutorial.
6. Clique na guia **Chaves e Tokens de acesso** .
7. Clique em **Criar meu token de acesso**.
8. Clique em **OAuth de teste** no canto superior direito da página.
9. Anote a **chave do consumidor**, o **Segredo do consumidor**, o **Token de acesso** e o **Segredo do token de acesso**. Você precisará dos valores mais tarde no tutorial.

Neste tutorial, você usa o Windows PowerShell para fazer a chamada de serviço Web. A outra ferramenta popular para fazer chamadas de serviço da Web é o [*Curl*][curl]. O Curl pode ser baixado [aqui][curl-download].

> [!NOTE]
> Quando usar o comando curl no Windows, use aspas duplas, em vez de aspas simples, para os valores de opção.

**Para obter tweets**

1. Abra o ISE (ambiente de script integrado) do Windows PowerShell. (Na tela Iniciar do Windows 8, digite **PowerShell_ISE** e, em seguida, clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][powershell-start].)
2. Copie o seguinte script no painel de script:

    ```powershell
    #region - variables and constants
    $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

    # Enter the OAuth information for your Twitter application
    $oauth_consumer_key = "<TwitterAppConsumerKey>";
    $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
    $oauth_token = "<TwitterAppAccessToken>";
    $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

    $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

    $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
    $track = [System.Uri]::EscapeDataString($trackString);
    $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    Login-AzureRmAccount
    #endregion

    #region - Create a block blob object for writing tweets into Blob storage
    Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $containerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

    Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
    Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

    Write-Host "Create block blob object ..." -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($containerName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    #end region

    # region - Format OAuth strings
    Write-Host "Format oauth strings ..." -ForegroundColor Green
    $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
    $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
    $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

    $signature = "POST&";
    $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
    $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
    $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
    $signature += [System.Uri]::EscapeDataString("track=" + $track);

    $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

    $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
    $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
    $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

    $oauth_authorization = 'OAuth ';
    $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
    $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
    $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
    $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
    $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
    $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
    $oauth_authorization += 'oauth_version="1.0"';

    $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
    #endregion

    #region - Read tweets
    Write-Host "Create HTTP web request ..." -ForegroundColor Green
    [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
    $request.Method = "POST";
    $request.Headers.Add("Authorization", $oauth_authorization);
    $request.ContentType = "application/x-www-form-urlencoded";
    $body = $request.GetRequestStream();

    $body.write($post_body, 0, $post_body.length);
    $body.flush();
    $body.close();
    $response = $request.GetResponse() ;

    Write-Host "Start stream reading ..." -ForegroundColor Green

    Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

    $inrec = $sReader.ReadLine()
    $count = 0
    while (($inrec -ne $null) -and ($count -le $lineMax))
    {
        if ($inrec -ne "")
        {
            Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

            $writeStream.WriteLine($inrec)
            $count ++
        }

        $inrec=$sReader.ReadLine()
    }
    #endregion

    #region - Write tweets to Blob storage
    Write-Host "Write to the destination blob ..." -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    $sReader.close()
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Defina as cinco a oito primeiras variáveis no script:

    Variável|Descrição
    ---|---
    $clusterName|Esse é o nome do cluster HDInsight em que você deseja executar o aplicativo.
    $oauth_consumer_key|Esta é a **chave de consumidor** do aplicativo do Twitter que você anotou anteriormente ao criar esse aplicativo.
    $oauth_consumer_secret|Este é o **segredo de consumidor** do aplicativo do Twitter que você anotou anteriormente.
    $oauth_token|Este é o **token de acesso** do aplicativo do Twitter que você anotou anteriormente.
    $oauth_token_secret|Este é o **segredo de token de acesso** do aplicativo do Twitter que você anotou anteriormente.
    $destBlobName|É o nome de saída do blob. O valor padrão é **tutorials/twitter/data/tweets.txt**. Se alterar o valor padrão, você precisará atualizar os scripts do Windows PowerShell adequadamente.
    $trackString|O serviço da Web retornará tweets relacionados a essas palavras-chave. O valor padrão é **Azure, Nuvem, HDInsight**. Se alterar o valor padrão, você atualizará os scripts do Windows PowerShell adequadamente.
    $lineMax|O valor determina quantos tweets o script lerá. Leva aproximadamente três minutos para ler 100 tweets. Você pode definir um número maior, mas levará mais tempo para fazer o download.

1. Pressione **F5** para executar o script. Se você tiver problemas, como alternativa, selecione todas as linhas e então pressione **F8**.
2. Você deverá ver a mensagem "Concluído!" no final da saída. Qualquer mensagem de erro será exibida em vermelho.

Como um procedimento de validação, você pode verificar o arquivo de saída, **/tutorials/twitter/data/tweets.txt**, em seu armazenamento de blob do Azure usando um gerenciador de armazenamento do Azure ou o PowerShell do Azure. Para um exemplo de script do Windows PowerShell para listagem de arquivos, consulte [Usar o Armazenamento de Blobs com o HDInsight][hdinsight-storage-powershell].

## <a name="create-hiveql-script"></a>Criar o script HiveQL
Usando o PowerShell do Azure, você pode executar várias instruções HiveQL uma por vez ou empacotar a instrução HiveQL em um arquivo de script. Neste tutorial, você irá criar um script HiveQL. O arquivo de script deve ser carregado para o armazenamento de Blob do Azure. Na próxima seção, você executará o arquivo de script usando o PowerShell do Azure.

> [!NOTE]
> O arquivo de script do Hive e um arquivo contendo 10.000 tweets foram carregados em um contêiner de Blob público. Você pode ignorar esta seção se quiser usar os arquivos carregados.

O script HiveQL executará o seguinte:

1. **Remova a tabela tweets_raw**, caso a tabela já exista.
2. **Crie a tabela tweets_raw do Hive**. Essa tabela Hive estruturada temporária contém os dados para mais processamento de ETL (extração, transformação e carregamento). Para obter informações sobre partições, consulte o [tutorial do Hive][apache-hive-tutorial].
3. **Carregue os dados** da pasta de origem, /tutorials/twitter/data. O conjunto de dados grande de tweets aninhados no formato JSON agora foi transformado em uma estrutura de tabela temporária do Hive.
4. **Remova a tabela de tweets** , caso a tabela já exista.
5. **Crie a tabela de tweets**. Antes de consultar o conjunto de dados de tweets usando o Hive, você precisa executar outro processo de ETL. Esse processo de ETL define um esquema de tabela mais detalhado para os dados armazenados na tabela "twitter_raw".
6. **Insira tabela de substituição**. Este script complexo do Hive iniciará um conjunto de longos trabalhos do MapReduce pelo cluster do Hadoop. Dependendo do seu conjunto de dados e do tamanho do cluster, o processo pode demorar cerca de 10 minutos.
7. **Insira diretório de substituição**. Execute uma consulta e passe o conjunto de dados para um arquivo. Essa consulta retornará uma lista de usuários do Twitter que enviou a maioria dos tweets que contêm a palavra "Azure".

**Para criar um script do Hive e carregá-lo para o Azure**

1. Abra o ISE do Windows PowerShell.
2. Copie o seguinte script no painel de script:

    ```powershell
    #region - variables and constants
    $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
    $subscriptionID = "<Azure Subscription ID>"

    $sourceDataPath = "/tutorials/twitter/data"
    $outputPath = "/tutorials/twitter/output"
    $hqlScriptFile = "tutorials/twitter/twitter.hql"

    $hqlStatements = @"
    set hive.exec.dynamic.partition = true;
    set hive.exec.dynamic.partition.mode = nonstrict;

    DROP TABLE tweets_raw;
    CREATE EXTERNAL TABLE tweets_raw (
        json_response STRING
    )
    STORED AS TEXTFILE LOCATION '$sourceDataPath';

    DROP TABLE tweets;
    CREATE TABLE tweets
    (
        id BIGINT,
        created_at STRING,
        created_at_date STRING,
        created_at_year STRING,
        created_at_month STRING,
        created_at_day STRING,
        created_at_time STRING,
        in_reply_to_user_id_str STRING,
        text STRING,
        contributors STRING,
        retweeted STRING,
        truncated STRING,
        coordinates STRING,
        source STRING,
        retweet_count INT,
        url STRING,
        hashtags array<STRING>,
        user_mentions array<STRING>,
        first_hashtag STRING,
        first_user_mention STRING,
        screen_name STRING,
        name STRING,
        followers_count INT,
        listed_count INT,
        friends_count INT,
        lang STRING,
        user_location STRING,
        time_zone STRING,
        profile_image_url STRING,
        json_response STRING
    );

    FROM tweets_raw
    INSERT OVERWRITE TABLE tweets
    SELECT
        cast(get_json_object(json_response, '$.id_str') as BIGINT),
        get_json_object(json_response, '$.created_at'),
        concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
        substr (get_json_object(json_response, '$.created_at'),27,4)),
        substr (get_json_object(json_response, '$.created_at'),27,4),
        case substr (get_json_object(json_response, '$.created_at'),5,3)
            when "Jan" then "01"
            when "Feb" then "02"
            when "Mar" then "03"
            when "Apr" then "04"
            when "May" then "05"
            when "Jun" then "06"
            when "Jul" then "07"
            when "Aug" then "08"
            when "Sep" then "09"
            when "Oct" then "10"
            when "Nov" then "11"
            when "Dec" then "12" end,
        substr (get_json_object(json_response, '$.created_at'),9,2),
        substr (get_json_object(json_response, '$.created_at'),12,8),
        get_json_object(json_response, '$.in_reply_to_user_id_str'),
        get_json_object(json_response, '$.text'),
        get_json_object(json_response, '$.contributors'),
        get_json_object(json_response, '$.retweeted'),
        get_json_object(json_response, '$.truncated'),
        get_json_object(json_response, '$.coordinates'),
        get_json_object(json_response, '$.source'),
        cast (get_json_object(json_response, '$.retweet_count') as INT),
        get_json_object(json_response, '$.entities.display_url'),
        array(
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
        array(
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
        trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
        trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
        get_json_object(json_response, '$.user.screen_name'),
        get_json_object(json_response, '$.user.name'),
        cast (get_json_object(json_response, '$.user.followers_count') as INT),
        cast (get_json_object(json_response, '$.user.listed_count') as INT),
        cast (get_json_object(json_response, '$.user.friends_count') as INT),
        get_json_object(json_response, '$.user.lang'),
        get_json_object(json_response, '$.user.location'),
        get_json_object(json_response, '$.user.time_zone'),
        get_json_object(json_response, '$.user.profile_image_url'),
        json_response
    WHERE (length(json_response) > 500);

    INSERT OVERWRITE DIRECTORY '$outputPath'
    SELECT name, screen_name, count(1) as cc
        FROM tweets
        WHERE text like "%Azure%"
        GROUP BY name,screen_name
        ORDER BY cc DESC LIMIT 10;
    "@
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #endregion

    #region - Create a block blob object for writing the Hive script file
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

    Write-Host "Define the connection string ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

    Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    $writeStream.Writeline($hqlStatements)
    #endregion

    #region - Write the Hive script file to Blob storage
    Write-Host "Write to the destination blob ... " -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $hqlScriptBlob.UploadFromStream($memStream)
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Defina as duas primeiras variáveis no script:

   | Variável | Descrição |
   | --- | --- |
   |  $clusterName |Digite o nome do cluster HDInsight onde você deseja executar o aplicativo. |
   |  $subscriptionID |Insira sua ID da assinatura do Azure. |
   |  $sourceDataPath |O local do armazenamento de Blob do Azure é onde as consultas de Hive lerão os dados. Não é necessário alterar essa variável. |
   |  $outputPath |O local de armazenamento de Blob do Azure onde as consultas de Hive produzirão os resultados. Não é necessário alterar essa variável. |
   |  $hqlScriptFile |O local e o nome de arquivo do arquivo de script HiveQL. Não é necessário alterar essa variável. |
4. Pressione **F5** para executar o script. Se você tiver problemas, como alternativa, selecione todas as linhas e então pressione **F8**.
5. Você deverá ver a mensagem "Concluído!" no final da saída. Qualquer mensagem de erro será exibida em vermelho.

Como procedimento de validação, você pode verificar o arquivo de saída, **/tutorials/twitter/twitter.hql**, em seu armazenamento de blob do Azure usando um gerenciador de armazenamento do Azure ou o PowerShell do Azure. Para um exemplo de script do Windows PowerShell para listagem de arquivos, consulte [Usar o Armazenamento de Blobs com o HDInsight][hdinsight-storage-powershell].

## <a name="process-twitter-data-by-using-hive"></a>Processar os dados do Twitter usando o Hive
Você concluiu todo o trabalho de preparação. Agora, você pode chamar o script do Hive e verificar os resultados.

### <a name="submit-a-hive-job"></a>Enviar um trabalho do Hive
Use o seguinte script do Windows PowerShell para executar o script do Hive. Você precisará definir a primeira variável.

> [!NOTE]
> Para usar os tweets e o script HiveQL carregado nas duas últimas seções, defina $hqlScriptFile para "/tutorials/twitter/twitter.hql". Para usar os que foram carregados em um blob público para você, defina $hqlScriptFile como "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"
$httpUserName = "admin"
$httpUserPassword = "<HDInsight Cluster HTTP User Password>"

#use one of the following
$hqlScriptFile = "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
$hqlScriptFile = "/tutorials/twitter/twitter.hql"

$statusFolder = "/tutorials/twitter/jobstatus"
#endregion

$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value

$defaultBlobContainerName = $myCluster.DefaultStorageContainer

#region - Invoke Hive
Write-Host "Invoke Hive ... " -ForegroundColor Green

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential
$response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable

Write-Host "Display the standard error log ... " -ForegroundColor Green
$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
#endregion
```

### <a name="check-the-results"></a>Verificar os resultados
Use o seguinte script do Windows PowerShell para verificar a saída de trabalho do Hive. Você precisará definir as duas primeiras variáveis.

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"

$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
#endregion

#region - Create an Azure storage context object
Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
$defaultBlobContainerName = $myCluster.DefaultStorageContainer

Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

Write-Host "Create a context object ... " -ForegroundColor Green
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
#endregion

#region - Download blob and display blob
Write-Host "Download the blob ..." -ForegroundColor Green
cd $HOME
Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force

Write-Host "Display the output ..." -ForegroundColor Green
Write-Host "==================================" -ForegroundColor Green
cat "./$blob"
Write-Host "==================================" -ForegroundColor Green
#end region
```

> [!NOTE]
> A tabela Hive usa \001 como o delimitador de campo. O delimitador não é visível na saída.

Depois que os resultados da análise tiverem sido colocados no armazenamento de BLOBs do Azure, é possível exportar os dados para um banco de dados SQL/SQL Server do Azure, exportar os dados para o Excel usando Power Query ou conectar seu aplicativo aos dados usando o Driver ODBC do Hive. Para obter mais informações, consulte [Usar Sqoop com HDInsight][hdinsight-use-sqoop], [Analisar os dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-delay-data], [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query] e [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][hdinsight-hive-odbc].

## <a name="next-steps"></a>Próximas etapas
Neste tutorial vimos como transformar o conjunto de dados não estruturado JSON em tabela estruturada do Hive para consultar, explorar e analisar dados do Twitter usando o HDInsight no Azure. Para obter mais informações, consulte:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Analisar dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-delay-data]
* [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query]
* [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][hdinsight-hive-odbc]
* [Use o Sqoop com o HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]:hdinsight-hadoop-use-blob-storage.md#access-blobs-using-azure-powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
