---
title: Exceção de armazenamento após a redefinição de conexão no Azure HDInsight
description: Exceção de armazenamento após a redefinição de conexão no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: 82cad7fc68d650e5f525a8722d3e2f3e9865f456
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936741"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Cenário: exceção de armazenamento após a redefinição de conexão no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível criar uma nova tabela do Apache HBase.

## <a name="cause"></a>Causa

Durante um processo de truncamento de tabela, houve um problema de conexão de armazenamento. A entrada da tabela foi excluída na tabela de metadados do HBase. Todos, exceto um arquivo de BLOB, foram excluídos.

Embora não haja nenhum blob de pasta chamado `/hbase/data/default/ThatTable` sentado no armazenamento. O driver WASB encontrou a existência do arquivo de blob acima e não permitiria criar nenhum blob chamado `/hbase/data/default/ThatTable` porque ele assumiu que as pastas pai existiam, portanto, a criação da tabela falhará.

## <a name="resolution"></a>Resolução

1. Na interface do usuário do Apache Ambari, reinicie o HMaster ativo. Isso permitirá que um dos dois HMaster em espera se torne o ativo e o novo HMaster ativo recarregará as informações da tabela de metadados. Portanto, você não verá a `already-deleted` tabela na interface do usuário do amHMaster.

1. Você pode encontrar o arquivo de blob órfão nas ferramentas de interface do usuário, como o Cloud Explorer ou executando o comando como `hdfs dfs -ls /xxxxxx/yyyyy` . Execute `hdfs dfs -rmr /xxxxx/yyyy` para excluir esse BLOB. Por exemplo, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Agora você pode criar uma nova tabela com o mesmo nome no HBase.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).