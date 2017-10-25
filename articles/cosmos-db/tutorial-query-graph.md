---
title: Como consultar dados do Graph no Azure Cosmos DB? | Microsoft Docs
description: Aprenda a consultar dados do Graph no Azure Cosmos DB
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
tags: 
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: denlee
ms.openlocfilehash: 81713c72da037f127e81239d214d7a877247dca1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-how-to-query-with-the-graph-api-preview"></a>Azure Cosmos DB: Como consultar com a API do Graph (visualização)?

A [API do Graph](graph-introduction.md) (visualização) do Azure Cosmos DB oferece suporte a consultas do [Gremlin](https://docs.mongodb.com/manual/tutorial/query-documents/). Este artigo fornece exemplos de documentos e consultas para você começar. Uma referência detalhada do Gremlin é fornecida no artigo [Suporte a Gremlin](gremlin-support.md).

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Consultar dados com o Gremlin

## <a name="prerequisites"></a>Pré-requisitos

Para essas consultas funcionarem, você deve ter uma conta do Azure Cosmos DB e ter dados de gráfico no contêiner. Não tenho nenhum deles? Complete o [Guia de início rápido de cinco minutos](create-graph-dotnet.md) ou o [tutorial de desenvolvedor](tutorial-query-graph.md) para criar uma conta e preencher seu banco de dados. Você pode executar as seguintes consultas usando a [Biblioteca de gráficos do .NET do Azure Cosmos DB](graph-sdk-dotnet.md), [Console do Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) ou o seu driver favorito do Gremlin.

## <a name="count-vertices-in-the-graph"></a>Contagem de vértices no gráfico

O trecho a seguir mostra como contar o número de vértices no gráfico:

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

Até agora, só vimos operadores de consulta que funcionam em qualquer banco de dados. Os gráficos são rápidos e eficientes para operações de passagem quando você precisa navegar até os vértices e bordas relacionadas. Vamos encontrar todos os amigos de Thomas. Fazemos isso usando a etapa `outE` do Gremlin para localizar todos as bordas externas de Thomas, depois passamos para os vértices internos dessas bordas usando a etapa `inV` do Gremlin:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

A próxima consulta executa dois saltos para localizar todos os "amigos dos amigos" de Thomas, chamando `outE` e `inV` duas vezes. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Você pode criar consultas mais complexas e implementar uma lógica avançada de passagem de gráfico usando o Gremlin, incluindo a combinação de expressões de filtro, executando o loop usando a etapa `loop` e implementando a navegação condicional usando a etapa `choose`. Saiba mais sobre o que você pode fazer com o [Suporte do Gremlin](gremlin-support.md)!

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Aprendeu a consultar usando o Graph 

Agora você pode prosseguir para o próximo tutorial e aprender a distribuir seus dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir os dados globalmente](tutorial-global-distribution-documentdb.md)