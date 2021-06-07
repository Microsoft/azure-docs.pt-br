---
title: Exemplos em Python
titleSuffix: Azure Cognitive Search
description: Encontre exemplos de código do Python de demonstração do Azure Pesquisa Cognitiva que usam o SDK do .NET do Azure para Python ou REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955115"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Exemplos de código do Python para o Azure Pesquisa Cognitiva

Saiba mais sobre os exemplos de código do Python que demonstram a funcionalidade e o fluxo de trabalho de uma solução de Pesquisa Cognitiva do Azure. Esses exemplos usam a [**biblioteca de cliente do azure pesquisa cognitiva**](/python/api/overview/azure/search-documents-readme) para o [**SDK do Azure para Python**](/azure/developer/python/), que pode ser explorado por meio dos links a seguir.

| Destino | Link |
|--------|------|
| Download do pacote | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| Referência de API | [azure-search-documents](/python/api/azure-search-documents)  |
| Casos de teste de API | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Código-fonte | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>Exemplos do SDK

Exemplos de código da equipe de desenvolvimento do SDK do Azure demonstram o uso da API. Você pode encontrar esses exemplos em [**Azure-SDK-for-Python/árvore/mestre/SDK/pesquisa/Azure-Search-Documents/Samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) no github.

| Exemplos | Descrição |
|---------|-------------|
| [Autenticar](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Demonstra como configurar um cliente do e autenticar o serviço do. | 
| [Operações de criação de índice-leitura-atualização-exclusão](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Demonstra como criar, atualizar, obter, listar e excluir [índices de pesquisa](search-what-is-an-index.md). |
| [Operações de criação-leitura-atualização-exclusão do indexador](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Demonstra como criar, atualizar, obter, listar, redefinir e excluir [indexadores](search-indexer-overview.md). |
| [Pesquisar fontes de dados do indexador](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Demonstra como criar, atualizar, obter, listar e excluir fontes de dados do indexador, necessárias para a indexação baseada em indexador de [fontes de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources). |
| [Sinônimos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Demonstra como criar, atualizar, obter, listar e excluir [mapas de sinônimos](search-synonyms.md).  |
| [Carregue os documentos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Demonstra como carregar ou mesclar documentos em um índice em uma operação de [importação de dados](search-what-is-data-import.md) . |
| [Consulta simples](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Demonstra como configurar uma [consulta básica](search-query-overview.md). |
| [Consulta de filtro](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Demonstra a configuração de uma [expressão de filtro](search-filters.md). |
| [Consulta de faceta](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Demonstra como trabalhar com [facetas](search-filters-facets.md). |

## <a name="doc-samples"></a>Exemplos de documento

Exemplos de código da equipe de Pesquisa Cognitiva demonstram recursos e fluxos de trabalho. Muitos desses exemplos são referenciados em tutoriais, guias de início rápido e artigos de instruções. Você pode encontrar esses exemplos em [**Azure-Samples/Azure-Search-Python-Samples**](https://github.com/Azure-Samples/azure-search-python-samples) no github.

| Exemplos | Artigo |
|---------|---------|
| [TUTORIAIS](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Código-fonte para [início rápido: criar um índice de pesquisa em Python](search-get-started-python.md). Este artigo aborda o fluxo de trabalho básico para criar, carregar e consultar um índice de pesquisa usando dados de exemplo. |
| [tutorial – reenriquecimento de ia](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Código-fonte para [tutorial: use python e ia para gerar conteúdo pesquisável de BLOBs do Azure](cognitive-search-tutorial-blob-python.md). Este artigo mostra como criar um indexador de blob com um habilidades cognitivas, em que o consumível cria e transforma conteúdo bruto para torná-lo pesquisável ou consumíveis. |
| [AzureML-habilidades personalizadas](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Código-fonte por [exemplo: criar uma habilidade personalizada usando Python](cognitive-search-custom-skill-python.md). Este artigo demonstra a integração do indexer com o Skills com modelos de aprendizado profundo no Azure Machine Learning. |

> [!Tip]
> Experimente o [navegador de exemplos](/samples/browse/?languages=python&products=azure-cognitive-search) para pesquisar exemplos de código da Microsoft no GitHub, filtrados por produto, serviço e idioma.