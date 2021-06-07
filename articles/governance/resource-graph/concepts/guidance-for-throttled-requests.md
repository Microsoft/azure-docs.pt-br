---
title: Diretrizes para solicitações limitadas
description: Aprenda a agrupar, escalonar, paginar e consultar em paralelo para evitar que as solicitações sejam limitadas pelo Azure Resource Graph.
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: ddd3cf4d411733e831c94039c3bc9aeaf0e95271
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917700"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Diretrizes para solicitações limitadas no Azure Resource Graph

Ao criar uso programático e frequente de dados do Azure Resource Graph, deve-se considerar como a limitação afeta os resultados das consultas. Mudar a maneira como os dados são solicitados pode ajudar a evitar a limitação e manter o fluxo de dados oportunos sobre os recursos do Azure.

Este artigo aborda quatro áreas e padrões relacionados à criação de consultas no Azure Resource Graph:

- Noções básicas de cabeçalhos de limitação
- Agrupar consultas
- Consultas de escalonamento
- O impacto da paginação

## <a name="understand-throttling-headers"></a>Noções básicas de cabeçalhos de limitação

O Azure Resource Graph aloca um número de cota para cada usuário com base em uma janela de tempo. Por exemplo, um usuário pode enviar no máximo 15 consultas dentro de cada janela de 5 segundos sem ser limitado. O valor da cota é determinado por muitos fatores e está sujeito a mudanças.

Em cada resposta de consulta, o Azure Resource Graph adiciona dois cabeçalhos de limitação:

- `x-ms-user-quota-remaining` (int): A cota de recursos que resta para o usuário. Esse valor é mapeado para a contagem de consultas.
- `x-ms-user-quota-resets-after` (hh:mm:ss): O tempo até que o consumo da cota do usuário seja redefinido.

Quando uma entidade de segurança tem acesso a mais de 5000 assinaturas no [escopo de consulta](./query-language.md#query-scope)de locatário ou grupo de gerenciamento, a resposta é limitada às primeiras assinaturas de 5000 e o `x-ms-tenant-subscription-limit-hit` cabeçalho é retornado como `true` .

Para ilustrar como os cabeçalhos funcionam, examinaremos a resposta de uma consulta que tem o cabeçalho e os valores de `x-ms-user-quota-remaining: 10` e `x-ms-user-quota-resets-after: 00:00:03`.

- Nos próximos 3 segundos, no máximo 10 consultas poderão ser enviadas sem limitação.
- Em 3 segundos, os valores de `x-ms-user-quota-remaining` e `x-ms-user-quota-resets-after` serão redefinidos para `15` e `00:00:05` respectivamente.

Para ver um exemplo de como usar os cabeçalhos para _retirada_ em solicitações de consulta, consulte o exemplo em [Consulta em paralelo](#query-in-parallel).

## <a name="grouping-queries"></a>Agrupar consultas

O agrupamento de consultas por assinatura, grupo de recursos ou recurso individual é mais eficiente do que paralelizar consultas. O custo de cota de uma consulta maior geralmente é menor do que o custo de cota de muitas consultas pequenas e direcionadas. É recomendável que o tamanho do grupo seja inferior a _300_.

- Exemplo de uma abordagem mal otimizada

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Exemplo 1 de uma abordagem de agrupamento otimizado

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Exemplo 2 de uma abordagem de agrupamento otimizado para obter vários recursos em uma consulta

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Consultas de escalonamento

Devido à maneira como a limitação é imposta, recomendamos que as consultas sejam escalonadas. Ou seja, em vez de enviar consultas 60 ao mesmo tempo, escalone as consultas em quatro janelas de 5 segundos:

- Agendamento de consulta escalonada

  | Contagem de consultas         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (s) | 0-5 | 5-10 | 10-15 | 15–20 |

- Agenda de consulta escalonada

  | Contagem de consultas         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervalo de tempo (s) | 0-5 | 5-10 | 10-15 | 15–20 |

Aqui está um exemplo de como respeitar cabeçalhos de limitação ao consultar o grafo de recursos do Azure:

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

Embora o agrupamento seja recomendado em detrimento da paralelização, há ocasiões em que as consultas não podem ser agrupadas facilmente. Nesses casos, talvez seja melhor consultar o Azure Resource Graph enviando várias consultas de maneira paralela. Veja um exemplo de como fazer a _retirada_ com base em cabeçalhos de limitação em tais cenários:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

Como o Azure Resource Graph retorna no máximo 1.000 entradas em uma única resposta de consulta, talvez seja necessário [paginar](./work-with-data.md#paging-results) as consultas para obter o conjunto de dado completo que você está procurando. No entanto, alguns clientes do Azure Resource Graph lidam com a paginação de um jeito diferente.

- SDK do C#

  Ao usar o SDK do ResourceGraph, é preciso lidar com a paginação passando o token de omissão retornado na resposta da consulta anterior para a próxima consulta paginada. Isso significa que é preciso coletar resultados de todas as chamadas paginadas e combiná-las no final. Nesse caso, cada consulta paginada enviada usa uma cota de consulta:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
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

- CLI do Azure/Azure PowerShell

  Ao usar a CLI do Azure ou o Azure PowerShell, as consultas para o Azure Resource Graph são paginadas automaticamente para buscar no máximo 5.000 entradas. Os resultados da consulta retornam uma lista combinada de entradas de todas as chamadas paginadas. Nesse caso, dependendo do número de entradas no resultado da consulta, uma única consulta paginada pode consumir mais de uma cota de consulta. Por exemplo, nos exemplos a seguir, uma única execução da consulta pode consumir até cinco cotas de consulta:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Ainda está enfrentando limitações?

Se estiver enfrentando limitações depois de seguir as recomendações acima, entre em contato com a equipe em [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com).

Forneça os detalhes:

- Seu caso de uso específico e o driver de negócios precisam de um limite de limitação mais alto.
- A quantos recursos você tem acesso? Quantos deles são retornados em uma única consulta?
- Em quais tipos de recursos você está interessado?
- Qual é seu padrão de consulta? X consultas por Y segundos e assim por diante.

## <a name="next-steps"></a>Próximas etapas

- Consulte a linguagem em uso em [Consultas iniciais](../samples/starter.md).
- Consulte os usos avançados em [Consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](explore-resources.md).