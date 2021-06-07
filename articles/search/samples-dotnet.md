---
title: Amostras do .NET
titleSuffix: Azure Cognitive Search
description: Encontre exemplos de código C# de demonstração do Azure Pesquisa Cognitiva que usam as bibliotecas de cliente .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: cbb84a4934eed4d258cf07772753315785f98019
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99218155"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Exemplos de código .NET (C#) para o Azure Pesquisa Cognitiva

Saiba mais sobre os exemplos de código do C# que demonstram a funcionalidade e o fluxo de trabalho de uma solução de Pesquisa Cognitiva do Azure. Esses exemplos usam a [**biblioteca de cliente do azure pesquisa cognitiva**](/dotnet/api/overview/azure/search) para o [**SDK do Azure para .net**](/dotnet/azure/), que pode ser explorado por meio dos links a seguir.

| Destino | Link |
|--------|------|
| Download do pacote | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| Referência de API | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| Casos de teste de API | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/testes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Código-fonte | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>Exemplos do SDK

Exemplos de código da equipe de desenvolvimento do SDK do Azure demonstram o uso da API. Você pode encontrar esses exemplos em [**Azure/Azure-SDK-for-net/Tree/Master/SDK/Search/Azure.Search.Documents/Samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) no github.

| Exemplos | Descrição |
|---------|-------------|
| ["Olá, mundo", de forma síncrona](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Demonstra como criar um cliente, autenticar e tratar erros usando métodos síncronos.|
| ["Olá, mundo", de forma assíncrona](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Demonstra como criar um cliente, autenticar e tratar erros usando métodos assíncronos.  |
| [Operações de nível de serviço](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Demonstra como criar índices, indexadores, fontes de dados, habilidades e mapas de sinônimos. Este exemplo também mostra como obter estatísticas de serviço e como consultar um índice.  |
| [Operações de índice](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Demonstra como executar uma ação no índice existente, nesse caso, obter uma contagem de documentos armazenados no índice.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Demonstra uma técnica para trabalhar com tipos de dados sem suporte.  |
| [Indexando documentos (modelo de push)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indexação de modelo "Push", em que você envia uma carga JSON para um índice em um serviço.   |
| [Exemplo de chave de criptografia](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Demonstra o uso de uma chave de criptografia gerenciada pelo cliente para adicionar uma camada extra de proteção por conteúdo confidencial.  |

## <a name="doc-samples"></a>Exemplos de documento

Exemplos de código da equipe de Pesquisa Cognitiva demonstram recursos e fluxos de trabalho. Muitos desses exemplos são referenciados em tutoriais, guias de início rápido e artigos de instruções. Você pode encontrar esses exemplos em [**Azure-Samples/Azure-Search-dotnet-Samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) e no [**Azure-Samples/Search-dotnet-Getting-Started**](https://github.com/Azure-Samples/search-dotnet-getting-started/) on github.

| Exemplos | Artigo  |
|---------|-------------|
| [TUTORIAIS](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Código-fonte para [início rápido: criar um índice de pesquisa ](search-get-started-dotnet.md). Este artigo aborda o fluxo de trabalho básico para criar, carregar e consultar um índice de pesquisa usando dados de exemplo. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Código-fonte de [como usar a biblioteca de cliente .net](search-howto-dotnet-sdk.md). Este artigo percorre o fluxo de trabalho básico, mas com mais detalhes e a discussão sobre o uso da API.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Código-fonte por [exemplo: Adicionar sinônimos em C#](search-synonyms-tutorial-sdk.md). As listas de sinônimos são usadas para expansão de consulta, fornecendo termos de correspondência externos a um índice. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Código-fonte para [tutorial: indexe dados SQL do Azure usando o SDK do .net](search-indexer-tutorial.md). Este artigo mostra como configurar um indexador do SQL Azure que tem um agendamento, mapeamentos de campo e parâmetros.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Código-fonte de [como configurar chaves gerenciadas pelo cliente para criptografia de dados](search-security-manage-encryption-keys.md). |
| [Criar seu primeiro aplicativo em C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Código-fonte para [tutorial: Crie seu primeiro aplicativo de pesquisa](tutorial-csharp-create-first-app.md). Embora a maioria dos exemplos sejam aplicativos de console, este exemplo MVC usa uma página da Web para antecipar o índice de hotéis de exemplo, demonstrando pesquisa básica, paginação, preenchimento automático e consultas sugeridas, facetas e filtros. |
| [fontes de dados múltiplos](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Código-fonte para [tutorial: índice de várias fontes de dados](tutorial-multiple-data-sources.md). |
|  [otimização de dados-indexação](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Código-fonte para [tutorial: otimizar a indexação com a API de envio por push](tutorial-optimize-indexing-push-api.md).  |
| [tutorial – reenriquecimento de ia](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Código-fonte para [tutorial: conteúdo pesquisável gerado por ia de BLOBs do Azure usando o SDK do .net](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Experimente o [navegador de exemplos](/samples/browse/?languages=csharp&products=azure-cognitive-search) para pesquisar exemplos de código da Microsoft no GitHub, filtrados por produto, serviço e idioma.

## <a name="other-samples"></a>Outras amostras

Os exemplos a seguir também são publicados pela equipe de Pesquisa Cognitiva, mas não são referenciados na documentação do. Os arquivos Leiame associados fornecem instruções de uso.

| Exemplos | Descrição |
|---------|-------------|
| [Azure-Search-Power-Skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Código-fonte para habilidades personalizadas consumíveis que você pode incorporar em suas soluções ganhas.  |
| [Acelerador de solução de mineração de conhecimento](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Inclui modelos, arquivos de suporte e relatórios analíticos para ajudá-lo a desenvolver um protótipo de uma solução de mineração de conhecimento de ponta a ponta.  |
| [Repositório de aplicativos de pesquisa do Covid-19](https://github.com/liamca/covid19search) | Repositório de código-fonte para o [aplicativo de pesquisa Covid-19](https://covid19search.azurewebsites.net/) baseado em pesquisa cognitiva |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Saiba mais sobre a [solução JFK](https://www.microsoft.com/ai/ai-lab-jfk-files). |
| [Pesquisar + acelerador de QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Uma [solução](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381) que combina o poder da pesquisa e QnA Maker. Consulte o [site de demonstração](https://aka.ms/qnaWithAzureSearchDemo)ao vivo. |