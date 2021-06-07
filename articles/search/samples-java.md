---
title: Exemplos do Java
titleSuffix: Azure Cognitive Search
description: Encontre exemplos de código Java de demonstração do Azure Pesquisa Cognitiva que usam o SDK do .NET do Azure para Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955030"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Exemplos de código Java para o Azure Pesquisa Cognitiva

Saiba mais sobre os exemplos de código Java que demonstram a funcionalidade e o fluxo de trabalho de uma solução de Pesquisa Cognitiva do Azure. Esses exemplos usam a [**biblioteca de cliente do azure pesquisa cognitiva**](/java/api/overview/azure/search-documents-readme) para o [**SDK do Azure para Java**](/azure/developer/java/sdk), que pode ser explorado por meio dos links a seguir.

| Destino | Link |
|--------|------|
| Download do pacote | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| Referência de API | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| Casos de teste de API | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Código-fonte | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>Exemplos do SDK

Exemplos de código da equipe de desenvolvimento do SDK do Azure demonstram o uso da API. Você pode encontrar esses exemplos no [**Azure/Azure-SDK-para-Java/árvore/mestre/SDK/pesquisa/Azure-Search-Documents/src/Samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) no github.

| Exemplos | Descrição |
|---------|-------------|
| [Pesquisar criação de índice](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Demonstra como criar [índices de pesquisa](search-what-is-an-index.md). |
| [Criação de sinônimos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Demonstra como criar [mapas de sinônimos](search-synonyms.md).  |
| [Criação do indexador de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Demonstra como criar [indexadores](search-indexer-overview.md). |
| [Criação da fonte de dados do indexador de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Demonstra como criar fontes de dados do indexador, necessárias para a indexação baseada em indexador de [fontes de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources). |
| [Criação de conconhecimento](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Demonstra como criar [habilidades](cognitive-search-working-with-skillsets.md) que são indexadores anexados e que executam o enriquecimento baseado em ia durante a indexação. |
| [Carregue os documentos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Demonstra como carregar ou mesclar documentos em um índice em uma operação de [importação de dados](search-what-is-data-import.md) . |
| [Sintaxe de consulta](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Demonstra como configurar uma [consulta básica](search-query-overview.md). |

## <a name="doc-samples"></a>Exemplos de documento

Exemplos de código da equipe de Pesquisa Cognitiva demonstram recursos e fluxos de trabalho. Muitos desses exemplos são referenciados em tutoriais, guias de início rápido e artigos de instruções. Você pode encontrar esses exemplos em [**Azure-Samples/Azure-Search-java-Samples**](https://github.com/Azure-Samples/azure-search-java-samples) no github.

| Exemplos | Artigo | 
|---------|-------------|
| [TUTORIAIS](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Código-fonte para [início rápido: criar um índice de pesquisa em Java e REST](search-get-started-java.md). Este exemplo não foi atualizado para o SDK do Java. Ele chama as APIs REST. |

> [!Tip]
> Experimente o [navegador de exemplos](/samples/browse/?languages=java&products=azure-cognitive-search) para pesquisar exemplos de código da Microsoft no GitHub, filtrados por produto, serviço e idioma.

## <a name="other-samples"></a>Outras amostras

Os exemplos a seguir também são publicados pela equipe de Pesquisa Cognitiva, mas não são referenciados na documentação do. Os arquivos Leiame associados fornecem instruções de uso.

| Exemplos | Descrição |
|---------|-------------|
| [Search-java-Getting-Started](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Usa a biblioteca de cliente do SDK do Java para criar, carregar e consultar um índice de pesquisa. Este exemplo é autônomo no momento. |
| [pesquisa-Java-indexer-demonstração](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Demonstra um Azure Cosmos DB indexador em Java. Este exemplo não foi atualizado para o SDK do Java. Ele chama as APIs REST.|