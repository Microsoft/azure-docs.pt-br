---
title: Junções em Apache Hive clientes potenciais para o erro OutOfMemory-Azure HDInsight
description: Lidando com erros de OutOfMemory "limite de sobrecarga de GC excedido"
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0c396cde38d8cba8e1f3eaf8527429647868a0c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935955"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Cenário: junções em Apache Hive leva a um erro de OutOfMemory no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O comportamento padrão para junções de Apache Hive é carregar todo o conteúdo de uma tabela na memória para que uma junção possa ser executada sem a necessidade de executar uma etapa de mapeamento/redução. Se a tabela do hive for muito grande para caber na memória, a consulta poderá falhar.

## <a name="cause"></a>Causa

Ao executar junções no hive do tamanho suficiente, o seguinte erro é encontrado:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Resolução

Impedir que o hive carregue tabelas na memória em junções (em vez de executar uma etapa de mapeamento/redução) definindo o seguinte valor de configuração do hive:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Próximas etapas

Se a configuração desse valor não resolver o problema, visite um dos seguintes...

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).