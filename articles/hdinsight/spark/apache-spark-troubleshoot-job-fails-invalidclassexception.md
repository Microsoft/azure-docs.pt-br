---
title: Erro de InvalidClassException da Apache Spark-Azure HDInsight
description: Apache Spark trabalho falha com InvalidClassException, incompatibilidade de versão de classe, no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 0b0889ac1e71ce33406e89ead62370a0c0168763
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929254"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark trabalho falha com InvalidClassException, incompatibilidade de versão de classe, no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você tenta criar um trabalho de Apache Spark em um cluster Spark 2. x. Ele falha com um erro semelhante ao seguinte:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Causa

Esse erro pode ser causado pela adição de um jar adicional à `spark.yarn.jars` configuração, especificamente um jar sombreado que inclui uma versão diferente do `commons-lang3` pacote e apresenta uma incompatibilidade de classe. Por padrão, o Spark 2.1/2/3 usa a versão 3,5 do `commons-lang3` .

> [!TIP]
> Para sombrear uma biblioteca, coloque seu conteúdo em seu próprio jar, alterando seu pacote. Isso é diferente de empacotar a biblioteca, que está colocando a biblioteca em seu próprio jar sem reempacotar.

## <a name="resolution"></a>Resolução

Remova o jar ou recompile o jar personalizado (AzureLogAppender) e use o [plug-in Maven-Shad-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) para realocar classes.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]