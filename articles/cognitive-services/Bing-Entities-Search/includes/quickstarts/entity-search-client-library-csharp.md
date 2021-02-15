---
title: Início rápido da biblioteca de clientes C# da Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 27951a6da2c5e4a9d17e8b332d8ee9706701649d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88931873"
---
Use este início rápido para começar a pesquisar entidades com a biblioteca de clientes da Pesquisa de Entidade do Bing para o C#. Embora a Pesquisa de Entidade do Bing tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca de clientes é uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/).
* A estrutura [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote do NuGet.
* Se você estiver usando Linux/MacOS, este aplicativo poderá ser executado usando [Mono](https://www.mono-project.com/).
* O [pacote NuGet do SDK de Pesquisa de Notícias do Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). A instalação desse pacote também instala o seguinte:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Para adicionar a biblioteca de clientes da Pesquisa de Entidade do Bing ao projeto do Visual Studio, use a opção **Gerenciar Pacotes NuGet** no **Gerenciador de Soluções** e, em seguida, adicione o pacote `Microsoft.Azure.CognitiveServices.Search.EntitySearch`.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Criar e inicializar um aplicativo

1. crie uma solução de console C# no Visual Studio. Em seguida, adicione os itens a seguir ao arquivo de código principal.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Criar um cliente e enviar uma solicitação de pesquisa

1. Crie um cliente de pesquisa. Adicione a chave de assinatura criando `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Use a função `Entities.Search()` do cliente para pesquisar na consulta:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Obter e imprimir uma descrição de entidade

1. Se a API retornou os resultados da pesquisa, obtenha a entidade principal em `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Imprimir a descrição da entidade principal 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade do Bing?](../../overview.md )
