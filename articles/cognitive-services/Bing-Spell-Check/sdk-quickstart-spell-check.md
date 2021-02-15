---
title: 'Início Rápido: Verificar a ortografia com o SDK de Verificação Ortográfica do Bing para C#'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API REST de Verificação Ortográfica do Bing para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 5194dab21842e47d2bf2445c69ccaeec3cb78e4f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943416"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Início Rápido: Verificar a ortografia com o SDK de Verificação Ortográfica do Bing para C#

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use este Início Rápido para iniciar a verificação ortográfica com o SDK de Verificação Ortográfica do Bing para C#. Embora a Verificação Ortográfica do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Dependências de aplicativo

* Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/).
* O [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) da Verificação Ortográfica do Bing

Para adicionar o SDK de Verificação Ortográfica do Bing ao seu projeto, selecione **Gerenciar Pacotes do NuGet** no **Gerenciador de Soluções** do Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. O pacote também instala as seguintes dependências:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie uma solução de console C# no Visual Studio. Em seguida, adicione a instrução `using` a seguir.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Crie uma classe. Em seguida, crie uma função assíncrona chamada `SpellCheckCorrection()` que usa uma chave de assinatura e envia a solicitação de verificação ortográfica.

3. Crie uma instância do cliente criando um objeto `ApiKeyServiceClientCredentials`. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Enviar a solicitação e ler a resposta

1. Na função criada acima, execute as etapas a seguir. Envie a solicitação de verificação ortográfica com o cliente. Adicione o texto a ser verificado ao parâmetro `text` e defina o modo como `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Obtenha o primeiro resultado da verificação ortográfica, se houver um. Imprima a primeira palavra com ortografia incorreta (token) retornada, o tipo de token e o número de sugestões.

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Obtenha a primeira correção sugerida, se houver. Imprima a pontuação de sugestão e a palavra sugerida. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>Executar o aplicativo

Compile e execute seu projeto. Se você estiver usando o Visual Studio, pressione **F5** para depurar o arquivo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](tutorials/spellcheck.md)

- [O que é API de Verificação Ortográfica do Bing?](overview.md)
- [Guia de referência de SDK do C# da Verificação Ortográfica do Bing](/dotnet/api/overview/azure/cognitiveservices/bing-spell-check-readme)
