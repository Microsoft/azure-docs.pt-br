---
title: Apache Hive exibição atinge o tempo limite do resultado da consulta-Azure HDInsight
description: Apache Hive exibição expira ao buscar um resultado de consulta no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930724"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Cenário: Apache Hive exibição expira ao buscar um resultado de consulta no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar determinadas consultas na exibição de Apache Hive, o seguinte erro pode ser encontrado:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Causa

O valor de tempo limite padrão de exibição do hive pode não ser adequado para a consulta que você está executando. O período de tempo especificado é muito curto para a exibição do hive buscar o resultado da consulta.

## <a name="resolution"></a>Resolução

Aumente os tempos limite de exibição do hive do Apache Ambari definindo as propriedades a seguir no `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

O valor de `HIVE_VIEW_INSTANCE_NAME` está disponível no final da URL de exibição do hive.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]