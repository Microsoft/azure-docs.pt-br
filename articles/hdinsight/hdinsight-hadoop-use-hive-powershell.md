---
title: "Usar o Hive do Hadoop com o PowerShell no HDInsight – Azure | Microsoft Docs"
description: Usar o PowerShell para executar consultas Hive com Hadoop no HDInsight
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.openlocfilehash: 74571fc6e1a0b2d6a903cdd992a247f4d5dfa700
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-hive-queries-using-powershell"></a>Executar consultas Hive usando o PowerShell
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de como usar o Azure PowerShell no modo de Grupo de Recursos do Azure para executar consultas do Hive em um Hadoop no cluster HDInsight.

> [!NOTE]
> Esse documento não fornece uma descrição detalhada do que as instruções HiveQL que usadas nos exemplos fazem. Para obter informações sobre o HiveQL usado neste exemplo, veja [Usar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md).

**Pré-requisitos**

* **Um cluster Azure HDInsight**: Não importa se o cluster é baseado no Windows ou no Linux.

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Uma estação de trabalho com o PowerShell do Azure**.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-hive-queries-using-azure-powershell"></a>Executar consultas do Hive usando o Azure PowerShell

O PowerShell do Azure fornece *cmdlets* que lhe permitem executar remotamente as consultas Hive no HDInsight. Internamente, os cmdlets fazem chamadas REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) no cluster HDInsight.

Os cmdlets a seguir são usados ao executar consultas do Hive em um cluster HDInsight remoto:

* **Add-AzureRmAccount**: autentica o Azure PowerShell para a sua assinatura do Azure.
* **New-AzureRmHDInsightHiveJobDefinition**: cria uma *definição de trabalho* usando as instruções HiveQL especificadas.
* **Start-AzureRmHDInsightJob**: envia a definição do trabalho para HDInsight e inicia o trabalho. Um objeto *job* é retornado.
* **Wait-AzureRmHDInsightJob**: usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou o tempo de espera seja excedido.
* **Get-AzureRmHDInsightJobOutput**: usado para recuperar a saída do trabalho.
* **Invoke-AzureRmHDInsightHiveJob**: usado para executar instruções HiveQL. Esse cmdlet bloqueia a consulta até que ela seja concluída e, em seguida, retorna os resultados.
* **Use-AzureRmHDInsightCluster**: define o cluster atual a ser usado para o comando **Invoke-AzureRmHDInsightHiveJob**.

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight:

1. Usando um editor, salve o código a seguir como **hivejob.ps1**.

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra um novo prompt de comando do **PowerShell do Azure** . Altere os diretórios para o local do arquivo **hivejob.ps1** e use o seguinte comando para executar o script:

        .\hivejob.ps1

    Quando o script for executado, você deverá inserir o nome do cluster e as credenciais da conta do Administrador/HTTPS do cluster. Você também deverá fazer logon em sua assinatura do Azure.

3. Quando o trabalho for concluído, ele deverá retornar informações semelhantes ao seguinte texto:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Como mencionado anteriormente, **Invoke-Hive** pode ser usado para executar uma consulta e aguardar a resposta. Use o script a seguir para ver como funciona o Invoke-Hive:

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A saída se parece com o seguinte texto:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Para consultas HiveQL mais longas, é possível usar o cmdlet **Here-Strings** do PowerShell do Azure ou arquivos de script HiveQL. Os fragmentos a seguir mostram como usar o cmdlet **Invoke-Hive** para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado em wasb://.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para obter mais informações sobre **Here-Strings**, consulte <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Como usar Here-Strings do Windows PowerShell</a>.

## <a name="troubleshooting"></a>Solucionar problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os logs de erro. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo **hivejob.ps1** , salve-o e execute-o novamente.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Esse cmdlet retorna as informações gravadas em STDERR durante o processamento do trabalho.

## <a name="summary"></a>Resumo

Como você pode ver, o PowerShell do Azure fornece uma maneira fácil de executar consultas do Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

## <a name="next-steps"></a>Próximas etapas

Para obter informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
