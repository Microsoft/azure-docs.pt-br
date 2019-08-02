---
title: Diretrizes para solicitações limitadas
description: Aprenda a criar consultas melhor para evitar solicitações para o gráfico de recursos do Azure do que está sendo limitado.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276893"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Diretrizes para solicitações limitadas no gráfico de recursos do Azure

Ao criar o uso programático e frequente de dados do gráfico de recursos do Azure, consideração deve ser feita para como limitação afeta os resultados das consultas. Alterando os maneira como os dados é solicitado podem ajudar você e sua organização evitar limitação e manter o fluxo de dados em tempo hábil sobre os recursos do Azure.

Este artigo aborda quatro áreas e padrões relacionados à criação de consultas no gráfico de recursos do Azure:

- Entender os cabeçalhos de limitação
- Consultas em lote
- Consultas escalonadas
- O impacto da paginação

## <a name="understand-throttling-headers"></a>Entender os cabeçalhos de limitação

Gráfico de recursos do Azure aloca um número de cota para cada usuário com base em uma janela de tempo. Por exemplo, um usuário pode enviar no máximo 15 consultas dentro de cada janela de 5 segundos sem que está sendo limitado. O valor da cota é determinado por vários fatores e está sujeitas a alterações.

Em cada resposta de consulta, o gráfico de recursos do Azure adiciona dois cabeçalhos de limitação:

- `x-ms-user-quota-remaining` (int): A cota de recursos que resta para o usuário. Esse valor é mapeado para a contagem de consultas.
- `x-ms-user-quota-resets-after` (hh:mm:ss): A duração de tempo até que o consumo de cota do usuário é redefinido.

Para ilustrar como funcionam os cabeçalhos, vamos dar uma olhada em uma resposta de consulta que tem o cabeçalho e os valores de `x-ms-user-quota-remaining: 10` e `x-ms-user-quota-resets-after: 00:00:03`.

- Dentro de 3 segundos, no máximo 10 consultas podem ser enviadas sem sendo limitadas.
- Em 3 segundos, os valores de `x-ms-user-quota-remaining` e `x-ms-user-quota-resets-after` será redefinido para `15` e `00:00:05` , respectivamente.

Para ver um exemplo de como usar os cabeçalhos a serem _retirada_ nas solicitações de consulta, consulte o exemplo no [consulta em paralelo](#query-in-parallel).

## <a name="batching-queries"></a>Consultas em lote

Envio em lote de consultas por assinatura, grupo de recursos ou recurso individual é mais eficiente do que a paralelização de consultas. O custo de cota de uma consulta maior costuma ser menor que o custo de cota de muitas consultas de pequenos e de destino. O tamanho do lote é recomendado para ser menor que _300_.

- Exemplo de uma abordagem mal otimizado

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Exemplo 1 # de uma abordagem otimizada de envio em lote

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Exemplo 2 de # de uma abordagem otimizada de envio em lote

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Consultas escalonadas

Por causa da maneira a limitação é imposta, recomendamos que as consultas para ser escalonada. Ou seja, em vez de enviar 60 consultas ao mesmo tempo, escalone as consultas em quatro janelas de 5 segundos:

- Agendamento de consulta não-alternados

  | Contagem de consulta         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (s) | 0-5 | 5-10 | 10-15 | 15-20 |

- Alternado de agendamento de consulta

  | Contagem de consulta         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (s) | 0-5 | 5-10 | 10-15 | 15-20 |

Abaixo está um exemplo de respeito aos cabeçalhos de limitação ao consultar o grafo de recursos do Azure:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Consulta em paralelo

Mesmo que o envio em lote é recomendável em vez de paralelização, há ocasiões em que consultas não podem ser facilmente em lote. Nesses casos, convém consultar o grafo de recursos do Azure, enviando várias consultas de forma paralela. Abaixo está um exemplo de como _retirada_ com base em cabeçalhos nesses cenários de limitação:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Paginação

Como o gráfico de recursos do Azure retorna no máximo 1000 entradas em uma resposta de consulta única, talvez você precise [paginar](./work-with-data.md#paging-results) suas consultas para obter o conjunto de dados completo, você está procurando. No entanto, alguns clientes de gráfico de recursos do Azure tratar paginação Diferentemente de outras pessoas.

- SDK do C#

  Ao usar ResourceGraph SDK, você precisa lidar com a paginação, passando o token de salto que está sendo retornado na resposta da consulta anterior para a próxima consulta paginada. Esse design significa que você precisa coletar resultados de todas as chamadas paginadas e combiná-los no final. Nesse caso, cada consulta paginada que você enviar usa uma cota de consulta:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- CLI do Azure / Azure PowerShell

  Ao usar a CLI do Azure ou Azure PowerShell, consultas de grafo de recursos do Azure são automaticamente paginadas para buscar no máximo de 5000 entradas. Os resultados da consulta retornam uma lista combinada de entradas de todas as chamadas paginadas. Nesse caso, dependendo do número de entradas no resultado da consulta, uma única consulta paginada pode consumir mais de uma cota de consulta. Por exemplo, no exemplo a seguir, uma única execução da consulta pode consumir até cinco cota de consulta:

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Ainda ficam limitados?

Se você está ficando limitado depois de exercitar as recomendações acima, entre em contato com a equipe em [ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com).

Forneça estes detalhes:

- Suas necessidades específicas de driver de caso de uso e de negócios para um limite mais alto de limitação.
- Quantos recursos você tem acesso ao? Quantos são retornados de uma única consulta?
- Quais são os tipos de recursos interessado?
- O que é o padrão de consulta? X consultas por segundos Y etc.

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso no [consultas Starter](../samples/starter.md).
- Consulte avançada usa na [consultas avançadas](../samples/advanced.md).
- Saiba como [explore recursos](explore-resources.md).