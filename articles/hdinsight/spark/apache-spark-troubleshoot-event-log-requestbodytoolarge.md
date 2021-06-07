---
title: Erro de RequestBodyTooLarge do aplicativo Apache Spark-Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge aparece no log para o aplicativo de streaming Apache Spark no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 73ae646cb083841ee1d55b6c7ce6af7180cef08e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929433"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "aparece no log do aplicativo Apache Spark streaming no HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O erro: `NativeAzureFileSystem ... RequestBodyTooLarge` aparece no log de driver para um aplicativo de Apache Spark streaming.

## <a name="cause"></a>Causa

O arquivo de log de eventos do Spark provavelmente está atingindo o limite de comprimento do arquivo para WASB.

No Spark 2,3, cada aplicativo Spark gera um arquivo de log de eventos do Spark. O arquivo de log de eventos do Spark para um aplicativo de streaming do Spark continua crescendo enquanto o aplicativo está em execução. Hoje, um arquivo em WASB tem um limite de bloco de 50000 e o tamanho de bloco padrão é 4 MB. Portanto, na configuração padrão, o tamanho máximo do arquivo é 195 GB. No entanto, o armazenamento do Azure aumentou o tamanho máximo do bloco para 100 MB, o que efetivamente trouxe o limite de arquivo único para 4,75 TB. Para obter mais informações, confira [Escalabilidade e metas de desempenho do Armazenamento de Blobs](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Resolução

Há três soluções disponíveis para este erro:

* Aumente o tamanho do bloco para até 100 MB. Na interface do usuário do amAmbari, modifique a propriedade de configuração HDFS `fs.azure.write.request.size` (ou crie-a na `Custom core-site` seção). Defina a propriedade como um valor maior, por exemplo: 33554432. Salve a configuração atualizada e reinicie os componentes afetados.

* Pare periodicamente e envie novamente o trabalho de streaming do Spark.

* Use o HDFS para armazenar logs de eventos do Spark. Usar o HDFS para armazenamento pode resultar em perda de dados de evento do Spark durante o dimensionamento do cluster ou atualizações do Azure.

    1. Faça alterações em `spark.eventlog.dir` e `spark.history.fs.logDirectory` por meio da interface do usuário do amAmbari:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Criar diretórios no HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Reinicie todos os serviços afetados por meio da interface do usuário do Ambari.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]