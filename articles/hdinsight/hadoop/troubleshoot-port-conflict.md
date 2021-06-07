---
title: Conflito de porta ao iniciar serviços no Azure HDInsight
description: Etapas de solução de problemas e possíveis resoluções para problemas de conflito de porta ao interagir com clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f42e84d5d9c1dd49d9bf5604fe2f967eae0b6276
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943094"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>Cenário: conflito de porta ao iniciar serviços no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Um serviço não é iniciado.

## <a name="cause"></a>Causa

Existe um conflito de porta.

## <a name="resolution"></a>Resolução

### <a name="method-1"></a>Método 1

Use os comandos abaixo para obter/eliminar todos os processos em execução, que são afetados pelo problema de porta.

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

Em seguida, inicie o serviço.

### <a name="method-2"></a>Método 2

Reinicialize o nó.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).