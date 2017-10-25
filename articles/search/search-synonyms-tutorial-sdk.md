---
title: "Tutorial para visualizar sinônimos no Azure Search | Microsoft Docs"
description: "Adicione o recurso de visualização de sinônimos a um índice no Azure Search."
services: search
manager: jhubbard
documentationcenter: 
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 03/31/2017
ms.author: heidist
ms.openlocfilehash: 014959ed471f796d2184f0f8ff10d15cdc8a2ec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="synonym-preview-c-tutorial-for-azure-search"></a>Tutorial C# de sinônimos (visualização) do Azure Search

Os sinônimos expandem uma consulta correspondendo os termos considerados semanticamente equivalentes com o termo de entrada. Por exemplo, você pode querer que "carro" corresponda aos documentos contendo os termos "automóvel" ou "veículo".

No Azure Search, os sinônimos são definidos em um *mapa de sinônimos*com *regras de mapeamento* que associam os termos equivalentes. Você pode criar vários mapas de sinônimos, publicá-los como um recurso de todo o serviço disponível em qualquer índice e fazer referência a qual deles usar no nível do campo. No momento da consulta, além de pesquisar um índice, o Azure Search faz uma pesquisa em um mapa de sinônimos, caso um seja especificado nos campos usados na consulta.

> [!NOTE]
> O recurso de sinônimos está atualmente na visualização e só tem suporte nas versões do SDK e da API de visualização mais recentes (api-version=2016-09-01-Preview, SDK versão 4.x-preview). Não há nenhum suporte do portal do Azure no momento. As APIs de visualização não estão no SLA e os recursos da visualização podem mudar, portanto, não recomendamos usá-los nos aplicativos de produção.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos do tutorial incluem o seguinte:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Serviço do Azure Search](search-create-service-portal.md)
* [Versão de visualização da biblioteca Microsoft.Azure.Search.NET](https://aka.ms/search-sdk-preview)
* [Como usar o Azure Search a partir de um Aplicativo .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Visão geral

As contas antes e depois demonstram o valor dos sinônimos. Neste tutorial, usamos um aplicativo de exemplo que executa as consultas e retorna os resultados em um índice de exemplo. O aplicativo de exemplo cria um pequeno índice chamado "hotéis" preenchido com dois documentos. O aplicativo executa as consultas de pesquisa usando termos e frases que não aparecem no índice, habilita o recurso de sinônimos e emite as mesmas pesquisas novamente. O código abaixo demonstra o fluxo geral.

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

Em `RunQueriesWithNonExistentTermsInIndex`, emitimos as consultas de pesquisa com "cinco estrelas", "Internet" e "economia e hotel".
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

Adicionar sinônimos muda completamente a experiência de pesquisa. Neste tutorial, as consultas originais falharam ao retornar resultados significativos, mesmo que os documentos em nosso índice tenham sido relevantes. Habilitando os sinônimos, podemos expandir um índice para incluir os termos de uso comum, sem alterações nos dados subjacentes no índice.

## <a name="sample-application-source-code"></a>Código-fonte do aplicativo de exemplo
Você pode encontrar o código-fonte completo do aplicativo de exemplo usado neste passo a passo no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="next-steps"></a>Próximas etapas

* Examine [Como usar sinônimos no Azure Search](search-synonyms.md)
* Examine [Documentação API REST dos sinônimos](https://aka.ms/rgm6rq)
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).
