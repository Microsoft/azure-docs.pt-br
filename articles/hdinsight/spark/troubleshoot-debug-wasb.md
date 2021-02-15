---
title: Depurar operações de arquivo WASB no Azure HDInsight
description: Descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f0f06c81906116dc278377cf9fd8871e8899a1d1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938745"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Depurar operações de arquivo WASB no Azure HDInsight

Há ocasiões em que você talvez queira entender quais operações o driver WASB iniciou com o armazenamento do Azure. Para o lado do cliente, o driver WASB produz logs para cada operação do sistema de arquivos no nível de **depuração** . O driver WASB usa Log4J para controlar o nível de log e o padrão é o nível de **informações** . Para logs de análise do lado do servidor do armazenamento do Azure, consulte [registro em log da análise de armazenamento do Azure](../../storage/common/storage-analytics-logging.md).

Um log produzido será semelhante a:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Ativar o log de depuração do WASB para operações de arquivo

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` , onde `CLUSTERNAME` é o nome do cluster do Spark.

1. Navegue até **avançado spark2-Log4J-Properties**.

    1. Modifique `log4j.appender.console.Threshold=INFO` para `log4j.appender.console.Threshold=DEBUG` .

    1. Adicione `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Navegue até **avançado livy2-Log4J-Properties**.

    Adicione `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Salve as alterações.

## <a name="additional-logging"></a>Registro em log adicional

Os logs acima devem fornecer uma compreensão de alto nível das operações do sistema de arquivos. Se os logs acima ainda não fornecerem informações úteis ou se você quiser investigar chamadas de API de armazenamento de BLOBs, adicione `fs.azure.storage.client.logging=true` ao `core-site` . Essa configuração habilitará os logs do SDK do Java para o driver de armazenamento WASB e imprimirá cada chamada para o servidor de armazenamento de BLOBs. Remova a configuração após as investigações porque ela pode preencher o disco rapidamente e pode retardar o processo.

Se o back-end for Azure Data Lake baseado, use a seguinte configuração Log4J para o componente (por exemplo, Spark/tez/HDFS):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Procure os logs em `/var/log/adl/adl.log` para os logs.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).