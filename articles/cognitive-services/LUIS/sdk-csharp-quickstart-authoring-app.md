---
title: 'Início Rápido: Biblioteca de clientes de criação do LUIS (Reconhecimento Vocal) para .NET'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de clientes do LUIS (Reconhecimento Vocal) para .NET. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.  O Reconhecimento Vocal (LUIS) permite aplicar inteligência de aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário para prever o significado geral e extrair informações detalhadas relevantes.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: diberry
ms.openlocfilehash: 0db00fce3a4108a65d9ab7baafbe2146b93ad39e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327210"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-net"></a>Início Rápido: Biblioteca de clientes de criação do LUIS (Reconhecimento Vocal) para .NET

Introdução à biblioteca de clientes de criação do Reconhecimento Vocal (LUIS) para .NET. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.  O Reconhecimento Vocal (LUIS) permite aplicar inteligência de aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário para prever o significado geral e extrair informações detalhadas relevantes. 

Use a biblioteca de clientes de criação de LUIS (Reconhecimento Vocal) para .NET com o objetivo de:

* Criar um aplicativo
* Adicionar intenções, entidades e enunciados
* Adicionar recursos, como uma lista de frases
* Treinar e publicar o aplicativo

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Código-fonte da biblioteca](https://github.com/Azure/cognitive-services-dotnet-sdk-samples/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [Pacote de Criação (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [Exemplos de C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Pré-requisitos

* Conta do portal do LUIS (Reconhecimento Vocal) – [Crie uma gratuitamente](https://www.luis.ai)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="get-your-language-understanding-luis-authoring-key"></a>Obtenha sua chave de criação do LUIS (Reconhecimento Vocal)

Obtenha sua [chave de criação](luis-how-to-account-settings.md) e [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, denominada `COGNITIVESERVICE_AUTHORING_KEY`.

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Crie um novo aplicativo .NET Core em seu IDE ou editor preferido. 

1. Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `new` para criar um novo aplicativo do console com o nome `language-understanding-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: `Program.cs`. 

    ```console
    dotnet new console -n language-understanding-quickstart
    ```

1. Altere o diretório para a pasta do aplicativo recém-criado. 

1. É possível criar o aplicativo com:

    ```console
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


### <a name="install-the-sdk"></a>Instalar o SDK

Dentro do diretório do aplicativo, instale a biblioteca de clientes de criação do LUIS (Reconhecimento Vocal) para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring -Version 3.0.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.


## <a name="object-model"></a>Modelo de objeto

O cliente de criação do LUIS (Reconhecimento Vocal) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) que se autentica no Azure, que contém sua chave de criação.

Depois que o cliente for criado, use-o para acessar a funcionalidade, incluindo:

* Aplicativos – [Criar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [excluir](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [publicar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Exemplo de enunciados – [Adicionar por lote](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [excluir por ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet) 
* Recursos – Gerenciar [listas de frases](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) 
* Modelo – Gerenciar [intenções](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) e entidades
* Padrão – Gerenciar [padrões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Treinar – [Treinar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) o aplicativo e sondar o [status de treinamento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) – Gerenciar com clonar, exportar e excluir


## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes do LUIS (Reconhecimento Vocal) para .NET:

* [Criar um aplicativo](#create-a-luis-app)
* [Adicionar entidades](#create-entities-for-the-app)
* [Adicionar tentativas](#create-intent-for-the-app)
* [Adicionar declarações de exemplo](#add-example-utterance-to-intent)
* [Treine o aplicativo](#train-the-app)
* [Publique o aplicativo](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo **Program.cs** no IDE ou no editor de sua preferência. Substitua o código `using` existente pelas seguintes diretivas de `using`:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para gerenciar sua chave de criação extraída de uma variável de ambiente chamada `COGNITIVESERVICES_AUTHORING_KEY`. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável. Os métodos serão criados posteriormente.

1. Crie variáveis para manter a região de criação e o ponto de extremidade. A região de sua chave de criação depende de onde você está criando. As [três regiões de criação](luis-reference-regions.md) são:

    * Austrália – `australiaeast`
    * Europa – `westeurope`
    * EUA e outras regiões – `westus` (Padrão)
    
    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Crie um objeto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o ponto de extremidade para criar um objeto [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet).

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar um aplicativo LUIS

1. Crie um aplicativo LUIS para conter o modelo de NLP (processamento de idioma natural) que contém tentativas, entidades e exemplo de enunciados. 

1. Crie um [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). O nome e a cultura do idioma são propriedades obrigatórias. 

1. Chame o método [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet). A resposta é a ID do aplicativo. 
    
    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Criar intenção para o aplicativo
O objeto principal no modelo de um aplicativo do LUIS é a intenção. A intenção se alinha com um agrupamento de _intenções_ de enunciado do usuário. Um usuário pode fazer uma pergunta ou uma declaração procurando uma resposta _desejada_ específica de um bot (ou outro aplicativo cliente). Exemplos de intenções são: reservar um voo, perguntar sobre o tempo em uma cidade de destino e solicitar informações de contato para o atendimento ao cliente.   

Crie um [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) com o nome da intenção exclusiva e, em seguida, passe a ID do aplicativo, a ID da versão e o método ModelCreateObject para o método [Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet). A resposta é a ID da intenção.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Criar entidades para o aplicativo

Embora as entidades não sejam necessárias, são encontradas na maioria dos aplicativos. A entidade extrai informações do enunciado do usuário, necessárias para atender à intenção do usuário. Há vários tipos de entidades [predefinidas](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) e personalizadas, cada uma com seus próprios modelos de DTO (objeto de transformação de dados).  Entidades predefinidas comuns para adicionar ao seu aplicativo [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md), [ordinal](luis-reference-prebuilt-ordinal.md). 

Esse método **AddEntities** criou uma entidade simples `Location` com duas funções, uma entidade simples `Class`, uma entidade composta `Flight` e adiciona várias entidades predefinidas.

É importante saber que as entidades não estão marcadas com uma intenção. Em geral, elas podem se aplicar a muitas intenções. Somente os exemplos de enunciados de usuário são marcados para uma única intenção específica.

Os métodos de criação para as entidades fazem parte da classe do [Modelo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet). Cada tipo de entidade tem seu próprio modelo de DTO (objeto de transformação de dados), geralmente contendo palavra `model` no namespace [Modelos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet). 

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Adicionar exemplo de enunciado para intenção

A fim de determinar a intenção de um enunciado e extrair entidades, o aplicativo precisa de exemplos de enunciados. Os exemplos precisam ter como destino uma única intenção específica e devem marcar todas as entidades personalizadas. As entidades predefinidas não precisam ser marcadas. 

Adicione exemplos de enunciado ao criar uma lista de objetos [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet), um objeto para cada exemplo de enunciado. Cada exemplo deve marcar todas as entidades com um dicionário de pares de nome/valor do nome da entidade e do valor da entidade. O valor da entidade deve exatamente o mesmo que aparece no texto do exemplo de enunciado. 

Chame [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) com a ID do aplicativo, a ID da versão e a lista de exemplos. A chamada responde com uma lista de resultados. Você precisa verificar o resultado de cada exemplo para verificar se ele foi adicionado ao modelo com êxito. 

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]
    
Os métodos **CreateUtterance** e **CreateLabel** são métodos utilitários para ajudá-lo a criar objetos.

## <a name="train-the-app"></a>Treinar o aplicativo

Depois que o modelo for criado, o aplicativo LUIS precisará ser treinado para esta versão do modelo. Um modelo treinado pode ser usado em um [contêiner](luis-container-howto.md) ou [publicado](luis-how-to-publish-app.md) nos slots de preparo ou de produto. 

O método [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) precisa da ID do aplicativo e da ID da versão. 

Um modelo muito pequeno, como este guia de início rápido mostra, será treinado muito rapidamente. Para aplicativos de nível de produção, o treinamento do aplicativo deve incluir uma chamada de sondagem para o método [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) para determinar quando ou se o treinamento foi bem-sucedido. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) com um status separado para cada objeto. Todos os objetos devem ter êxito para que o treinamento seja considerado concluído.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Publicar um aplicativo de Reconhecimento Vocal

Publique o aplicativo do LUIS usando o método [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet). Isso publica a versão treinada atual no slot especificado no ponto de extremidade. Seu aplicativo cliente usa esse ponto de extremidade para enviar enunciados de usuário para previsão de extração de intenção e entidade.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo em seu próprio diretório com o comando `run` de dotnet.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar, exclua o aplicativo do LUIS. A exclusão do aplicativo é feita com o método [Apps.DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet). Você também pode excluir o aplicativo do [Portal do LUIS](https://www.luis.ai). 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Usar o SDK do .Net para consultar o ponto de extremidade de previsão](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [O que é a API do LUIS (Reconhecimento Vocal)?](what-is-luis.md)
* [O que há de novo?](whats-new.md)
* [Intenções](luis-concept-intent.md), [entidades](luis-concept-entity-types.md), [exemplos de enunciados](luis-concept-utterance.md) e [entidades predefinidas](luis-reference-prebuilt-entities.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs).
