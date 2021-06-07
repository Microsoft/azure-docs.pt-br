---
title: Use o MapReduce e o PowerShell com o Apache Hadoop - Azure HDInsight
description: Aprenda a usar o PowerShell para executar trabalhos do MapReduce remotamente com o Apache Hadoop no HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 16c6c5e317591b70c3a1300453093fc715e213fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939679"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Executar tarefas do MapReduce com o Apache Hadoop no HDInsight usando o PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Esse documento fornece um exemplo de uso do PowerShell do Azure para executar um trabalho MapReduce em um Hadoop no cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Confira [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* O [Módulo Az](/powershell/azure/) do PowerShell instalado.

## <a name="run-a-mapreduce-job"></a>Executar um trabalho MapReduce

O PowerShell do Azure fornece *cmdlets* que permitem executar remotamente trabalhos MapReduce no HDInsight. Internamente, o PowerShell faz chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente chamado de Templeton) em execução no cluster HDInsight.

Os cmdlets a seguir são usados ao executar trabalhos MapReduce em um cluster HDInsight remoto.

|Cmdlet | Descrição |
|---|---|
|Connect-AzAccount|Autentica o Azure PowerShell para a assinatura do Azure.|
|New-AzHDInsightMapReduceJobDefinition|Cria uma nova *definição de trabalho*, usando as informações do MapReduce especificadas.|
|Start-AzHDInsightJob|Envia a definição de trabalho para HDInsight e inicia o trabalho. Um objeto *job* é retornado.|
|Wait-AzHDInsightJob|Usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou o tempo de espera seja excedido.|
|Get-AzHDInsightJobOutput|Usado para recuperar a saída do trabalho.|

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight.

1. Usando um editor, salve o código a seguir como **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Abra um novo prompt de comando do **PowerShell do Azure** . Altere os diretórios para o local do arquivo **mapreducejob.ps1** e use o seguinte comando para executar o script:

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    Ao executar o script, você será solicitado a fornecer o nome do cluster HDInsight e o logon do cluster. Você também poderá receber uma solicitação para autenticar a sua assinatura do Azure.

3. Quando o trabalho for concluído, você receberá uma saída semelhante ao seguinte texto:

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    Essa saída indica que o trabalho foi concluído com êxito.

    > [!NOTE]  
    > Se o **ExitCode** for um valor diferente de 0, consulte [Solução de problemas](#troubleshooting).

    Este exemplo também armazena os arquivos baixados em um arquivo **output.txt** no diretório do qual você executa o script.

### <a name="view-output"></a>Exibir saída

Para ver as palavras e contagens produzidas pelo trabalho, abra o arquivo **output.txt** em um editor de texto.

> [!NOTE]  
> Os arquivos de saída de um trabalho MapReduce são imutáveis. Portanto, se você executar esse exemplo novamente, será necessário alterar o nome do arquivo de saída.

## <a name="troubleshooting"></a>Solução de problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os erros para o trabalho. Para exibir informações de erro para esse trabalho, adicione o seguinte comando ao final do arquivo de **mapreducejob.ps1** . Em seguida, salve o arquivo e execute o script novamente.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Esse cmdlet retorna as informações gravadas em STDERR conforme o trabalho é executado.

## <a name="next-steps"></a>Próximas etapas

Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar trabalhos MapReduce em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída. Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)
* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)