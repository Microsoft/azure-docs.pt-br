---
title: Usar Apache Hive com PowerShell no HDInsight - Azure
description: Usar o PowerShell para executar consultas de Apache Hive no Apache Hadoop no Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 43cac81717027b39210d6cd9cb9ad0baa596f50d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943179"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Executar consultas do Apache Hive usando o PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Este documento fornece um exemplo de como usar Azure PowerShell para executar Apache Hive consultas em um Apache Hadoop cluster HDInsight.

> [!NOTE]  
> Esse documento não fornece uma descrição detalhada do que as instruções HiveQL que usadas nos exemplos fazem. Para obter informações sobre o HiveQL usado neste exemplo, veja [Usar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* O [Módulo Az](/powershell/azure/) do PowerShell instalado.

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive

O PowerShell do Azure fornece *cmdlets* que lhe permitem executar remotamente as consultas Hive no HDInsight. Internamente, os cmdlets fazem chamadas REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) no cluster HDInsight.

Os cmdlets a seguir são usados ao executar consultas do Hive em um cluster HDInsight remoto:

* `Connect-AzAccount`: autentica o Azure PowerShell para a sua assinatura do Azure.
* `New-AzHDInsightHiveJobDefinition`: cria uma *definição de trabalho* usando as instruções HiveQL especificadas.
* `Start-AzHDInsightJob`: envia a definição do trabalho para HDInsight e inicia o trabalho. Um objeto *job* é retornado.
* `Wait-AzHDInsightJob`: usa o objeto de trabalho para verificar o status do trabalho. Ele aguarda até que o trabalho seja concluído ou o tempo de espera seja excedido.
* `Get-AzHDInsightJobOutput`: usado para recuperar a saída do trabalho.
* `Invoke-AzHDInsightHiveJob`: usado para executar instruções de HiveQL. Esse cmdlet bloqueia a consulta até que ela seja concluída e, em seguida, retorna os resultados.
* `Use-AzHDInsightCluster`: define o cluster atual para usar o comando `Invoke-AzHDInsightHiveJob`.

As etapas a seguir demonstram como usar esses cmdlets para executar um trabalho no seu cluster HDInsight:

1. Usando um editor, salve o código a seguir como `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra um novo prompt de comando do **PowerShell do Azure** . Altere os diretórios para o local do arquivo `hivejob.ps1` e use o seguinte comando para executar o script:

    ```azurepowershell
    .\hivejob.ps1
    ```

    Quando o script for executado, você será solicitado a inserir o nome do cluster e as credenciais da conta do administrador de HTTPS/cluster. Você também pode ser solicitado a entrar em sua assinatura do Azure.

3. Quando o trabalho for concluído, ele deverá retornar informações semelhantes ao seguinte texto:

    ```output
    Display the standard output...
    2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
    2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
    2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id
    ```

4. Como mencionado anteriormente, `Invoke-Hive` pode ser usado para executar uma consulta e aguardar a resposta. Use o script a seguir para ver como funciona o Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    A saída se parece com o seguinte texto:

    ```output
    2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
    2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
    2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id
    ```

   > [!NOTE]  
   > Para consultas HiveQL mais longas, é possível usar o cmdlet **Here-Strings** do PowerShell do Azure ou arquivos de script HiveQL. O snippet a seguir mostram como usar o cmdlet `Invoke-Hive` para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado em wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para obter mais informações sobre as **cadeias de caracteres aqui**, consulte as [cadeias de caracteres aqui](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#here-strings).

## <a name="troubleshooting"></a>Solução de problemas

Se nenhuma informação for retornada quando o trabalho for concluído, exiba os logs de erro. Para exibir informações de erro para esse trabalho, adicione o seguinte ao final do arquivo `hivejob.ps1`, salve-o e execute-o novamente.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
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

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
