---
title: 'Tutorial: Reconhecer as intenções da fala usando o Speech SDK for C#'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você aprenderá a reconhecer intenções de fala usando o SDK de Fala para C#.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: d61141a0955f916b1d4bfeabb22454ec38415cea
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603230"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Tutorial: Reconhecer as intenções da fala usando o SDK de Fala para C#

O [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) dos Serviços Cognitivos integra-se ao [LUIS (Serviço Inteligente de Reconhecimento Vocal)](https://www.luis.ai/home) para fornecer **reconhecimento de intenção.** Uma intenção é algo que o usuário deseja fazer: agendar um voo, conferir o clima ou fazer uma chamada. O usuário pode usar os termos que mais lhe parecerem naturais. Usando aprendizado de máquina, o LUIS mapeia as solicitações do usuário para as intenções que você definiu.

> [!NOTE]
> Um aplicativo LUIS define as intenções e entidades que você deseja reconhecer. Ele é separado do aplicativo C# que usa o serviço de Fala. Neste artigo, “app” significa o aplicativo LUIS, enquanto “aplicativo” significa o código C#.

Neste tutorial, você usará o SDK de Fala para desenvolver um aplicativo de console C# que deriva intenção de declarações de usuário por meio do microfone do dispositivo. Você aprenderá a:

> [!div class="checklist"]
> * Criar um projeto do Visual Studio que faz referência ao pacote NuGet do SDK de Fala
> * Criar uma configuração de fala e obter um reconhecedor de intenção
> * Obter o modelo para seu app LUIS e adicionar as intenção que você precisa
> * Especificar o idioma do reconhecimento de fala
> * Reconhecer a fala de um arquivo
> * Usar reconhecimento contínuo, assíncrono e orientado a eventos

## <a name="prerequisites"></a>Pré-requisitos

Você precisará ter os itens a seguir antes de começar este tutorial.

* Uma conta LUIS. Você pode obter uma gratuitamente por meio do [portal do LUIS](https://www.luis.ai/home).
* Visual Studio 2017 (qualquer edição).

## <a name="luis-and-speech"></a>LUIS e fala

O LUIS integra-se aos Serviços de Fala para reconhecer intenções de fala. Você não precisa de uma assinatura dos Serviços de Fala, apenas do LUIS.

O LUIS usa dois tipos de chaves:

|Tipo de chave|Finalidade|
|--------|-------|
|criação|permite criar e modificar apps LUIS programaticamente|
|endpoint |autoriza o acesso a um app LUIS específico|

A chave do ponto de extremidade é a chave do LUIS necessária para este tutorial. Este tutorial usa o app LUIS de Automação Residencial como exemplo, que pode ser criado seguindo [Usar o app de Automação residencial predefinido](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Se já tiver criado um app LUIS próprio, você poderá usá-lo.

Ao criar um app LUIS, uma chave de início é gerada automaticamente para que você possa testar o app usando consultas de texto. Essa chave não habilita a integração com os Serviços de Fala e não funcionará com este tutorial. Será preciso criar um recurso LUIS no painel do Azure e atribui-lo ao app LUIS. Você pode usar a camada de assinatura gratuita para este tutorial.

Depois de criar o recurso LUIS no painel do Azure, faça logon no [portal do LUIS](https://www.luis.ai/home), escolha o seu aplicativo na página Meus aplicativos e alterne para a página Gerenciar do app. Por fim, clique em **Chaves e os pontos de extremidade** na barra lateral.

![Configurações de ponto de extremidade e chaves do portal do LUIS](media/sdk/luis-keys-endpoints-page.png)

Na página de configurações de Ponto de extremidade e chaves:

1. Role para baixo até a seção “Recursos e Chaves” e clique em **Atribuir recurso**.
1. Na caixa de diálogo **Atribuir uma chave ao seu app**, escolha o seguinte:

    * Escolha Microsoft como o Locatário.
    * Em “Nome da Assinatura”, escolha a assinatura do Azure que contém o recurso de LUIS que você deseja usar.
    * Em “Chave”, escolha o recurso de LUIS que você deseja usar com o app.

Em alguns instantes, a nova assinatura será exibida na tabela na parte inferior da página. Clique no ícone ao lado de uma chave para copiá-la na área de transferência. (Você pode usar qualquer uma das chaves.)

![Chaves da assinatura do app LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Criar um projeto de fala no Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Adicionar o código

Abra o arquivo `Program.cs` no projeto do Visual Studio e substitua o bloco de instruções `using` no início do arquivo pelas declarações a seguir.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

Adicione o código a seguir ao interior do método `Main()` fornecido.

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

Crie um método `RecognizeIntentAsync()` assíncrono vazio, conforme mostrado aqui.

```csharp
static async Task RecognizeIntentAsync()
{
}
```

Adicione este código ao corpo desse novo método.

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

Substitua os espaços reservados nesse método pela sua chave de assinatura do LUIS, região e ID do aplicativo, conforme mostrado a seguir.

|Placeholder|Substitua por|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|Sua chave de ponto de extremidade do LUIS. Como mencionado anteriormente, essa precisa ser uma chave obtida do painel do Azure, não uma “chave de início”. Você pode encontrá-la na página “Chaves e pontos de extremidade” do app (em “Gerenciar”) no [portal do LUIS](https://www.luis.ai/home).|
|`YourLanguageUnderstandingServiceRegion`|O identificador curto para a região na qual sua assinatura do LUIS se encontra, como `westus` para Oeste dos EUA. Consulte [Regiões](regions.md).|
|`YourLanguageUnderstandingAppId`|A ID do aplicativo de LUIS. Você pode encontrá-la na página Configurações do seu app no [portal do LUIS](https://www.luis.ai/home).|

Com essas alterações feitas, você poderá compilar (Control-Shift-B) e executar (F5) o aplicativo do tutorial. Quando solicitado, tente dizer “Apague as luzes” no microfone do seu computador. O resultado é exibido na janela do console.

As seções a seguir incluem uma discussão sobre o código.


## <a name="create-an-intent-recognizer"></a>Criar um reconhecedor de intenção

A primeira etapa para reconhecer intenções na fala é criar uma configuração de fala da sua chave de ponto de extremidade e região do LUIS. As configurações de fala podem ser usadas para criar os reconhecedores para os vários recursos do SDK de Fala. A configuração de fala tem várias maneiras de especificar a assinatura que você deseja usar. Aqui, usaremos `FromSubscription`, que usa a chave de assinatura e a região.

> [!NOTE]
> Use a chave e a região de sua assinatura do LUIS, não de uma assinatura dos Serviços de Fala.

Em seguida, crie um reconhecedor de intenção usando `new IntentRecognizer(config)`. Depois que a configuração souber qual assinatura usar, não será necessário especificar a chave de assinatura e o ponto de extremidade novamente ao criar o reconhecedor.

## <a name="import-a-luis-model-and-add-intents"></a>Importar um modelo LUIS e adicionar intenções

Agora, importe o modelo do app LUIS usando `LanguageUnderstandingModel.FromAppId()` e adicione as intenções de LUIS que você deseja reconhecer por meio do método `AddIntent()` do reconhecedor. Essas duas etapas melhoram a precisão do reconhecimento de fala, indicando as palavras que o usuário provavelmente usará nas solicitações. Não é necessário adicionar todas as intenções do app se você não precisar reconhecer todas elas no seu aplicativo.

Adicionar intenções requer três argumentos: o modelo de LUIS (que foi criado e chama-se `model`), o nome da intenção e uma ID de intenção. A diferença entre a ID e o nome é a seguinte.

|Argumento `AddIntent()`|Finalidade|
|--------|-------|
|intentName |O nome da intenção conforme definido no app LUIS. Precisa corresponder exatamente ao nome de intenção de LUIS.|
|intentID    |Uma ID atribuída a uma intenção reconhecida pelo SDK de Fala. Ela pode ser o que você desejar, e não precisa corresponder ao nome da intenção definido no app LUIS. Se várias intenções forem tratadas pelo mesmo código, por exemplo, seria possível usar a mesma ID para elas.|

O aplicativo LUIS Home Automation tem duas intenções: uma para ligar um dispositivo e outra para desligá-lo. As linhas a seguir adicionam essas intenções ao reconhecedor. Substitua as três linhas `AddIntent` no método `RecognizeIntentAsync()` com esse código.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Em vez de adicionar as intenções individuais, você também pode usar o método `AddAllIntents` para adicionar todas as intenções em um modelo ao reconhecedor.

## <a name="start-recognition"></a>Iniciar reconhecimento

Com o reconhecedor criado e as intenções adicionadas, o reconhecimento pode começar. O SDK de Fala é compatível com reconhecimento pontual e contínuo.

|Modo de reconhecimento|Métodos de chamada|Result|
|----------------|-----------------|---------|
|Pontual|`RecognizeOnceAsync()`|Retorna a intenção reconhecida, se houver, após uma declaração.|
|Contínuo|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Reconhece várias declarações. Emite eventos (por exemplo, `IntermediateResultReceived`) quando os resultados estão disponíveis.|

O aplicativo do tutorial usa o modo pontual, por isso, ele chama `RecognizeOnceAsync()` para iniciar o reconhecimento. O resultado é um objeto `IntentRecognitionResult` que contém informações sobre a intenção reconhecida. A resposta JSON do LUIS é extraída pela seguinte expressão:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

O aplicativo do tutorial não analisa o resultado JSON, apenas o exibe na janela do console.

![Resultados de reconhecimento do LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Especificar idioma de reconhecimento

Por padrão, o LUIS reconhece as intenções em inglês dos EUA (`en-us`). É possível reconhecer intenções em outros idiomas atribuindo um código de localidade à propriedade `SpeechRecognitionLanguage` da configuração de fala. Por exemplo, adicione `config.SpeechRecognitionLanguage = "de-de";` em nosso aplicativo de tutorial antes de criar o reconhecedor a reconhecer intenções em alemão. Confira os [Idiomas compatíveis](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Reconhecimento contínuo de um arquivo

O código a seguir ilustra duas outras funcionalidades do reconhecimento de intenção usando o SDK de Fala. A primeira, mencionada anteriormente, é o reconhecimento contínuo, em que o reconhecedor emite eventos quando os resultados estão disponíveis. Esses eventos podem ser processados por manipuladores de eventos que você fornecer. Com o reconhecimento contínuo, o `StartContinuousRecognitionAsync()` do reconhecedor é chamado para iniciar o reconhecimento, em vez de `RecognizeOnceAsync()`.

A outra funcionalidade é a leitura do áudio que contém a fala, processado de um arquivo WAV. Isso envolve a criação de uma configuração de áudio que pode ser usada ao criar o reconhecedor de intenção. O arquivo precisa ser mono (canal único) com uma taxa de amostragem de 16 kHz.

Para experimentar esses recursos, substitua o corpo do método `RecognizeIntentAsync()` pelo código a seguir.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Revise o código para incluir sua chave de ponto de extremidade do LUIS, a região e a ID do aplicativo e adicione as intenção à Automação Residencial da mesma forma que antes. Altere `whatstheweatherlike.wav` para o nome de seu arquivo de áudio. Em seguida, compile e execute.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure o código usado neste artigo na pasta samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como reconhecer a fala](how-to-recognize-speech-csharp.md)
