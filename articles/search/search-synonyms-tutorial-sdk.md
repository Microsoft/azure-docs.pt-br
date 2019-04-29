---
title: Sinônimos C# exemplo - Azure Search
description: Neste C# exemplo, saiba como adicionar o recurso de sinônimos para um índice no Azure Search. Um mapa de sinônimos é uma lista de termos equivalentes. Campos com suporte a sinônimos expandem consultas para incluir o termo fornecido pelo usuário e todos os sinônimos de relacionados.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f9cadfcf0c027f4aec4f9d4928872709ee7d3e99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61281770"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>Exemplo: Adicionar sinônimos para o Azure Search em C#

Os sinônimos expandem uma consulta correspondendo os termos considerados semanticamente equivalentes com o termo de entrada. Por exemplo, você pode querer que "carro" corresponda aos documentos contendo os termos "automóvel" ou "veículo". 

No Azure Search, os sinônimos são definidos em um *mapa de sinônimos*com *regras de mapeamento* que associam os termos equivalentes. Este exemplo aborda as etapas essenciais para adicionar e usar sinônimos com um índice existente. Você aprenderá como:

> [!div class="checklist"]
> * Habilitar sinônimos criando e postando regras de mapeamento 
> * Fazer referência a um mapa de sinônimos em uma cadeia de caracteres de consulta

Você pode criar vários mapas de sinônimos, publicá-los como um recurso de todo o serviço disponível em qualquer índice e fazer referência a qual deles usar no nível do campo. No momento da consulta, além de pesquisar um índice, o Azure Search faz uma pesquisa em um mapa de sinônimos, caso um seja especificado nos campos usados na consulta.

> [!NOTE]
> Os sinônimos têm suporte na API nas últimas versões de API e SDK (api-version=2017-11-11, versão de SDK 5.0.0). Não há suporte do portal do Azure no momento. Se o suporte do portal do Azure para sinônimos é útil para você, forneça seus comentários no [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos do tutorial incluem o seguinte:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Serviço do Azure Search](search-create-service-portal.md)
* [Biblioteca do .NET Microsoft.Azure.Search](https://aka.ms/search-sdk)
* [Como usar o Azure Search de um aplicativo .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Visão geral

As contas antes e depois demonstram o valor dos sinônimos. Neste exemplo, use um aplicativo de exemplo que executa consultas e retorna os resultados em um índice de exemplo. O aplicativo de exemplo cria um pequeno índice chamado "hotéis" preenchido com dois documentos. O aplicativo executa as consultas de pesquisa usando termos e frases que não aparecem no índice, habilita o recurso de sinônimos e emite as mesmas pesquisas novamente. O código abaixo demonstra o fluxo geral.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
As etapas para criar e preencher o índice de exemplo são explicadas em [Como usar o Azure Search a partir de um Aplicativo .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Consultas "antes"

Em `RunQueriesWithNonExistentTermsInIndex`, emitimos as consultas de pesquisa com "cinco estrelas", "Internet" e "economia E hotel".
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Nenhum dos dois documentos indexados contêm os termos, portanto, recebemos a seguinte saída da primeira `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Habilitar sinônimos

A habilitação dos sinônimos é um processo com duas etapas. Primeiro, definimos e carregamos as regras do sinônimo, em seguida, configuramos os campos para usá-las. O processo é descrito em `UploadSynonyms` e `EnableSynonymsInHotelsIndex`.

1. Adicione um mapa de sinônimos ao serviço de pesquisa. Em `UploadSynonyms`, definimos quatro regras no nosso mapa sinônimos 'desc synonymmap' e carregamos no serviço.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Um mapa de sinônimos deve estar de acordo com o formato padrão do código-fonte aberto`solr`. O formato é explicado em [Sinônimos no Azure Search](search-synonyms.md) na seção `Apache Solr synonym format`.

2. Configure os campos de pesquisa para usar o mapa de sinônimos na definição do índice. Em `EnableSynonymsInHotelsIndex`, habilitamos os sinônimos nos dois campos `category` e `tags` definindo a propriedade `synonymMaps` para o nome do mapa de sinônimos recém-carregado.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Quando você adiciona um mapa de sinônimos, as recompilações do índice não são necessárias. Você pode adicionar um mapa de sinônimos ao serviço, em seguida, corrigir as definições do campo existentes em qualquer índice para usar o novo mapa de sinônimos. A adição de novos atributos não tem impacto sobre a disponibilidade do índice. O mesmo se aplica ao desabilitar os sinônimos para um campo. Você pode simplesmente definir a propriedade `synonymMaps` para uma lista vazia.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>Consultas "depois"

Depois do mapa de sinônimos ser carregado e o índice ser atualizado para usar o mapa de sinônimos, a segunda chamada `RunQueriesWithNonExistentTermsInIndex` gera o seguinte:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
A primeira consulta encontra o documento a partir da regra `five star=>luxury`. A segunda consulta expande a pesquisa usando `internet,wifi` e a terceira usando `hotel, motel` e `economy,inexpensive=>budget` ao encontrar os documentos correspondentes.

Adicionar sinônimos muda completamente a experiência de pesquisa. Neste exemplo, as consultas originais falharam ao retornar resultados significativos, mesmo que os documentos em nosso índice tenham sido relevantes. Habilitando os sinônimos, podemos expandir um índice para incluir os termos de uso comum, sem alterações nos dados subjacentes no índice.

## <a name="sample-application-source-code"></a>Código-fonte do aplicativo de exemplo
Você pode encontrar o código-fonte completo do aplicativo de exemplo usado neste passo a passo no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida para limpar após um exemplo é excluindo o grupo de recursos que contém o serviço de Azure Search. Você pode excluir o grupo de recursos agora para excluir permanentemente todo o conteúdo. No portal, o nome do grupo de recursos está na página Visão geral do serviço Azure Search.

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstrou a [API REST dos sinônimos](https://aka.ms/rgm6rq) em C# código para criar e lançar as regras de mapeamento e, em seguida, chamar o mapa de sinônimos em uma consulta. Informações adicionais podem ser encontradas na documentação de referência do [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e da [API REST](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Como usar sinônimos no Azure Search](search-synonyms.md)
