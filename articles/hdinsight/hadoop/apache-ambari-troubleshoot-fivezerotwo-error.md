---
title: Erro do Apache Ambari UI 502 no Azure HDInsight
description: Erro do Apache Ambari UI 502 ao tentar acessar o cluster HDInsight do Azure
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 750232b19bd5ef0674a9df79fdf3972a679eda7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946789"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Cenário: erro do Apache Ambari UI 502 no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao tentar acessar a interface do usuário do Apache Ambari para seu cluster HDInsight, você receberá uma mensagem semelhante a: "502-o servidor Web recebeu uma resposta inválida ao atuar como um gateway ou servidor proxy."

## <a name="cause"></a>Causa

Em geral, o código de status HTTP 502 significa que o servidor Ambari não está sendo executado corretamente no cabeçalho ativo. Há algumas causas raiz possíveis.

## <a name="resolution"></a>Resolução

Na maioria dos casos, para atenuar o problema, você pode reiniciar o cabeçalho ativo. Ou escolha um tamanho maior de VM para seu cabeçalho.

### <a name="ambari-server-failed-to-start"></a>Falha ao iniciar o servidor Ambari

Você pode verificar os logs de ambari-Server para descobrir por que o Ambari Server falhou ao iniciar. Um motivo comum é o erro de verificação de consistência do banco de dados. Você pode descobrir isso neste arquivo de log: `/var/log/ambari-server/ambari-server-check-database.log` .

Se você fez modificações no nó do cluster, desfaça-as. Sempre use a interface do usuário do Ambari para modificar as configurações relacionadas ao Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari Server realizando 100% de utilização da CPU

Em raras situações, vimos que o processo ambari-Server vem perto de 100% da utilização da CPU constantemente. Como uma mitigação, você pode fazer ssh no cabeçalho ativo e eliminar o processo do servidor Ambari e iniciá-lo novamente.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Servidor Ambari eliminado por Oom-Killer

Em alguns cenários, o cabeçalho fica sem memória e o Linux Oom-Killer começa a escolher os processos a serem eliminados. Você pode verificar essa situação pesquisando a ID do processo AmbariServer, que não deve ser encontrada. Em seguida, examine seu `/var/log/syslog` e procure algo assim:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Em seguida, identifique quais processos estão usando lembranças e tente uma maior causa raiz.

### <a name="other-issues-with-ambari-server"></a>Outros problemas com o Ambari Server

Raramente o servidor Ambari não pode lidar com a solicitação de entrada, você pode encontrar mais informações examinando os logs do Ambari-Server para qualquer erro. Um desses casos é um erro como este:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]