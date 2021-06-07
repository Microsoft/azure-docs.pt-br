---
title: API REST para consultar o Apache HBase no Azure HDInsight
description: Este artigo descreve as etapas de solução de problemas ao interagir com componentes do Apache HBase em clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 636f84c4c6aa097288dc2fb5481dcedd6863409d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942880"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>API REST para consultar o Apache HBase no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Usando a interface REST do Apache HBase para consultar uma tabela em um namespace diferente dos resultados padrão em um erro de tempo de execução (status HTTP 500).

## <a name="cause"></a>Causa

A API REST do HBase só tem suporte ao usar o namespace padrão. Esse é um problema conhecido em relação ao uso de namespaces do HBase ou à realização de chamadas que se referem a colunas GETs específicas com famílias de colunas com o servidor REST no HDInsight. Isso ocorre devido a um problema de segurança com o gateway do HDInsight. Ao usar a API para criar uma tabela com um namespace, acessar colunas por meio de famílias de colunas, você precisa especificar o `:` caractere, que é considerado um problema de segurança no módulo de gateway do IIS.

## <a name="mitigation"></a>Atenuação

Ignore o gateway/servidor REST implantando seu aplicativo em uma VM que está localizada na mesma VNet do Azure que o cluster HDInsight. Em seguida, você pode se comunicar com o HBase diretamente via RPC (ignorando totalmente o servidor REST) ou para servidores REST individuais em execução em nós de trabalho que ignoram os gateways do HDInsight.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).