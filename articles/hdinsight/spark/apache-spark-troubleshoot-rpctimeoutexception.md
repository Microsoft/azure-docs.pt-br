---
title: RpcTimeoutException para Apache Spark Thrift-Azure HDInsight
description: Você verá erros 502 ao processar grandes conjuntos de dados usando Apache Spark servidor thrift
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: c2975599272474fed9d61702fc1dbceb946c1190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932700"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Cenário: RpcTimeoutException para o servidor de Apache Spark Thrift no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O aplicativo Spark falha com uma `org.apache.spark.rpc.RpcTimeoutException` exceção e uma mensagem: `Futures timed out` , como no exemplo a seguir:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` e `overhead limit exceeded` os erros também podem aparecer no `sparkthriftdriver.log` como no exemplo a seguir:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Esses erros são causados por uma falta de recursos de memória durante o processamento de dados. Se o processo de coleta de lixo do Java for iniciado, ele poderá levar ao aplicativo Spark para parar de responder. As consultas começarão a atingir o tempo limite e interromperão o processamento. O `Futures timed out` erro indica um cluster sob sobrecarga grave.

## <a name="resolution"></a>Resolução

Aumente o tamanho do cluster adicionando mais nós de trabalho ou aumentando a capacidade de memória dos nós de cluster existentes. Você também pode ajustar o pipeline de dados para reduzir a quantidade de dados que estão sendo processados de uma vez.

O `spark.network.timeout` controla o tempo limite para todas as conexões de rede. Aumentar o tempo limite da rede pode permitir mais tempo para que algumas operações críticas sejam concluídas, mas isso não resolverá completamente o problema.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]