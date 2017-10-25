---
title: "Usar o Pig do Hadoop com o PowerShell no HDInsight – Azure | Microsoft Docs"
description: Saiba como enviar trabalhos do Pig para um cluster do Hadoop no HDInsight usando o PowerShell do Azure.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: ce088b22ca45bc40252c909697153ce9e847592e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Usar o Azure PowerShell para executar trabalhos do Pig com o HDInsight

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece um exemplo de uso do PowerShell do Azure para enviar trabalhos do Pig para um Hadoop no cluster HDInsight. O Pig permite que você escreva trabalhos do MapReduce usando uma linguagem (Pig Latin) que modela transformações de dados, em vez das funções mapear e reduzir.

> [!NOTE]
> Esse documento não fornece uma descrição detalhada do que fazem as instruções Pig Latin usadas nos exemplos. Para obter informações sobre o Pig Latin usado neste exemplo, consulte [Usar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Pré-requisitos

* **Um cluster Azure HDInsight**

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Uma estação de trabalho com o PowerShell do Azure**.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a id="powershell"></a>Executar trabalhos do Pig usando o PowerShell

O PowerShell do Azure fornece *cmdlets* que permitem executar remotamente trabalhos do Pig no HDInsight. Internamente, o PowerShell usa chamadas REST para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) em execução no cluster HDInsight.

Os seguintes cmdlets são usados ao executar trabalhos do Pig em um cluster HDInsight remoto:

* **Login-AzureRmAccount**: autentica o Azure PowerShell para sua assinatura do Azure.
* **New-AzureRmHDInsightPigJobDefinition**: cria uma *definição de trabalho* usando as instruções de Pig Latin especificadas.
* **Start-AzureRmHDInsightJob**: envia a definição do trabalho para HDInsight e inicia o trabalho. Um objeto *job* é retornado.
* **Wait-AzureRmHDInsightJob**: usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou que o tempo de espera seja excedido.
* **Get-AzureRmHDInsightJobOutput**: usado para recuperar a saída do trabalho.

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight.

1. Usando um editor, salve o código a seguir como **pigjob.ps1**.

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Abra um novo prompt de comando do Windows PowerShell. Altere os diretórios para o local do arquivo **pigjob.ps1** e use o seguinte comando para executar o script:

        .\pigjob.ps1

    Você deverá fazer logon em sua assinatura do Azure. Em seguida, você deverá fornecer o nome da conta e senha de Administrador/HTTPs do cluster HDInsight.

2. Quando o trabalho for concluído, ele deverá retornar informações semelhantes ao seguinte texto:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Solucionar problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os logs de erro. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo **pigjob.ps1** , salve o arquivo e execute-o novamente.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Esse cmdlet retorna as informações gravadas em STDERR durante o processamento do trabalho.

## <a id="summary"></a>Resumo
Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar trabalhos do Pig em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

## <a id="nextsteps"></a>Próximas etapas
Para obter informações gerais sobre o Pig no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
