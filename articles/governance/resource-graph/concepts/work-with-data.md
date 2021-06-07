---
title: Trabalhar com grandes conjuntos de dados
description: Entenda como obter, formatar, paginar e ignorar registros em grandes conjuntos de dados enquanto estiver trabalhando com o Azure Resource Graph.
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 1eaabfdd78712966f3b21d869259a312db31b7bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917683"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Trabalhando com grandes conjuntos de dados de recurso do Azure

O Azure Resource Graph foi projetado para trabalhar com informações sobre os recursos em seu ambiente do Azure e obtê-las. O Resource Graph facilita a obtenção esses dados rapidamente, mesmo ao consultar milhares de registros. O Resource Graph tem várias opções para trabalhar com esses grandes conjuntos de dados.

Para obter orientação sobre como trabalhar com consultas em alta frequência, veja [Diretrizes para solicitações limitadas](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Tamanho do resultado do conjunto de dados

Por padrão, o Resource Graph limita qualquer consulta a retornar apenas **100** registros. Esse controle protege o usuário e o serviço de consultas não intencionais que resultariam em grandes conjuntos de dados. Esse evento geralmente ocorre quando um cliente está experimentando consultas para localizar e filtrar recursos da maneira que atende às suas necessidades específicas. Esse controle é diferente de usar os operadores de linguagem de programação [top](/azure/kusto/query/topoperator) ou [limit](/azure/kusto/query/limitoperator) do Azure Data Explorer para limitar os resultados.

> [!NOTE]
> É recomendável ordenar os resultados em pelo menos uma coluna com `asc` ou `desc` quando **First** for usado. Os resultados retornados são aleatórios e não podem ser repetidos quando não são classificados.

O limite padrão pode ser substituído por meio de todos os métodos de interação com o Resource Graph. Os exemplos a seguir mostram como alterar o limite de tamanho do conjunto de dados para _200_:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

Na [API REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources), o controle é **$top** e faz parte de **QueryRequestOptions**.

O controle que for _mais restritivo_ prevalecerá. Por exemplo, se sua consulta usa os operadores **top** ou **limit** e resultaria em mais registros do que **First**, o máximo de registros retornado seria igual a **First**. Da mesma forma, se **top** ou **limit** for menor do que **First**, o conjunto de registros retornado será o menor valor configurado por **top** ou **limit**.

Atualmente, **First** tem um valor máximo permitido de _5000_, obtido através da [paginação de resultados](#paging-results) de _1000_ registros por consulta.

> [!IMPORTANT]
> Quando **First** é configurado para mais de _1000_ registros, a consulta deve **projetar** o campo **id** para que a paginação funcione. Se ele não estiver presente na consulta, a resposta não será [paginada](#paging-results) e os resultados serão limitados a _1000_ registros.

## <a name="skipping-records"></a>Ignorando os registros

A próxima opção para trabalhar com grandes conjuntos de dados é o controle **Skip**. Esse controle permite que sua consulta pule ou ignore o número definido de registros antes de retornar os resultados. **Skip** é útil para consultas que classificam os resultados de uma maneira significativa, em que a intenção é chegar a registros em algum lugar no meio do conjunto de resultados. Se os resultados necessários estão no final do conjunto de dados retornado, é mais eficiente usar uma configuração de classificação diferente e, em vez disso, recuperar os resultados da parte superior do conjunto de dados.

> [!NOTE]
> É recomendável ordenar os resultados em pelo menos uma coluna com `asc` ou `desc` quando **Skip** for usado. Os resultados retornados são aleatórios e não podem ser repetidos quando não são classificados. Se `limit` ou `take` forem usados na consulta, **Skip** será ignorado.

Os exemplos a seguir mostram como ignorar os primeiros _10_ registros em que uma consulta resultaria, começando em vez disso o conjunto de resultados pelo 11º registro:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

Na [API REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources), o controle é **$skip** e faz parte de **QueryRequestOptions**.

## <a name="paging-results"></a>Resultados da paginação

Quando é necessário dividir um conjunto de resultados em conjuntos de registros menores para processamento ou porque um conjunto de resultados excede o valor máximo permitido de _1000_ registros retornados, use paginação. O QueryResponse da [API REST](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) 
  fornece valores para indicar que um conjunto de resultados foi dividido: **resultTruncated** e **$skipToken**. **resultTruncated** é um valor booliano que informa ao consumidor se há mais registros não retornados na resposta. Essa condição também pode ser identificada quando a propriedade **count** é menor do que a propriedade **totalRecords**. **totalRecords** define quantos registros correspondem à consulta.

 **resultTruncated** é **true** quando a paginação está desabilitada ou não é possível porque nenhuma `id` coluna ou quando há menos recursos disponíveis do que uma consulta está solicitando. Quando **resultTruncated** é **true**, a propriedade **$skipToken** não é definida.

Os exemplos a seguir mostram como **ignorar** os primeiros 3000 registros e retornar os **primeiros** 1000 registros depois dos registros ignorados com a CLI do Azure e o Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> A consulta precisa **projetar** o campo **id** para que a paginação funcione. Se estiver ausente da consulta, a resposta não incluirá o **$skipToken**.

Para ver um exemplo, confira a [Consulta de próxima página](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#next-page-query) na documentação da API REST.

## <a name="formatting-results"></a>Formatação de resultados

Os resultados de uma consulta do Resource Graph são fornecidos em dois formatos, _Table_ e _ObjectArray_. O formato é configurado com o parâmetro **resultFormat** como parte das opções de solicitação. O formato _Table_ é o valor padrão para **resultFormat**.

Os resultados de CLI do Azure são fornecidos, por padrão, em JSON. Os resultados no Azure PowerShell são um **PSCustomObject** por padrão, mas podem ser rapidamente convertidos em JSON com o cmdlet `ConvertTo-Json`. Para outros SDKs, os resultados da consulta podem ser configurados para gerar o formato _ObjectArray_.

### <a name="format---table"></a>Formato - Table

O formato padrão, _Table_, retorna resultados em um formato JSON projetado para realçar o design da coluna e os valores da linha das propriedades retornadas pela consulta. Esse formato se assemelha bastante aos dados apresentados em uma tabela ou planilha estruturada, com as colunas identificadas primeiro e, em seguida, cada linha representando os dados alinhados a essas colunas.

Aqui está um exemplo de um resultado de consulta com a formatação de _tabela_ :

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Formato - ObjectArray

O formato _ObjectArray_ também retorna resultados em um formato JSON. No entanto, esse design é semelhante ao relacionamento do par chave/valor comum em JSON, com a correspondência dos dados da coluna e da linha em grupos de matrizes.

Aqui está um exemplo de um resultado de consulta com a formatação _objectarray_ :

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

A seguir estão alguns exemplos de configuração de **resultFormat** para usar o formato _ObjectArray_:

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Próximas etapas

- Consulte a linguagem em uso em [Consultas iniciais](../samples/starter.md).
- Consulte os usos avançados em [Consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](explore-resources.md).
