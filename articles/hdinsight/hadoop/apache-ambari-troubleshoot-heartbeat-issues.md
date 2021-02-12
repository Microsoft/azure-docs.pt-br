---
title: Problemas de pulsação do Apache Ambari no Azure HDInsight
description: Revisão de vários motivos para problemas de pulsação do Apache Ambari no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 5eebde42098d74f533565d274b693c4a06f2f60d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946726"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemas de pulsação do Apache Ambari no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Cenário: alta utilização da CPU

### <a name="issue"></a>Problema

O agente Ambari tem alta utilização da CPU, o que resulta em alertas da interface do usuário do Ambari que, para alguns nós, a pulsação do agente do Ambari é perdida. O alerta de pulsação perdida geralmente é transitório.

### <a name="cause"></a>Causa

Devido a vários bugs do ambari Agent, em casos raros, o ambari-Agent pode ter alta utilização de CPU de percentual (perto de 100).

### <a name="resolution"></a>Resolução

1. Identificar a ID do processo (PID) do ambari-Agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Em seguida, execute o seguinte comando para mostrar a utilização da CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Reinicie o ambari-Agent para atenuar o problema:

    ```bash
    service ambari-agent restart
    ```

1. Se a reinicialização não funcionar, encerre o processo do ambari e inicie-o:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Cenário: agente Ambari não iniciado

### <a name="issue"></a>Problema

O agente Ambari não foi iniciado, o que resulta em alertas da interface do usuário do Ambari que, para alguns nós, a pulsação do agente do Ambari é perdida.

### <a name="cause"></a>Causa

Os alertas são causados pelo agente do Ambari não estar em execução.

### <a name="resolution"></a>Resolução

1. Confirme o status do ambari-Agent:

    ```bash
    service ambari-agent status
    ```

1. Confirme se os serviços do controlador de failover estão em execução:

    ```bash
    ps -ef | grep failover
    ```

    Se os serviços do controlador de failover não estiverem em execução, provavelmente devido a um problema, impeça que o hdinsight-Agent inicie o controlador de failover. Verifique o log do hdinsight-Agent do `/var/log/hdinsight-agent/hdinsight-agent.out` arquivo.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Cenário: pulsação perdida para Ambari

### <a name="issue"></a>Problema

O agente de pulsação Ambari foi perdido.

### <a name="cause"></a>Causa

Os logs do OMS estão causando alta utilização da CPU.

### <a name="resolution"></a>Resolução

* Desabilite o log de Azure Monitor usando o cmdlet [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) do PowerShell.
* Excluir o `mdsd.warn` arquivo de log

---

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]