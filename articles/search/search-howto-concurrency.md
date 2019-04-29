---
title: Como gerenciar gravações simultâneas em recursos – Azure Search
description: Use a simultaneidade otimista para evitar colisões de ar intermediário em atualizações ou exclusões para os índices do Azure Search, indexadores e fontes de dados.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4599498918b7a01a1207f20135c26924c6758eb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871358"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Como gerenciar a simultaneidade no Azure Search

Ao gerenciar recursos do Azure Search, como índices e fontes de dados, é importante atualizar recursos com segurança, especialmente se os recursos são acessados simultaneamente por diferentes componentes do seu aplicativo. Quando dois clientes atualizam simultaneamente um recurso sem coordenação, condições de corrida são possíveis. Para evitar isso, o Azure Search oferece uma *modelo de simultaneidade otimista*. Não há nenhum bloqueio em um recurso. Em vez disso, há uma ETag para todos os recursos que identifica a versão do recurso para que você possa criar solicitações que evitam substituições acidentais.

> [!Tip]
> Código conceitual em uma [solução C# de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) explica como funciona o controle de simultaneidade no Azure Search. O código cria condições que invocam o controle de simultaneidade. Ler o [fragmento de código a seguir](#samplecode) é provavelmente suficiente para a maioria dos desenvolvedores, mas se você deseja executar, edite appsettings.json para adicionar o nome do serviço e uma chave de api de administração. Dado um URL de serviço de `http://myservice.search.windows.net`, o nome do serviço é `myservice`.

## <a name="how-it-works"></a>Como ele funciona

A simultaneidade otimista é implementada por meio de verificações de condição de acesso nas chamadas à API gravando índices, indexadores, fontes de dados e recursos synonymMap.

Todos os recursos tiverem uma [ *marca da entidade (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) que fornece informações de versão do objeto. Verificando o ETag pela primeira vez, você pode evitar atualizações simultâneas em um fluxo de trabalho típico (obter, modificar localmente e atualizar), garantindo que a ETag do recurso coincida com sua cópia local.

+ A API REST usa um [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no cabeçalho da solicitação.
+ O SDK .NET define o ETag por meio de um objeto accessCondition, definindo o [Cabeçalho If-Match | If-Match-None](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no recurso. Qualquer objeto que herda de [IResourceWithETag (SDK do .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) possui um objeto accessCondition.

Sempre que você atualizar um recurso, a ETag é alterada automaticamente. Quando você implementa o gerenciamento de simultaneidade, tudo o que você está fazendo é colocar uma pré-condição na solicitação de atualização que exige o recurso remoto com o mesmo ETag como a cópia do recurso que você modificou no cliente. Se um processo simultâneo já alterou o recurso remoto, a ETag não corresponderá com a pré-condição e a solicitação falhará com HTTP 412. Se você estiver usando o SDK do .NET, isso se manifesta como uma `CloudException` onde o método de extensão `IsAccessConditionFailed()` retorna true.

> [!Note]
> Há apenas um mecanismo para simultaneidade. Ele sempre é usado, independentemente de qual API é usada para atualizações de recursos.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Casos de uso e código de exemplo

O código a seguir demonstra verificações accessCondition para operações de atualização da chave:

+ Falha de uma atualização se o recurso não existe mais
+ Falha de uma atualização se a versão do recurso muda

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Código de exemplo do [programa DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
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
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

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

Este snippet de código mostra a adição de um synonymMap para um índice que já existe. Esse código é do [sinônimos (visualização) C# exemplo para o Azure Search](search-synonyms-tutorial-sdk.md).

O snippet de código obtém o índice "hotéis", verifica a versão do objeto em uma operação de atualização, gera uma exceção se a condição falha e, em seguida, repete a operação (até três vezes), iniciando com a recuperação de índice do servidor para obter a versão mais recente.

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


## <a name="next-steps"></a>Próximas etapas

Examine o [exemplo sinônimos c#](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) para obter mais contexto sobre como atualizar um índice existente, com segurança.

Tente modificar qualquer um dos exemplos a seguir para incluir objetos ETags ou AccessCondition.

+ [Amostra de API REST no GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [Amostra do SDK do .NET no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Essa solução inclui o projeto "DotNetEtagsExplainer" que contém o código apresentado neste artigo.

## <a name="see-also"></a>Consulte também

[Cabeçalhos comuns de solicitação e resposta HTTP](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[Códigos de status HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[Operações de índice (API REST)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
