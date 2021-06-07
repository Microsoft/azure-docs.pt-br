---
title: Apache Hive erro do intérprete Zeppelin-Azure HDInsight
description: O intérprete JDBC do Zeppelin hive do Apache está apontando para a URL incorreta no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930748"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Cenário: Apache Hive intérprete Zeppelin fornece um erro Zookeeper no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Em um cluster Apache Hive LLAP, o intérprete Zeppelin fornece a seguinte mensagem de erro ao tentar executar uma consulta:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Causa

O intérprete JDBC do hive do Zeppelin está apontando para a URL errada.

## <a name="resolution"></a>Resolução

1. Navegue até o resumo do componente do hive e copie o "URL JDBC do hive" para a área de transferência.

1. Navegue até `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. Edite as configurações JDBC: Atualize o valor Hive. URL para a URL JDBC do hive copiada na etapa 1

1. Salve e repita a consulta

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]