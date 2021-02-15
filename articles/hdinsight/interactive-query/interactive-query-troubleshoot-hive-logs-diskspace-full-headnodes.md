---
title: 'Solução de problemas: logs de Apache Hive preencher espaço em disco – Azure HDInsight'
description: Este artigo fornece etapas de solução de problemas a serem seguidas quando Apache Hive logs estiverem preenchendo o espaço em disco nos nós de cabeçalho no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.date: 10/05/2020
ms.openlocfilehash: cd7e6a7f13f6cccb5be5d23d69c2a44fc655cf55
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930958"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: os logs de Apache Hive estão preenchendo o espaço em disco nos nós de cabeçalho no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas relacionados a espaço em disco insuficiente nos nós de cabeçalho nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Em um cluster Apache Hive/LLAP, os logs indesejados estão ocupando todo o espaço em disco nos nós de cabeçalho. Essa condição pode causar os seguintes problemas:

- O acesso SSH falha porque nenhum espaço é deixado no nó principal.
- Ambari gera *erro http: serviço 503 indisponível*.
- Falha na reinicialização do HiveServer2 Interactive.

Os `ambari-agent` logs incluirão as seguintes entradas quando o problema ocorrer:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nas configurações avançadas de Log4J do hive, o agendamento de exclusão padrão atual é excluir arquivos com mais de 30 dias, com base na data da última modificação.

## <a name="resolution"></a>Resolução

1. Vá para o resumo do componente do hive no portal do Ambari e selecione a guia **configurações** .

2. Vá para a `Advanced hive-log4j` seção em **Configurações avançadas**.

3. Defina o `appender.RFA.strategy.action.condition.age` parâmetro como uma idade de sua escolha. Este exemplo definirá a idade para 14 dias: `appender.RFA.strategy.action.condition.age = 14D`

4. Se você não vir nenhuma configuração relacionada, acrescente estas configurações:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Defina `hive.root.logger` como `INFO,RFA` , conforme mostrado no exemplo a seguir. A configuração padrão é `DEBUG` , que torna os logs grandes.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Salve as configurações e reinicie os componentes necessários.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
