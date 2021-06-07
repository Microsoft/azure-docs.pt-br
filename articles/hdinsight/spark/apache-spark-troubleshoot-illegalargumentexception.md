---
title: Erro IllegalArgumentException para o Apache Spark-Azure HDInsight
description: IllegalArgumentException para atividade de Apache Spark no Azure HDInsight para Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 429659d605cdaf8aad978841e486a17da321cce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929391"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Cenário: IllegalArgumentException para a atividade de Apache Spark no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe a seguinte exceção ao tentar executar uma atividade do Spark em um pipeline de Azure Data Factory:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Causa

Um trabalho do Spark falhará se o arquivo JAR do aplicativo não estiver localizado no armazenamento padrão/primário do cluster do Spark.

Esse é um problema conhecido com a estrutura de código-fonte aberto do Spark rastreada nesse bug: [falha no trabalho do Spark se FS. defaultFS e aplicativo jar forem URLs diferentes](https://issues.apache.org/jira/browse/SPARK-22587).

Esse problema foi resolvido no Spark 2.3.0.

## <a name="resolution"></a>Resolução

Verifique se o JAR do aplicativo está armazenado no armazenamento padrão/primário do cluster HDInsight. No caso de Azure Data Factory, verifique se o serviço vinculado do ADF está apontado para o contêiner padrão do HDInsight em vez de um contêiner secundário.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]