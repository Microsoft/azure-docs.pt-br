---
title: Como gerenciar gravações simultâneas em recursos
titleSuffix: Azure Cognitive Search
description: Use a simultaneidade otimista para evitar colisões de ar médio em atualizações ou exclusões para índices de Pesquisa Cognitiva do Azure, indexadores, fontes de dados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 52260facc1a458a3ef18b067e6d52e189f5d329c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881747"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Como gerenciar a simultaneidade no Azure Pesquisa Cognitiva

Ao gerenciar recursos do Azure Pesquisa Cognitiva como índices e fontes de dados, é importante atualizar os recursos com segurança, especialmente se os recursos forem acessados simultaneamente por diferentes componentes do seu aplicativo. Quando dois clientes atualizam simultaneamente um recurso sem coordenação, condições de corrida são possíveis. Para evitar isso, o Azure Pesquisa Cognitiva oferece um *modelo de simultaneidade otimista*. Não há nenhum bloqueio em um recurso. Em vez disso, há uma ETag para cada recurso que identifica a versão do recurso para que você possa formular solicitações que evitem substituições acidentais.

> [!Tip]
> O código conceitual em uma [solução C# de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) explica como o controle de simultaneidade funciona no Azure pesquisa cognitiva. O código cria condições que invocam o controle de simultaneidade. Ler o [fragmento de código abaixo](#samplecode) pode ser suficiente para a maioria dos desenvolvedores, mas se você quiser executá-lo, edite appsettings.jsem para adicionar o nome do serviço e uma chave de API de administração. Dado um URL de serviço de `http://myservice.search.windows.net`, o nome do serviço é `myservice`.

## <a name="how-it-works"></a>Como ele funciona

A simultaneidade otimista é implementada por meio de verificações de condição de acesso em chamadas à API gravando em índices, indexadores, fontes de dados, habilidades e recursos synonymMap.

Todos os recursos tiverem uma [*marca da entidade (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag) que fornece informações de versão do objeto. Verificando o ETag pela primeira vez, você pode evitar atualizações simultâneas em um fluxo de trabalho típico (obter, modificar localmente e atualizar), garantindo que a ETag do recurso coincida com sua cópia local.

+ A API REST usa um [ETag](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no cabeçalho da solicitação.

+ O SDK .NET define o ETag por meio de um objeto accessCondition, definindo o [Cabeçalho If-Match | If-Match-None](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no recurso. Objetos que usam ETags, como [SynonymMap. ETag](/dotnet/api/azure.search.documents.indexes.models.synonymmap.etag) e [SearchIndex. ETag](/dotnet/api/azure.search.documents.indexes.models.searchindex.etag), têm um objeto accessCondition.

Sempre que você atualizar um recurso, a ETag é alterada automaticamente. Quando você implementa o gerenciamento de simultaneidade, tudo o que você está fazendo é colocar uma pré-condição na solicitação de atualização que exige o recurso remoto com o mesmo ETag como a cópia do recurso que você modificou no cliente. Se um processo simultâneo já alterou o recurso remoto, a ETag não corresponderá com a pré-condição e a solicitação falhará com HTTP 412. Se você estiver usando o SDK do .NET, isso se manifesta como uma `CloudException` onde o método de extensão `IsAccessConditionFailed()` retorna true.

> [!Note]
> Há apenas um mecanismo para simultaneidade. Ele sempre é usado, independentemente de qual API é usada para atualizações de recursos.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Casos de uso e código de exemplo

O código a seguir demonstra verificações accessCondition para operações de atualização da chave:

+ Falha de uma atualização se o recurso não existe mais
+ Falha de uma atualização se a versão do recurso muda

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Código de exemplo do [programa DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```csharp
class Program
{
    // This sample shows how ETags work by performing conditional updates and deletes
    // on an Azure Cognitive Search index.
    static void Main(string[] args)
    {
        IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
        IConfigurationRoot configuration = builder.Build();

        SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

        Console.WriteLine("Deleting index...\n");
        DeleteTestIndexIfExists(serviceClient);

        // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
        // of the resource you're working on. When you first create a resource such as an index, its ETag is
        // empty.
        Index index = DefineTestIndex();
        Console.WriteLine(
            $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

        // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
        // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
        // blank ETag.
        Console.WriteLine("Creating index...\n");
        index = serviceClient.Indexes.Create(index);
        
        Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

        // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
        // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
        // succeed if the index already exists.
        index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
        index =
            serviceClient.Indexes.CreateOrUpdate(
                index,
                accessCondition: AccessCondition.GenerateIfExistsCondition());

        Console.WriteLine(
            $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

        // More importantly, ETags protect you from concurrent updates to the same resource. If another
        // client tries to update the resource, it will fail as long as all clients are using the right
        // access conditions.
        Index indexForClient1 = index;
        Index indexForClient2 = serviceClient.Indexes.Get("test");

        Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
        Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

        // Client 1 successfully updates the index.
        indexForClient1.Fields.Add(new Field("a", DataType.Int32));
        indexForClient1 =
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient1,
                accessCondition: AccessCondition.IfNotChanged(indexForClient1));

        Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

        // Client 2 tries to update the index, but fails, thanks to the ETag check.
        try
        {
            indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient2,
                accessCondition: AccessCondition.IfNotChanged(indexForClient2));

            Console.WriteLine("Whoops; This shouldn't happen");
            Environment.Exit(1);
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine("Client 2 failed to update the index, as expected.");
        }

        // You can also use access conditions with Delete operations. For example, you can implement an
        // atomic version of the DeleteTestIndexIfExists method from this sample like this:
        Console.WriteLine("Deleting index...\n");
        serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

        // This is slightly better than using the Exists method since it makes only one round trip to
        // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
        // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
        // Azure Cognitive Search is idempotent.

        // And we're done! Bye!
        Console.WriteLine("Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

    private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
    {
        string searchServiceName = configuration["SearchServiceName"];
        string adminApiKey = configuration["SearchServiceAdminApiKey"];

        SearchServiceClient serviceClient =
            new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
        return serviceClient;
    }

    private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("test"))
        {
            serviceClient.Indexes.Delete("test");
        }
    }

    private static Index DefineTestIndex() =>
        new Index()
        {
            Name = "test",
            Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
        };
    }
}
```

## <a name="design-pattern"></a>Padrão de design

Um padrão de design para implementar simultaneidade otimista deve incluir um loop que repete a verificação da condição de acesso, um teste para a condição de acesso e, opcionalmente, recupera um recurso atualizado antes de tentar aplicar novamente as alterações.

Este snippet de código mostra a adição de um synonymMap para um índice que já existe. Esse código é do [exemplo de sinônimo C# para pesquisa cognitiva do Azure](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/v10/DotNetHowToSynonyms).

O snippet de código obtém o índice "hotéis", verifica a versão do objeto em uma operação de atualização, gera uma exceção se a condição falha e, em seguida, repete a operação (até três vezes), iniciando com a recuperação de índice do servidor para obter a versão mais recente.

```csharp
private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
{
    int MaxNumTries = 3;

    for (int i = 0; i < MaxNumTries; ++i)
    {
        try
        {
            Index index = serviceClient.Indexes.Get("hotels");
            index = AddSynonymMapsToFields(index);

            // The IfNotChanged condition ensures that the index is updated only if the ETags match.
            serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

            Console.WriteLine("Updated the index successfully.\n");
            break;
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
        }
    }
}

private static Index AddSynonymMapsToFields(Index index)
{
    index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
    index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
    return index;
}
```

## <a name="next-steps"></a>Próximas etapas

Tente modificar outros exemplos para exercitar os objetos ETags ou AccessCondition.

+ [Search-dotnet-Getting-Started on GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Esse repositório inclui o projeto "DotNetEtagsExplainer".

+ [Azure-Search-dotnet-Samples no GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples) contém exemplos C# adicionais.

## <a name="see-also"></a>Veja também

+ [Cabeçalhos de resposta e solicitação HTTP comuns](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
+ [Códigos de status HTTP](/rest/api/searchservice/http-status-codes)
+ [Operações de índice (API REST)](/rest/api/searchservice/index-operations)