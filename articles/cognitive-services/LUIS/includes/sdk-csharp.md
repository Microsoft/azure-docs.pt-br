---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: fd47d5df053931c343fd811fe4d93b66f080f225
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947573"
---
Use as bibliotecas de clientes do LUIS (Reconhecimento Vocal) para .NET para:
* Criar um aplicativo
* Adicionar uma intenção e uma entidade com machine learning com um exemplo de enunciado
* Treinar e publicar o aplicativo
* Runtime de previsão da consulta

[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding) | [Criação](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) e [Previsão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) Código-fonte da biblioteca | NuGet de [Criação](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) e [Previsão](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [Exemplo de C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/sdk-3x//Program.cs)

## <a name="prerequisites"></a>Pré-requisitos

* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) e da [CLI do .NET Core](/dotnet/core/tools/).
* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Quando você tiver sua assinatura do Azure, [crie um recurso de criação do Reconhecimento vocal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) no portal do Azure para obter a chave e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você [criar](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) para conectar seu aplicativo à criação do Reconhecimento vocal. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido. Você pode usar o tipo de preço gratuito (`F0`) para experimentar o serviço.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Crie um novo aplicativo .NET Core em seu IDE ou editor preferido.

1. Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `new` para criar um novo aplicativo do console com o nome `language-understanding-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Altere o diretório para a pasta do aplicativo recém-criado.

    ```dotnetcli
    cd language-understanding-quickstart
    ```

1. É possível criar o aplicativo com:

    ```dotnetcli
    dotnet build
    ```

    A saída de compilação não deve conter nenhum aviso ou erro.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-nuget-libraries"></a>Instalar as bibliotecas NuGet

Dentro do diretório do aplicativo, instale a biblioteca de clientes do LUIS (Reconhecimento Vocal) para .NET com os seguintes comandos:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.2.0-preview.3
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.1.0-preview.1
```

## <a name="authoring-object-model"></a>Modelo de Objeto de Criação

O cliente de criação do LUIS (Reconhecimento Vocal) é um objeto [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient) que se autentica no Azure, que contém sua chave de criação.

## <a name="code-examples-for-authoring"></a>Exemplos de código para criação

Depois que o cliente for criado, use-o para acessar a funcionalidade, incluindo:

* Aplicativos – [Criar](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync), [excluir](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync), [publicar](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync)
* Exemplo de enunciados – [adicionar](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions), [excluir por ID](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync)
* Recursos – Gerenciar [listas de frases](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync)
* Modelo – Gerenciar [intenções](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions) e entidades
* Padrão – Gerenciar [padrões](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions)
* Treinar – [Treinar](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync) o aplicativo e sondar o [status de treinamento](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync)
* [Versões](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions) – Gerenciar com clonar, exportar e excluir

## <a name="prediction-object-model"></a>Modelo de Objeto de Previsão

O cliente de runtime de previsão do LUIS (Reconhecimento Vocal) é um objeto [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient) que se autentica no Azure, que contém sua chave de recurso.

## <a name="code-examples-for-prediction-runtime"></a>Exemplos de código para runtime de previsão

Depois que o cliente for criado, use-o para acessar a funcionalidade, incluindo:

* Previsão por [slot de preparo ou produção](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync)
* Previsão por [versão](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync)


[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo *Program.cs* no IDE ou no editor de sua preferência. Substitua o código `using` existente pelas seguintes diretivas de `using`:

[!code-csharp[Add NuGet libraries to code file](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Adicionar o código clichê

1. Altere a assinatura do método `Main` para permitir chamadas assíncronas:

    ```csharp
    public static async Task Main()
    ```

1. Adicione o restante do código no método `Main` da classe `Program`, a menos que especificado de outra forma.

## <a name="create-variables-for-the-app"></a>Criar variáveis para o aplicativo

Crie dois conjuntos de variáveis: o primeiro conjunto, você vai alterar; o segundo, você vai deixar como ele aparece no exemplo de código. 

1. Crie variáveis para manter a chave de criação e os nomes do recurso.

    [!code-csharp[Variables you need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouChange)]

1. Crie variáveis para armazenar os pontos de extremidade, o nome do aplicativo, a versão e o nome da intenção.

    [!code-csharp[Variables you don't need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials) com a sua chave e use-o com o ponto de extremidade para criar um objeto [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient).

[!code-csharp[Authenticate the client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar um aplicativo LUIS

Um aplicativo LUIS contém o modelo de NLP (processamento em idioma natural), incluindo intenções, entidades e exemplos de enunciados.

Crie um [ApplicationCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject). O nome e a cultura do idioma são propriedades obrigatórias. Chame o método [Apps.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync). A resposta é a ID do aplicativo.

[!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Criar intenção para o aplicativo
O objeto principal no modelo de um aplicativo do LUIS é a intenção. A intenção se alinha com um agrupamento de _intenções_ de enunciado do usuário. Um usuário pode fazer uma pergunta ou uma declaração procurando uma resposta _desejada_ específica de um bot (ou outro aplicativo cliente). Exemplos de intenções são: reservar um voo, perguntar sobre o tempo em uma cidade de destino e solicitar informações de contato para o atendimento ao cliente.

Crie um [ModelCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject) com o nome da intenção exclusiva e, em seguida, passe a ID do aplicativo, a ID da versão e o método ModelCreateObject para o método [Model.AddIntentAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync). A resposta é a ID da intenção.

O valor de `intentName` é embutido em código em `OrderPizzaIntent` como parte das variáveis da seção [Criar variáveis para o aplicativo](#create-variables-for-the-app).

[!code-csharp[Create intent for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Criar entidades para o aplicativo

Embora as entidades não sejam necessárias, são encontradas na maioria dos aplicativos. A entidade extrai informações do enunciado do usuário, necessárias para atender à intenção do usuário. Há vários tipos de entidades [predefinidas](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync) e personalizadas, cada uma com seus próprios modelos de DTO (objeto de transformação de dados).  Entidades predefinidas comuns para adicionar ao seu aplicativo [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

É importante saber que as entidades não estão marcadas com uma intenção. Em geral, elas podem se aplicar a muitas intenções. Somente os enunciados de usuário de exemplo são marcados para uma intenção específica.

Os métodos de criação para as entidades fazem parte da classe do [Modelo](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions). Cada tipo de entidade tem seu próprio modelo de DTO (objeto de transformação de dados), geralmente contendo palavra `model` no namespace [Modelos](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models).

O código de criação de entidade cria uma entidade de machine learning com as subentidades e os recursos aplicados às subentidades `Quantity`.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Captura de tela parcial do portal mostrando a entidade criada, uma entidade de machine learning com as subentidades e os recursos aplicados às subentidades `Quantity`.":::

[!code-csharp[Create entities for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddEntities)]

Use o método a seguir para que a classe localize a ID da subentidade Quantity, a fim de atribuir os recursos a essa subentidade.

[!code-csharp[Find subentity id](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Adicionar exemplo de enunciado para intenção

A fim de determinar a intenção de um enunciado e extrair entidades, o aplicativo precisa de exemplos de enunciados. Os exemplos precisam ter como destino uma única intenção específica e devem marcar todas as entidades personalizadas. As entidades predefinidas não precisam ser marcadas.

Adicione exemplos de enunciado ao criar uma lista de objetos [ExampleLabelObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject), um objeto para cada exemplo de enunciado. Cada exemplo deve marcar todas as entidades com um dicionário de pares de nome/valor do nome da entidade e do valor da entidade. O valor da entidade deve exatamente o mesmo que aparece no texto do exemplo de enunciado.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Captura de tela parcial mostrando o exemplo de enunciado rotulado no portal. ":::

Chame [Examples.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions) com a ID do aplicativo, a ID da versão e o exemplo.

[!code-csharp[Add example utterance to intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Treinar o aplicativo

Depois que o modelo for criado, o aplicativo LUIS precisará ser treinado para esta versão do modelo. Um modelo treinado pode ser usado em um [contêiner](../luis-container-howto.md) ou [publicado](../luis-how-to-publish-app.md) nos slots de preparo ou de produto.

O método [Train.TrainVersionAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions) precisa da ID do aplicativo e da ID da versão.

Um modelo muito pequeno, como este guia de início rápido mostra, será treinado muito rapidamente. Para aplicativos de nível de produção, o treinamento do aplicativo deve incluir uma chamada de sondagem para o método [GetStatusAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) para determinar quando ou se o treinamento foi bem-sucedido. A resposta é uma lista de objetos [ModelTrainingInfo](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo) com um status separado para cada objeto. Todos os objetos devem ter êxito para que o treinamento seja considerado concluído.

[!code-csharp[Train the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Publicar aplicativo no slot de produção

Publique o aplicativo do LUIS usando o método [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync). Isso publica a versão treinada atual no slot especificado no ponto de extremidade. Seu aplicativo cliente usa esse ponto de extremidade para enviar enunciados de usuário para previsão de extração de intenção e entidade.

[!code-csharp[Publish app to production slot](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Autenticar o cliente de runtime de previsão

Use um objeto [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials) com a sua chave e use-o com o ponto de extremidade para criar um objeto [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient).

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-csharp[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PredictionCreateClient)]


## <a name="get-prediction-from-runtime"></a>Obter uma previsão do runtime

Adicione o código a seguir para criar a solicitação para o runtime de previsão.

O enunciado do usuário faz parte do objeto [PredictionRequest](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest).

O método **GetSlotPredictionAsync** precisa de vários parâmetros, como a ID do aplicativo, o nome do slot e o objeto de solicitação de previsão, para atender à solicitação. As outras opções, como detalhada, mostram todas as intenções e log são opcionais.

[!code-csharp[Get prediction from runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `dotnet run` do seu próprio diretório de aplicativo.

```dotnetcli
dotnet run
```
