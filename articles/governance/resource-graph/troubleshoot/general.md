---
title: Solução de problemas comuns
description: Saiba como solucionar problemas de consulta de recursos do Azure com o grafo de recursos do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/24/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 511d170f90e8ed34b00a3960d084223ec73d99dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480547"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Solucionar erros usando o grafo de recursos do Azure

Você pode encontrar erros ao consultar recursos do Azure com o grafo de recursos do Azure. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes do erro

A maioria dos erros é o resultado de um problema durante a execução de uma consulta com o grafo de recursos do Azure. Quando uma consulta falha, o SDK fornece detalhes sobre a consulta com falha. Essas informações indicam o problema para que ele possa ser corrigido e uma consulta posterior seja realizada com sucesso.

## <a name="general-errors"></a>Erros gerais

### <a name="toomanysubscription"></a>Cenário: Excesso de assinaturas

#### <a name="issue"></a>Problema

Os clientes com acesso a mais de 1000 assinaturas, incluindo assinaturas entre locatários com o [Azure Lighthouse](../../../lighthouse/overview.md), não podem buscar dados em todas as assinaturas em uma única chamada para o grafo de recursos do Azure.

#### <a name="cause"></a>Causa

CLI do Azure e o PowerShell encaminham apenas as primeiras assinaturas 1000 para o grafo de recursos do Azure. A API REST para o grafo de recursos do Azure aceita um número máximo de assinaturas para executar a consulta.

#### <a name="resolution"></a>Resolução

Solicitações em lote para a consulta com um subconjunto de assinaturas para permanecer sob o limite de assinatura 1000. A solução está usando o parâmetro de **assinatura** no PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
- Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.