---
title: Bindexception-o endereço já está em uso no Azure HDInsight
description: Bindexception-o endereço já está em uso no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: c0a7bc7629b5c2a2e458ba94d62e341f578fdd25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946393"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Cenário: Bindexception-o endereço já está em uso no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A operação de reinicialização em um servidor de região do Apache HBase não é concluída. No `region-server.log` diretório in `/var/log/hbase` nos nós de trabalho em que o servidor de região é iniciado, você pode ver uma mensagem de erro semelhante à seguinte:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Causa

Reiniciando servidores de região do Apache HBase durante atividade de carga de trabalho pesada. Abaixo está o que acontece nos bastidores quando um usuário inicia a operação de reinicialização no servidor de região do HBase da interface do usuário do Apache amAmbari:

1. O agente Ambari envia uma solicitação de parada para o servidor de região.

1. O agente Ambari aguarda 30 segundos para que o servidor de região seja desligado normalmente

1. Se o seu aplicativo continuar a se conectar com o servidor de região, o servidor não desligará imediatamente. O tempo limite de 30 segundos expira antes do desligamento.

1. Após 30 segundos, o agente do Ambari envia um comando (`kill -9`) de encerramento forçado ao servidor de região.

1. Devido a esse desligamento abrupta, embora o processo do servidor de região seja eliminado, a porta associada ao processo pode não ser liberada, o que eventualmente acarreta `AddressBindException` .

## <a name="resolution"></a>Resolução

Reduza a carga nos servidores de região do HBase antes de iniciar uma reinicialização. Além disso, convém primeiro liberar todas as tabelas. Para obter uma referência sobre como liberar tabelas, confira [HDInsight HBase: como melhorar o tempo de reinicialização do cluster Apache HBase liberando tabelas](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Como alternativa, tente reiniciar manualmente os servidores de região nos nós de trabalho usando os seguintes comandos:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).