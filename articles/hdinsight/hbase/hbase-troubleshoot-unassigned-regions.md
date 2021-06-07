---
title: Problemas com servidores de região no Azure HDInsight
description: Problemas com servidores de região no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 968a0c6e1717245171bf84821a58cad4e440046e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936620"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemas com servidores de região no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Cenário: regiões não atribuídas

### <a name="issue"></a>Problema

Ao executar `hbase hbck` o comando, você verá uma mensagem de erro semelhante a:

```
multiple regions being unassigned or holes in the chain of regions
```

Na interface do usuário do Apache HBase Master, você pode ver o número de regiões desbalanceadas em todos os servidores de região. Depois, você pode executar o comando `hbase hbck` para ver os espaços na cadeia de regiões.

### <a name="cause"></a>Causa

Os orifícios podem ser o resultado de regiões offline.

### <a name="resolution"></a>Resolução

Corrija as atribuições. Siga as etapas abaixo para exibir as regiões não atribuídas de volta no estado normal:

1. Entre no cluster HBase do HDInsight usando SSH.

1. Execute o `hbase zkcli` comando para se conectar ao shell do ZooKeeper.

1. Executar `rmr /hbase/regions-in-transition` ou `rmr /hbase-unsecure/regions-in-transition` comando.

1. Saia do shell do Zookeeper usando o `exit` comando.

1. Abra a interface do usuário do Apache Ambari e reinicie o serviço Active HBase Master.

1. Execute o `hbase hbck` comando novamente (sem nenhuma outra opção). Verifique a saída e verifique se todas as regiões estão sendo atribuídas.

---

## <a name="scenario-dead-region-servers"></a>Cenário: servidores de região inoperante

### <a name="issue"></a>Problema

Falha ao iniciar os servidores de região.

### <a name="cause"></a>Causa

Vários diretórios WAL de divisão.

1. Obter lista de WALs atuais: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` .

1. Inspecione o `wals.out` arquivo. Se houver muitos diretórios de divisão (começando com *-divisão), o servidor de região provavelmente falhará devido a esses diretórios.

### <a name="resolution"></a>Resolução

1. Interrompa o HBase do portal do Ambari.

1. Execute `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` para obter uma lista atualizada de WALs.

1. Mova os diretórios * de divisão para uma pasta temporária, `splitWAL` e exclua os diretórios de divisão *.

1. Execute o `hbase zkcli` comando para se conectar ao shell do Zookeeper.

1. Execute `rmr /hbase-unsecure/splitWAL`.

1. Reinicie o serviço HBase.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).