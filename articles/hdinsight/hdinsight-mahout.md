---
title: "Gerar recomendações usando o HDInsight para Mahout do PowerShell – Azure | Microsoft Docs"
description: "Saiba como usar a biblioteca de Machine Learning do Apache Mahout para gerar recomendações de vídeos com o HDInsight (Hadoop) usando o script de PowerShell em execução no seu cliente."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: larryfr
ms.openlocfilehash: 934de9ca2df48b29ef7a56d5729d59d77875ea7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Gerar recomendações de vídeo usando o Apache Mahout com o Hadoop no HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como usar a biblioteca de aprendizado de máquina do [Apache Mahout](http://mahout.apache.org) com o Azure HDInsight para gerar recomendações de vídeos. O exemplo neste documento usa o Azure PowerShell para executar trabalhos do Mahout.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um cluster HDInsight baseado em Linux. Para saber mais sobre como criar um, confira [Introdução ao uso do Hadoop baseado em Linux no HDInsight][getstarted].

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [PowerShell do Azure](/powershell/azure/overview)

## <a name="recommendations"></a>Gerar recomendações usando o Azure PowerShell

> [!WARNING]
> O trabalho nesta seção funciona usando o Azure PowerShell. Muitas das classes fornecidas com o Mahout não funcionam com o Azure PowerShell no momento. Para obter uma lista de classes que não funcionam com o Azure PowerShell, confira a seção [Solucionar problemas](#troubleshooting).
>
> Para obter um exemplo de como usar o SSH para se conectar ao HDInsight e executar exemplos do Mahout diretamente no cluster, confira [Gerar recomendações de filme usando o Mahout e o HDInsight (SSH)](hdinsight-hadoop-mahout-linux-mac.md).

Uma das funções oferecidas pelo Mahout é um mecanismo de recomendação. Ele aceita dados no formato de `userID`, `itemId` e `prefValue` (os usuários escolhem o item de sua preferência). O Mahout usa dados para determinar os usuários com preferências de item similares, o que pode ser utilizado para fazer recomendações.

O exemplo a seguir é uma apresentação simplificada de como funciona o processo de recomendação:

* **Coocorrência**: Joe, Alice e Bob gostaram de *Guerra nas Estrelas*, *O Império Contra-ataca* e *O Retorno de Jedi*. O Mahout determina que usuários que gostam de qualquer um desses filmes também gostam dos outros dois.

* **Co-ocorrência**: Bob e Alice também gostaram de *A Ameaça Fantasma*, *A Guerra dos Clones* e *A Vingança dos Sith*. O Mahout determina que usuários gostaram dos três filmes citados anteriormente também gostam desses filmes.

* **Recomendação por similaridade**: já que Joe gostou dos primeiros três, o Mahout pesquisará os filmes que outros com preferências similares gostaram, mas que Joe não assistiu (curtiu/classificou). Nesse caso, o Mahout recomendaria *A Ameaça Fantasma*, *A Guerra dos Clones* e *A Vingança dos Sith*.

### <a name="understanding-the-data"></a>Compreendendo os dados

A [GroupLens Research][movielens] oferece dados de classificação para filmes em um formato compatível com o Mahout. Esses dados estão disponíveis no armazenamento padrão do cluster em `/HdiSamples//HdiSamples/MahoutMovieData`.

Existem dois arquivos, `moviedb.txt` (informações sobre filmes) e `user-ratings.txt`. O arquivo `user-ratings.txt` é usado durante a análise. O arquivo `moviedb.txt` é usado para fornecer um texto amigável ao exibir os resultados da análise.

Os dados contidos em user-ratings.txt têm uma estrutura de `userID`, `movieID`, `userRating` e `timestamp`, que informa a classificação que cada usuário deu a um filme. Aqui está um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Executar o trabalho

Use o script do Windows PowerShell a seguir para executar um trabalho usando o mecanismo de recomendação do Mahout com os dados de filmes:

> [!NOTE]
> Esse arquivo solicita informações que são usadas para conectar-se ao cluster HDInsight e executar trabalhos. Podem ser necessários vários minutos para os trabalhos serem concluídos e para baixar o arquivo output.txt.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Os trabalhos do Mahout não removem dados temporários criados durante o processamento do trabalho. O parâmetro `--tempDir` é especificado no trabalho de exemplo para isolar os arquivos temporários em um caminho específico.

O trabalho do Mahout não retorna a saída para STDOUT. Em vez disso, ele armazena no diretório de saída especificado como **part-r-00000**. O script baixa esse arquivo em **output.txt** no diretório atual em sua estação de trabalho.

O texto a seguir é um exemplo do conteúdo deste arquivo:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

A primeira coluna é a `userID`. Os valores contidos em '[' and ']' são `movieId`:`recommendationScore`.

O script também baixa os arquivos `moviedb.txt` e `user-ratings.txt`, que são necessários para formatar a saída para ser mais legível.

### <a name="view-the-output"></a>Exibir a saída

Apesar de a saída gerada talvez ser adequada para uso em um aplicativo, ela não é amigável. O `moviedb.txt` do servidor pode ser usado para resolver o `movieId` para um nome de filme. Use o seguinte script do PowerShell para exibir recomendações com nomes de filme:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Use o seguinte comando para exibir as recomendações em um formato amigável: 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

A saída é semelhante ao texto a seguir:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="cannot-overwrite-files"></a>Não foi possível substituir os arquivos

Os trabalhos do Mahout não limpam arquivos temporários criados durante o processamento. Além disso, os trabalhos não substituirão um arquivo de saída existente.

Para evitar erros ao executar trabalhos do Mahout, exclua os arquivos temporários e de saída entre as execuções. Use o seguinte script do PowerShell para remover os arquivos criados pelos scripts anteriores neste documento:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Classes que não funcionam com o Azure PowerShell

Os trabalhos do Mahout que usam as classes a seguir retornarão várias mensagens de erro se utilizados no Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Para executar trabalhos que usam essas classes, conecte-se ao cluster HDInsight usando SSH e execute os trabalhos usando a linha de comando. Para obter um exemplo de como usar o SSH para executar trabalhos do Mahout, confira [Gerar recomendações de filmes usando o Mahout e o HDInsight (SSH)](hdinsight-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar o Mahout, descubra outras maneiras de trabalhar com dados no HDInsight:

* [Hive com o HDInsight](hdinsight-use-hive.md)
* [Pig com o HDInsight](hdinsight-use-pig.md)
* [MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
