---
title: Exemplos de JavaScript
titleSuffix: Azure Cognitive Search
description: Encontre exemplos de código JavaScript de demonstração do Azure Pesquisa Cognitiva que usam o SDK do .NET do Azure para JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: f83767813ea3923d85db2ca3f0164776c610525e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955013"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Exemplos de código JavaScript para o Azure Pesquisa Cognitiva

Saiba mais sobre os exemplos de código do JavaScript que demonstram a funcionalidade e o fluxo de trabalho de uma solução de Pesquisa Cognitiva do Azure. Esses exemplos usam a [**biblioteca de cliente do azure pesquisa cognitiva**](/javascript/api/overview/azure/search-documents-readme) para o [**SDK do Azure para JavaScript**](/azure/developer/javascript/), que pode ser explorado por meio dos links a seguir.

| Destino | Link |
|--------|------|
| Download do pacote | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| Referência de API | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| Casos de teste de API | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Código-fonte | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>Exemplos do SDK

Exemplos de código da equipe de desenvolvimento do SDK do Azure demonstram o uso da API. Você pode encontrar esses exemplos em [**Azure-SDK-for-js/árvore/mestre/SDK/Search/Search-Documents/Samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) no github.

### <a name="javascript-sdk-samples"></a>Exemplos de SDK do JavaScript

| Exemplos | Descrição |
|---------|-------------|
| [índices](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Demonstra como criar, atualizar, obter, listar e excluir [índices de pesquisa](search-what-is-an-index.md). Essa categoria de exemplo também inclui um exemplo de estatística de serviço. |
| [dataSourceConnections (para indexadores)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Demonstra como criar, atualizar, obter, listar e excluir fontes de dados do indexador, necessárias para a indexação baseada em indexador de [fontes de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources). |
| [indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Demonstra como criar, atualizar, obter, listar, redefinir e excluir [indexadores](search-indexer-overview.md).|
| [Qualificações](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Demonstra como criar, atualizar, obter, listar e excluir [habilidades](cognitive-search-working-with-skillsets.md) que são indexadores anexados e que executam o enriquecimento baseado em ia durante a indexação. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Demonstra como criar, atualizar, obter, listar e excluir [mapas de sinônimos](search-synonyms.md).  |
| [Consultas](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Demonstra a execução da consulta em um índice público somente leitura hospedado pela Microsoft.  |

### <a name="typescript-samples"></a>Exemplos de TypeScript

| Exemplos | Descrição |
|---------|-------------|
| [índices](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Demonstra como criar, atualizar, obter, listar e excluir [índices de pesquisa](search-what-is-an-index.md). Essa categoria de exemplo também inclui um exemplo de estatística de serviço. |
| [dataSourceConnections (para indexadores)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Demonstra como criar, atualizar, obter, listar e excluir fontes de dados do indexador, necessárias para a indexação baseada em indexador de [fontes de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources). |
| [indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Demonstra como criar, atualizar, obter, listar, redefinir e excluir [indexadores](search-indexer-overview.md).|
| [Qualificações](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Demonstra como criar, atualizar, obter, listar e excluir [habilidades](cognitive-search-working-with-skillsets.md) que são indexadores anexados e que executam o enriquecimento baseado em ia durante a indexação. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Demonstra como criar, atualizar, obter, listar e excluir [mapas de sinônimos](search-synonyms.md).  |
| [Consultas](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Demonstra a execução da consulta em um índice público somente leitura hospedado pela Microsoft.  |

## <a name="doc-samples"></a>Exemplos de documento

Exemplos de código da equipe de Pesquisa Cognitiva demonstram recursos e fluxos de trabalho. Muitos desses exemplos são referenciados em tutoriais, guias de início rápido e artigos de instruções. Você pode encontrar esses exemplos em [**Azure-Samples/Azure-Search-JavaScript-Samples**](https://github.com/Azure-Samples/azure-search-javascript-samples) no github.

| Exemplos | Artigo |
|---------|---------|
| [TUTORIAIS](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Código-fonte para [início rápido: criar um índice de pesquisa em JavaScript](search-get-started-javascript.md). Este artigo aborda o fluxo de trabalho básico para criar, carregar e consultar um índice de pesquisa usando dados de exemplo. |

> [!Tip]
> Experimente o [navegador de exemplos](/samples/browse/?languages=javascript&products=azure-cognitive-search) para pesquisar exemplos de código da Microsoft no GitHub, filtrados por produto, serviço e idioma.

## <a name="other-samples"></a>Outras amostras

Os exemplos a seguir também são publicados pela equipe de Pesquisa Cognitiva, mas não são referenciados na documentação do. Os arquivos Leiame associados fornecem instruções de uso.

| Exemplos | Descrição |
|---------|-------------|
| [Azure-Search-reagir-modelo](https://github.com/dereklegenzoff/azure-search-react-template) | Modelo de reagem para o Azure Pesquisa Cognitiva (github.com) |