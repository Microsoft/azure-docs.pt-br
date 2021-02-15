---
title: O servidor Apache ZooKeeper não consegue formar um quórum no Azure HDInsight
description: O servidor Apache ZooKeeper não consegue formar um quórum no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 3301d00dce6feb00edcb70ba9edfedcce2e31ec9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929208"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>O servidor Apache ZooKeeper não consegue formar um quórum no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas relacionados ao Zookeepers nos clusters do Azure HDInsight.

## <a name="symptoms"></a>Sintomas

* Ambos os gerenciadores de recursos ficam no modo de espera
* Ambos os namenodes ficam no modo de espera
* Trabalhos do Spark, Hive e Yarn ou consultas do Hive falham devido a falhas de conexão do Zookeeper
* Os daemons do LLAP não são iniciados no Spark seguro nem em clusters do Hive interativo e seguro

## <a name="sample-log"></a>Log de exemplo

Você poderá ver uma mensagem de erro semelhante à seguinte nos logs do yarn (/var/log/Hadoop-yarn/yarn/yarn-yarn *. log no cabeçalho):

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>Problemas relacionados

* Serviços de alta disponibilidade como Yarn, NameNode e Livy podem ficar inativos por vários motivos.
* Confirme nos logs que o problema está relacionado a conexões do Zookeeper
* Garanta que o problema ocorra repetidamente (não use essas soluções em casos únicos)
* Os trabalhos podem falhar temporariamente devido a problemas de conexão do Zookeeper

## <a name="common-causes-for-zookeeper-failure"></a>Causas comuns de falha do Zookeeper

* Alto uso da CPU nos servidores Zookeeper
  * Na interface do usuário do Ambari, se você vir o uso de CPU sustentado de quase 100% nos servidores Zookeeper, as sessões do Zookeeper abertas durante esse tempo poderão expirar e atingir o tempo limite
* Os clientes Zookeeper relatam o atingimento frequente de tempos limite
  * Nos logs do Resource Manager, namenode e outros, você verá tempos limite de conexão do cliente frequentes
  * Isso pode resultar em perda de quórum, failovers frequentes e outros problemas

## <a name="check-for-zookeeper-status"></a>Verificar o status do Zookeeper

* Localizar os servidores Zookeeper com base no arquivo /etc/hosts ou na interface do usuário do Ambari
* Executar o seguinte comando
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (ou 2182)  
  * A porta 2181 é a instância do Apache Zookeeper
  * A porta 2182 é usada pelo Zookeeper do HDInsight (para fornecer HA para serviços que não sejam de alta disponibilidade nativa)
  * Se o comando não mostrar nenhuma saída, isso significa que os servidores Zookeeper não estão em execução
  * Se os servidores estiverem em execução, o resultado incluirá estatísticas de conexões de cliente, entre outras estatísticas

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>Picos de carga da CPU a cada hora

* Faça logon no servidor Zookeeper e verifique o /etc/crontab
* Se no momento houver trabalhos por hora em execução, torne aleatório a hora de início em diferentes servidores Zookeeper.
  
## <a name="purging-old-snapshots"></a>Limpar instantâneos antigos

* Os Zookeepers são configurados para limpar automaticamente os instantâneos antigos
* Por padrão, os últimos 30 instantâneos são retidos
* O número de instantâneos retidos é controlado pela chave de configuração `autopurge.snapRetainCount`. Essa propriedade pode ser localizada nos seguintes arquivos:
  * `/etc/zookeeper/conf/zoo.cfg` para Zookeeper do Hadoop
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` para Zookeeper do HDInsight
* Defina `autopurge.snapRetainCount` como 3 e reinicie os servidores Zookeeper
  * A configuração do Zookeeper do Hadoop pode ser atualizada, e o serviço pode ser reiniciado por meio do Ambari
  * Parar e reiniciar o Zookeeper do HDInsight manualmente
    * O `sudo lsof -i :2182` fornecerá a ID do processo a ser eliminado
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Não limpe os instantâneos manualmente: a exclusão manual de instantâneos pode resultar em perda de dados

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>CancelledKeyException no log do servidor Zookeeper não requer limpeza de instantâneo

* Essa exceção será vista nos servidores Zookeeper (arquivos/var/log/Zookeeper/Zookeeper-Zookeeper-* ou/var/log/hdinsight-Zookeeper/Zookeeper *)
* Essa exceção normalmente significa que o cliente não está mais ativo, e o servidor não consegue enviar uma mensagem
* Essa exceção também indica que o cliente Zookeeper está encerrando as sessões prematuramente
* Procure os outros sintomas descritos neste documento

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).