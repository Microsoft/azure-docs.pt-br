---
title: Solução de problemas comuns
description: Saiba como solucionar problemas com vários SDKs ao consultar recursos do Azure com o Azure Resource Graph.
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 0d783b9cb4fa30e3c3e0ff82536b4878c29c7a0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916694"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Soluções de erros usando o Azure Resource Graph

Você pode encontrar erros ao consultar recursos do Azure com o Azure Resource Graph. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes do erro

A maioria dos erros é o resultado de um problema durante a execução de uma consulta com o Azure Resource Graph. Quando uma consulta falha, o SDK fornece detalhes sobre a consulta com falha. Essa informação indica o problema para que ele possa ser corrigido e uma consulta posterior possa ser bem-sucedida.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>Cenário: Solicitações limitadas

#### <a name="issue"></a>Problema

Clientes que fazem consultas de recursos grandes ou frequentes têm solicitações limitadas.

#### <a name="cause"></a>Causa

O Azure Resource Graph aloca um número de cota para cada usuário com base em uma janela de tempo. Por exemplo, um usuário pode enviar no máximo 15 consultas dentro de cada janela de 5 segundos sem ser limitado. O valor da cota é determinado por muitos fatores e está sujeito a mudanças. Para saber mais, veja [Limitação no Azure Resource Graph](../overview.md#throttling).

#### <a name="resolution"></a>Resolução

Há vários métodos para lidar com solicitações limitadas:

- [Agrupar consultas](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [Consultas de escalonamento](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [Consultar em paralelo](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [Paginação](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Cenário: Muitas assinaturas

#### <a name="issue"></a>Problema

Clientes com acesso a mais de 1000 assinaturas, incluindo assinaturas entre locatários com o [Azure Lighthouse](../../../lighthouse/overview.md), não podem buscar dados em todas as assinaturas em uma única chamada para o Azure Resource Graph.

#### <a name="cause"></a>Causa

A CLI do Azure e o PowerShell encaminham apenas as 1000 primeiras assinaturas para o Azure Resource Graph. A API REST para o Azure Resource Graph aceita um número máximo de assinaturas para realizar a consulta.

#### <a name="resolution"></a>Resolução

Faça as solicitações em lote para a consulta com um subconjunto de assinaturas abaixo do limite de 1000 assinaturas. A solução é usar o parâmetro **Assinatura** no PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Cenário: Cabeçalho REST de tipo de conteúdo sem suporte

#### <a name="issue"></a>Problema

Os clientes que consultam a API REST do Azure Resource Graph obtêm uma resposta _500_ (Erro de servidor interno).

#### <a name="cause"></a>Causa

A API REST do Azure Resource Graph só dá suporte a um `Content-Type` de **aplicativo/json**. Algumas ferramentas REST ou agentes assumem como padrão **texto/sem formatação**, que não é suportado pela API REST.

#### <a name="resolution"></a>Resolução

Valide se a ferramenta ou o agente que você está usando para consultar o Azure Resource Graph tem o cabeçalho da API REST `Content-Type` configurado para **aplicativo/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Cenário: Nenhuma permissão de leitura para todas as assinaturas na lista

#### <a name="issue"></a>Problema

Os clientes que passam explicitamente uma lista de assinaturas com uma consulta do Azure Resource Graph obtêm uma resposta _403_ (Proibido).

#### <a name="cause"></a>Causa

Se o cliente não tiver permissão de leitura para todas as assinaturas fornecidas, a solicitação será negada devido à falta de direitos de segurança apropriados.

#### <a name="resolution"></a>Resolução

Inclua pelo menos uma assinatura na lista de assinaturas a qual o cliente que está executando a consulta tenha pelo menos acesso de leitura. Para saber mais, veja [Permissões no Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
- Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.