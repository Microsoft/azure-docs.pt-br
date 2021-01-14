---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c14dfadfb7e8724c196b125e9b93c439fac249c3
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109456"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Instale o SDK de Fala para seu ambiente de desenvolvimento e crie um projeto de exemplo vazio<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Criar um aplicativo LUIS para reconhecimento de intenção

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Abra o projeto no Visual Studio

Em seguida, abra seu projeto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue o projeto e abra o `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto. Lembre-se de que você criou um método assíncrono chamado `RecognizeIntentAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Antes de inicializar um objeto `IntentRecognizer`, é preciso criar uma configuração que use a chave e o local do recurso de previsão do LUIS.

> [!IMPORTANT]
> A chave inicial e as chaves de criação não funcionarão. Você deve usar sua chave de previsão e o local que você criou anteriormente. Para obter mais informações, confira [Criar um aplicativo LUIS para reconhecimento de intenção](#create-a-luis-app-for-intent-recognition).

Insira esse código no método `RecognizeIntentAsync()`. Atualize estes valores:

* Substitua `"YourLanguageUnderstandingSubscriptionKey"` pela sua chave de previsão do LUIS.
* Substitua `"YourLanguageUnderstandingServiceRegion"` pelo seu local do LUIS. Use **identificador de região** da [região](../../../../regions.md).

>[!TIP]
> Se precisar de ajuda para encontrar esses valores, confira [Criar um aplicativo LUIS para reconhecimento de intenção](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

Esta amostra usa o método `FromSubscription()` para criar o `SpeechConfig`. Para ver uma lista completa dos métodos disponíveis, confira a [Classe SpeechConfig](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet).

O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar um `IntentRecognizer`. Esse objeto é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `RecognizeIntentAsync()`, logo abaixo da configuração de Fala.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicione um LanguageUnderstandingModel e as intenções

Você precisa associar um `LanguageUnderstandingModel` ao reconhecedor de intenção e adicionar as intenções que deseja reconhecer. Vamos usar as intenções do domínio predefinido para a automação doméstica. Insira este código na instrução using da seção anterior. Substitua `"YourLanguageUnderstandingAppId"` pela ID do aplicativo LUIS.

>[!TIP]
> Se precisar de ajuda para encontrar esse valor, confira [Criar um aplicativo LUIS para reconhecimento de intenção](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

Este exemplo usa a função `AddIntent()` para adicionar intenções individualmente. Se você quiser adicionar todas as intenções de um modelo, use `AddAllIntents(model)` e passe o modelo. 

> [!NOTE]
> O SDK de Fala é compatível somente com os pontos de extremidade do LUIS v2.0.
> Você deve modificar manualmente a URL do ponto de extremidade v3.0 encontrada no campo de consulta de exemplo a fim de usar um padrão de URL v2.0.
> Os pontos de extremidade do LUIS v2.0 sempre seguem um destes dois padrões:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

No objeto `IntentRecognizer`, chame o método `RecognizeOnceAsync()`. Esse método permite que o Serviço de Fala saiba que você está enviando uma única expressão para reconhecimento e permite parar o reconhecimento, assim que a frase é identificada.

Dentro da instrução using, adicione este código abaixo do seu modelo.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Exibir os resultados do reconhecimento (ou os erros)

Quando o Serviço de Fala retornar o resultado do reconhecimento, você poderá utilizá-lo. Para manter a simplicidade, vamos imprimir os resultados no console.

Dentro da instrução using, abaixo de `RecognizeOnceAsync()`, adicione este código:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Verificar o código

Neste momento, seu código deverá ter a seguinte aparência:

> [!NOTE]
> Adicionamos alguns comentários a esta versão.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Compilar e executar o aplicativo

Agora está tudo pronto para você compilar o aplicativo e testar o reconhecimento de fala usando o serviço de fala.

1. **Compilar o código**: na barra de menus do Visual Studio, escolha **Compilar** > **Compilar Solução**.
2. **Iniciar o aplicativo**: na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou pressione <kbd>F5</kbd>.
3. **Iniciar o reconhecimento**: ele solicitará que você fale uma frase em inglês. Sua fala é enviada ao Serviço de Fala, transcrita como texto e renderizada no console.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]