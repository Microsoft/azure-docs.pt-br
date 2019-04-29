---
title: Trabalhar com grandes conjuntos de dados
description: Entenda como obter e controlar grandes conjuntos de dados enquanto estiver trabalhando com o Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 729e9fe749212942c6dc18fc7d6301934e7dd184
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775890"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Trabalhando com grandes conjuntos de dados de recurso do Azure

O Azure Resource Graph foi projetado para trabalhar com informações sobre os recursos em seu ambiente do Azure e obtê-las. O Resource Graph facilita a obtenção esses dados rapidamente, mesmo ao consultar milhares de registros. O Resource Graph tem várias opções para trabalhar com esses grandes conjuntos de dados.

## <a name="data-set-result-size"></a>Tamanho do resultado do conjunto de dados

Por padrão, o Resource Graph limita qualquer consulta a retornar apenas **100** registros. Esse controle protege o usuário e o serviço de consultas não intencionais que resultariam em grandes conjuntos de dados. Esse evento geralmente ocorre quando um cliente está experimentando consultas para localizar e filtrar recursos da maneira que atende às suas necessidades específicas. Esse controle é diferente de usar os operadores de linguagem de programação [top](/azure/kusto/query/topoperator) ou [limit](/azure/kusto/query/limitoperator) do Azure Data Explorer para limitar os resultados.

> [!NOTE]
> Ao usar **primeira**, é recomendável para ordenar os resultados pelo menos uma coluna com `asc` ou `desc`. Sem classificação, os resultados retornados são aleatórias e não repetível.

O limite padrão pode ser substituído por meio de todos os métodos de interação com o Resource Graph. Os exemplos a seguir mostram como alterar o limite de tamanho do conjunto de dados para _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

Na [API REST](/rest/api/azureresourcegraph/resources/resources), o controle é **$top** e faz parte de **QueryRequestOptions**.

O controle que for _mais restritivo_ prevalecerá. Por exemplo, se sua consulta usa os operadores **top** ou **limit** e resultaria em mais registros do que **First**, o máximo de registros retornado seria igual a **First**. Da mesma forma, se **top** ou **limit** for menor do que **First**, o conjunto de registros retornado será o menor valor configurado por **top** ou **limit**.

**First** atualmente tem um valor máximo permitido de _5000_.

## <a name="skipping-records"></a>Ignorando os registros

A próxima opção para trabalhar com grandes conjuntos de dados é o controle **Skip**. Esse controle permite que sua consulta pule ou ignore o número definido de registros antes de retornar os resultados. **Skip** é útil para consultas que classificam os resultados de uma maneira significativa, em que a intenção é chegar a registros em algum lugar no meio do conjunto de resultados. Se os resultados necessários estão no final do conjunto de dados retornado, é mais eficiente usar uma configuração de classificação diferente e, em vez disso, recuperar os resultados da parte superior do conjunto de dados.

> [!NOTE]
> Ao usar **Skip**, é recomendável para ordenar os resultados pelo menos uma coluna com `asc` ou `desc`. Sem classificação, os resultados retornados são aleatórias e não repetível.

Os exemplos a seguir mostram como ignorar os primeiros _10_ registros em que uma consulta resultaria, começando em vez disso o conjunto de resultados pelo 11º registro:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

Na [API REST](/rest/api/azureresourcegraph/resources/resources), o controle é **$skip** e faz parte de **QueryRequestOptions**.

## <a name="paging-results"></a>Resultados da paginação

Quando for necessário dividir um conjunto de resultados em conjuntos menores de registros para processamento ou porque um conjunto de resultados exceder o valor máximo permitido de _1000_ registros retornados, usar a paginação. O **QueryResponse** da [API REST](/rest/api/azureresourcegraph/resources/resources) fornece valores para indicar se um conjunto de resultados foi subdividido: **resultTruncated** e **$skipToken**.
**resultTruncated** é um valor booliano que informa ao consumidor se existem registros adicionais não retornados na resposta. Essa condição também pode ser identificada quando a propriedade **count** é menor do que a propriedade **totalRecords**. **totalRecords** define quantos registros correspondem à consulta.

Quando **resultTruncated** é **true**, a propriedade **$skipToken** é definida na resposta. Esse valor é usado com os mesmos valores de consulta e de assinatura para obter o próximo conjunto de registros que correspondeu à consulta.

> [!IMPORTANT]
> A consulta precisa **projetar** o campo **id** para que a paginação funcione. Se ele estiver ausente da consulta, a resposta da API REST não incluirá o **$skipToken**.

Para ver um exemplo, confira a [Consulta de próxima página](/rest/api/azureresourcegraph/resources/resources#next_page_query) na documentação da API REST.

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso no [consultas Starter](../samples/starter.md)
- Ver usos avançados em [Consultas avançadas](../samples/advanced.md)
- Aprender a [explorar recursos](explore-resources.md)