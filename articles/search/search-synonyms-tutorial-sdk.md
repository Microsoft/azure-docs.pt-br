---
title: Exemplo de sinônimos em C#
titleSuffix: Azure Cognitive Search
description: Neste exemplo de C#, saiba como adicionar o recurso de sinônimos a um índice no Azure Pesquisa Cognitiva. Um mapa de sinônimos é uma lista de termos equivalentes. Campos com suporte a sinônimos expandem consultas para incluir o termo fornecido pelo usuário e todos os sinônimos de relacionados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704649"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Exemplo: Adicionar sinônimos para o Azure Pesquisa Cognitiva em C #

Os sinônimos expandem uma consulta correspondendo os termos considerados semanticamente equivalentes com o termo de entrada. Por exemplo, você pode querer que "carro" corresponda aos documentos contendo os termos "automóvel" ou "veículo". 

No Azure Pesquisa Cognitiva, os sinônimos são definidos em um *mapa de sinônimos*, por meio de *regras de mapeamento* que associam termos equivalentes. Este exemplo aborda as etapas essenciais para adicionar e usar sinônimos com um índice existente.

Neste exemplo, você aprenderá a:

> [!div class="checklist"]
> * Crie um mapa de sinônimos usando a [classe SynonymMap](/dotnet/api/azure.search.documents.indexes.models.synonymmap). 
> * Defina a [Propriedade SynonymMapsName](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) em campos que devem dar suporte à expansão de consulta por meio de sinônimos.

Você pode consultar um campo habilitado para sinônimo como faria normalmente. Não há nenhuma sintaxe de consulta adicional necessária para acessar sinônimos.

Você pode criar vários mapas de sinônimos, publicá-los como um recurso de todo o serviço disponível em qualquer índice e fazer referência a qual deles usar no nível do campo. No momento da consulta, além de Pesquisar um índice, o Azure Pesquisa Cognitiva faz uma pesquisa em um mapa de sinônimos, se um for especificado em campos usados na consulta.

> [!NOTE]
> Os sinônimos podem ser criados programaticamente, mas não no Portal.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos do tutorial incluem o seguinte:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Serviço de Pesquisa Cognitiva do Azure](search-create-service-portal.md)
* [Azure.Search.Docpacote uments](https://www.nuget.org/packages/Azure.Search.Documents/)

Se você não estiver familiarizado com a biblioteca de cliente .NET, consulte [como usar o Azure pesquisa cognitiva no .net](search-howto-dotnet-sdk.md).

## <a name="sample-code"></a>Código de exemplo

Você pode encontrar o código-fonte completo do aplicativo de exemplo usado neste exemplo no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="overview"></a>Visão geral

As consultas antes e depois são usadas para demonstrar o valor de sinônimos. Neste exemplo, um aplicativo de exemplo executa consultas e retorna resultados em um índice de exemplo "Hotéis" preenchido com dois documentos. Primeiro, o aplicativo executa consultas de pesquisa usando termos e frases que não aparecem no índice. Em segundo lugar, o código habilita o recurso de sinônimos e, em seguida, emite novamente as mesmas consultas, desta vez retornando resultados com base nas correspondências no mapa de sinônimos. 

O código abaixo demonstra o fluxo geral.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>Consultas "antes"

Em `RunQueriesWithNonExistentTermsInIndex`, emitimos as consultas de pesquisa com "cinco estrelas", "Internet" e "economia E hotel".

As consultas de frase, como "cinco estrelas", devem ser colocadas entre aspas e também podem precisar de caracteres de escape, dependendo do cliente.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

Nenhum dos dois documentos indexados contém os termos, então obtemos a seguinte saída do primeiro `RunQueriesWithNonExistentTermsInIndex` :  **nenhum documento foi correspondido**.

## <a name="enable-synonyms"></a>Habilitar sinônimos

Depois que as consultas "Before" são executadas, o código de exemplo permite sinônimos. A habilitação dos sinônimos é um processo com duas etapas. Primeiro, defina e carregue as regras de sinônimo. Em segundo lugar, configure os campos para usá-los. O processo é descrito em `UploadSynonyms` e `EnableSynonymsInHotelsIndex`.

1. Adicione um mapa de sinônimos ao serviço de pesquisa. Em `UploadSynonyms`, definimos quatro regras no nosso mapa sinônimos 'desc synonymmap' e carregamos no serviço.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Configure os campos de pesquisa para usar o mapa de sinônimos na definição do índice. Em `AddSynonymMapsToFields`, habilitamos os sinônimos nos dois campos `category` e `tags` definindo a propriedade `SynonymMapNames` para o nome do mapa de sinônimos recém-carregado.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   Quando você adiciona um mapa de sinônimos, as recompilações do índice não são necessárias. Você pode adicionar um mapa de sinônimos ao serviço, em seguida, corrigir as definições do campo existentes em qualquer índice para usar o novo mapa de sinônimos. A adição de novos atributos não tem impacto sobre a disponibilidade do índice. O mesmo se aplica ao desabilitar os sinônimos para um campo. Você pode simplesmente definir a propriedade `SynonymMapNames` para uma lista vazia.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>Consultas "depois"

Depois do mapa de sinônimos ser carregado e o índice ser atualizado para usar o mapa de sinônimos, a segunda chamada `RunQueriesWithNonExistentTermsInIndex` gera o seguinte:

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

A primeira consulta encontra o documento a partir da regra `five star=>luxury`. A segunda consulta expande a pesquisa usando `internet,wifi` e a terceira usando `hotel, motel` e `economy,inexpensive=>budget` ao encontrar os documentos correspondentes.

Adicionar sinônimos muda completamente a experiência de pesquisa. Neste exemplo, as consultas originais não retornaram resultados significativos, embora os documentos em nosso índice fossem relevantes. Habilitando os sinônimos, podemos expandir um índice para incluir os termos de uso comum, sem alterações nos dados subjacentes no índice.

## <a name="clean-up-resources"></a>Limpar os recursos

A maneira mais rápida de limpar após um exemplo é excluir o grupo de recursos que contém o serviço de Pesquisa Cognitiva do Azure. Você pode excluir o grupo de recursos agora para excluir permanentemente todo o conteúdo. No portal, o nome do grupo de recursos está na página Visão geral do serviço da Pesquisa Cognitiva do Azure.

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstrou o recurso de sinônimos no código C# para criar e postar regras de mapeamento e, em seguida, chamar o mapa de sinônimos em uma consulta. Informações adicionais podem ser encontradas na documentação de referência do [SDK do .NET](/dotnet/api/overview/azure/search.documents-readme) e da [API REST](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Como usar sinônimos no Azure Pesquisa Cognitiva](search-synonyms.md)