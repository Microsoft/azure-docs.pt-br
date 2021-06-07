---
title: Como consultar dados do grafo no Azure Cosmos DB?
description: Saiba como consultar dados de grafo do Azure Cosmos DB usando consultas do Gremlin
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 12/03/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 545ffd303d2039a3c54088220c1fa74e742c750f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360761"
---
# <a name="tutorial-query-azure-cosmos-db-gremlin-api-by-using-gremlin"></a>Tutorial: Consultar a API do Gremlin do Azure Cosmos DB usando Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

A [API do Gremlin](graph-introduction.md) do Azure Cosmos DB também é compatível com consultas do [Gremlin](https://github.com/tinkerpop/gremlin/wiki). Este artigo fornece exemplos de documentos e consultas para você começar. Uma referência detalhada do Gremlin é fornecida no artigo [Suporte a Gremlin](gremlin-support.md).

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Consultar dados com o Gremlin

## <a name="prerequisites"></a>Pré-requisitos

Para essas consultas funcionarem, você deve ter uma conta do Azure Cosmos DB e ter dados de grafo no contêiner. Não tenho nenhum deles? Complete o [Guia de início rápido de cinco minutos](create-graph-dotnet.md) ou o [tutorial de desenvolvedor](tutorial-query-graph.md) para criar uma conta e preencher seu banco de dados. Você pode executar as seguintes consultas usando o [console Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) ou o seu driver favorito do Gremlin.

## <a name="count-vertices-in-the-graph"></a>Contagem de vértices no grafo

O snippet a seguir mostra como contar o número de vértices no grafo:

```
g.V().count()
```

## <a name="filters"></a>Filtros

Execute filtros usando as etapas `has` e `hasLabel` do Gremlin e combine-as usando `and`, `or` e `not` para compilar filtros mais complexos. O Azure Cosmos DB fornece indexação independente do esquema de todas as propriedades em seus vértices e graus para consultas rápidas:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projeção

Você pode projetar certas propriedades nos resultados da consulta usando a etapa `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Localizar os vértices e bordas relacionadas

Até agora, só vimos operadores de consulta que funcionam em qualquer banco de dados. Os grafo são rápidos e eficientes para operações de passagem quando você precisa navegar até os vértices e bordas relacionadas. Vamos encontrar todos os amigos de Thomas. Fazemos isso usando a etapa `outE` do Gremlin para localizar todos as bordas externas de Thomas, depois passamos para os vértices internos dessas bordas usando a etapa `inV` do Gremlin:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

A próxima consulta executa dois saltos para localizar todos os "amigos dos amigos" de Thomas, chamando `outE` e `inV` duas vezes. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Você pode criar consultas mais complexas e implementar uma lógica avançada de passagem de grafo usando o Gremlin, incluindo a combinação de expressões de filtro, executando o loop usando a etapa `loop` e implementando a navegação condicional usando a etapa `choose`. Saiba mais sobre o que você pode fazer com o [Suporte do Gremlin](gremlin-support.md)!

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Aprendeu a consultar usando o Graph 

Agora você pode seguir para a seção Conceitos para obter mais informações sobre o Cosmos DB.

> [!div class="nextstepaction"]
> [Distribuição global](distribute-data-globally.md) 

