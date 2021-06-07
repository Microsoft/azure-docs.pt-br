---
title: 'Como reconhecer tentativas de fala usando o SDK de fala C #'
titleSuffix: Azure Cognitive Services
description: Neste guia, você aprenderá a reconhecer as intenções de fala usando o SDK de fala para C#.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 93a3adf00203e317be912e3e72de7a3f7ca666c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001075"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Como reconhecer tentativas de fala usando o SDK de fala para C #

O [SDK de Fala](speech-sdk.md) dos Serviços Cognitivos integra-se ao [LUIS (Reconhecimento vocal)](https://www.luis.ai/home) para fornecer **reconhecimento de intenção**. Uma intenção é algo que o usuário deseja fazer: agendar um voo, conferir o clima ou fazer uma chamada. O usuário pode usar os termos que mais lhe parecerem naturais. Usando o aprendizado de máquina, o LUIS mapeia as solicitações do usuário para as intenções que você definiu.

> [!NOTE]
> Um aplicativo LUIS define as intenções e entidades que você deseja reconhecer. Ele é separado do aplicativo C# que usa o serviço de Fala. Neste artigo, “app” significa o aplicativo LUIS, enquanto “aplicativo” significa o código C#.

Neste guia, você usa o SDK de fala para desenvolver um aplicativo de console em C# que deriva as intenções do declarações do usuário por meio do microfone do dispositivo. Você aprenderá a:

> [!div class="checklist"]
>
> - Criar um projeto do Visual Studio que faz referência ao pacote NuGet do SDK de Fala
> - Criar uma configuração de fala e obter um reconhecedor de intenção
> - Obter o modelo para seu app LUIS e adicionar as intenção que você precisa
> - Especificar o idioma do reconhecimento de fala
> - Reconhecer a fala de um arquivo
> - Usar reconhecimento contínuo, assíncrono e orientado a eventos

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem os seguintes itens antes de iniciar este guia:

- Uma conta LUIS. Você pode obter uma gratuitamente por meio do [portal do LUIS](https://www.luis.ai/home).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (qualquer edição).

## <a name="luis-and-speech"></a>LUIS e fala

O LUIS integra-se ao serviço de Fala para reconhecer intenções de fala. Você não precisa de uma assinatura do serviço de Fala, apenas do LUIS.

O LUIS usa três tipos de chaves:

| Tipo de chave  | Finalidade                                               |
| --------- | ----------------------------------------------------- |
| Criação | Permite criar e modificar aplicativos LUIS programaticamente |
| Inicial   | Permite testar o aplicativo LUIS usando somente texto   |
| Ponto de extremidade  | Autoriza o acesso a um aplicativo LUIS específico            |

Para este guia, você precisa do tipo de chave do ponto de extremidade. Este guia usa o aplicativo de exemplo Home Automation LUIS, que você pode criar seguindo o guia de início rápido [usar aplicativo de automação inicial predefinido](../luis/luis-get-started-create-app.md) . Se já tiver criado um aplicativo LUIS próprio, você poderá usá-lo.

Ao criar um aplicativo LUIS, o LUIS gera automaticamente uma chave de início para que você possa testar o aplicativo usando consultas de texto. Esta chave não habilita a integração do serviço de fala e não funcionará com este guia. Crie um recurso LUIS no painel do Azure e atribua-o ao aplicativo LUIS. Você pode usar a camada de assinatura gratuita para este guia.

Após criar o recurso LUIS no painel do Azure, faça logon no [portal do LUIS](https://www.luis.ai/home), escolha seu aplicativo na página **Meus Aplicativos** e alterne para a página **Gerenciar** do aplicativo. Por fim, selecione **Chaves e os pontos de extremidade** na barra lateral.

![Configurações de ponto de extremidade e chaves do portal do LUIS](media/sdk/luis-keys-endpoints-page.png)

Na página **Configurações de Chave se Ponto de Extremidade**:

1. Role para baixo até a seção **Recursos e Chaves** e selecione **Atribuir recurso**.
1. Na caixa de diálogo **Atribuir uma chave ao seu aplicativo**, faça as seguintes alterações:

   - Em **Locatário**, escolha **Microsoft**.
   - Em **Nome da Assinatura**, escolha a assinatura do Azure que contém o recurso LUIS que você deseja usar.
   - Em **Chave**, escolha o recurso LUIS que você deseja usar com o aplicativo.

   Em alguns instantes, a nova assinatura será exibida na tabela na parte inferior da página.

1. Selecione o ícone ao lado da chave para copiá-la para a área de transferência. (Você pode usar qualquer uma das chaves.)

![Chaves da assinatura do app LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Criar um projeto de fala no Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Adicionar o código

Em seguida, adicione o código ao projeto.

1. No **Gerenciador de Soluções**, abra o arquivo **Program.cs**.

1. Substitua o bloco de instruções `using` no início do arquivo pelas declarações a seguir:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Substitua o `Main()` método fornecido com o seguinte equivalente assíncrono:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Crie um método `RecognizeIntentAsync()` assíncrono vazio, conforme mostrado aqui:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. No corpo deste novo método, adicione este código:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Substitua os espaços reservados nesse método pela sua chave de assinatura do LUIS, região e ID do aplicativo, conforme mostrado a seguir.

   | Espaço reservado | Substitua por |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Sua chave de ponto de extremidade do LUIS. Novamente, você deve obter esse item do seu painel do Azure, não uma “chave de início”. Você pode encontrá-lo na página de **chaves e pontos de extremidade** do aplicativo (em **gerenciar**) no [portal do Luis](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | O identificador curto para a região na qual sua assinatura do LUIS se encontra, como `westus` para Oeste dos EUA. Consulte [Regiões](regions.md). |
   | `YourLanguageUnderstandingAppId` | A ID do aplicativo de LUIS. Você pode encontrá-la na página **Configurações** do seu aplicativo no [portal do LUIS](https://www.luis.ai/home). |

Com essas alterações feitas, você pode criar (**Control + Shift + B**) e executar (**F5**) o aplicativo. Quando solicitado, tente dizer “Apague as luzes” no microfone do seu computador. O aplicativo exibe o resultado na janela do console.

As seções a seguir incluem uma discussão sobre o código.

## <a name="create-an-intent-recognizer"></a>Criar um reconhecedor de intenção

Primeiro, você precisa criar uma configuração de fala em sua chave e região de ponto de extremidade do LUIS. Você pode usar as configurações de fala para criar reconhecedores para as várias funcionalidades do SDK de Fala. A configuração de fala tem várias maneiras de especificar a assinatura que você deseja usar. Aqui, usamos `FromSubscription`, que usa a chave de assinatura e a região.

> [!NOTE]
> Use a chave e a região da sua assinatura do LUIS, não uma assinatura do serviço de fala.

Em seguida, crie um reconhecedor de intenção usando `new IntentRecognizer(config)`. Depois que a configuração souber qual assinatura usar, você não precisará especificar a chave de assinatura e o ponto de extremidade novamente ao criar o reconhecedor.

## <a name="import-a-luis-model-and-add-intents"></a>Importar um modelo LUIS e adicionar intenções

Agora, importe o modelo do app LUIS usando `LanguageUnderstandingModel.FromAppId()` e adicione as intenções de LUIS que você deseja reconhecer por meio do método `AddIntent()` do reconhecedor. Essas duas etapas melhoram a precisão do reconhecimento de fala, indicando as palavras que o usuário provavelmente usará nas solicitações. Você não precisará adicionar todas as intenções do aplicativo se não precisar reconhecer todas elas em seu aplicativo.

Para adicionar intenções, você deve fornecer três argumentos: o modelo LUIS (que foi criado e é denominado `model`), o nome da intenção e uma ID de intenção. A diferença entre a ID e o nome é a seguinte.

| `AddIntent()`&nbsp;argument | Finalidade |
| --------------------------- | ------- |
| `intentName` | O nome da intenção conforme definido no app LUIS. Esse valor precisa corresponder exatamente ao nome de intenção de LUIS. |
| `intentID` | Uma ID atribuída a uma intenção reconhecida pelo SDK de Fala. Esse valor pode ser o que você desejar; ele não precisa corresponder ao nome da intenção definido no aplicativo LUIS. Se várias intenções forem tratadas pelo mesmo código, por exemplo, seria possível usar a mesma ID para elas. |

O aplicativo LUIS de Automação Residencial tem duas intenções: uma para ligar um dispositivo e outra para desligá-lo. As linhas a seguir adicionam essas intenções ao reconhecedor. Substitua as três linhas `AddIntent` no método `RecognizeIntentAsync()` com esse código.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Em vez de adicionar as intenções individuais, você também pode usar o método `AddAllIntents` para adicionar todas as intenções em um modelo ao reconhecedor.

## <a name="start-recognition"></a>Iniciar reconhecimento

Com o reconhecedor criado e as intenções adicionadas, o reconhecimento pode começar. O SDK de Fala é compatível com reconhecimento pontual e contínuo.

| Modo de reconhecimento | Métodos de chamada | Resultado |
| ---------------- | --------------- | ------ |
| Pontual | `RecognizeOnceAsync()` | Retorna a intenção reconhecida, se houver, após uma declaração. |
| Contínuo | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Reconhece vários enunciados; emite eventos (por exemplo, `IntermediateResultReceived`) quando os resultados estão disponíveis. |

O aplicativo usa o modo de captura única e, portanto, chamadas `RecognizeOnceAsync()` para iniciar o reconhecimento. O resultado é um objeto `IntentRecognitionResult` que contém informações sobre a intenção reconhecida. Extraia a resposta JSON do LUIS usando a seguinte expressão:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

O aplicativo não analisa o resultado JSON. Ele exibe apenas o texto JSON na janela do console.

![Resultados de reconhecimento simples do LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Especificar idioma de reconhecimento

Por padrão, o LUIS reconhece as intenções em inglês dos EUA (`en-us`). É possível reconhecer intenções em outros idiomas atribuindo um código de localidade à propriedade `SpeechRecognitionLanguage` da configuração de fala. Por exemplo, adicione `config.SpeechRecognitionLanguage = "de-de";` em nosso aplicativo antes de criar o reconhecedor para reconhecer as intenções em alemão. Para obter mais informações, consulte [suporte ao idioma Luis](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Reconhecimento contínuo de um arquivo

O código a seguir ilustra duas outras funcionalidades do reconhecimento de intenção usando o SDK de Fala. A primeira, mencionada anteriormente, é o reconhecimento contínuo, em que o reconhecedor emite eventos quando os resultados estão disponíveis. Esses eventos podem ser processados por manipuladores de eventos que você fornecer. Com o reconhecimento contínuo, você chama o método `StartContinuousRecognitionAsync()` do reconhecedor para iniciar o reconhecimento, em vez de `RecognizeOnceAsync()`.

A outra funcionalidade é a leitura do áudio que contém a fala, processado de um arquivo WAV. Essa implementação envolve a criação de uma configuração de áudio que pode ser usada ao criar o reconhecedor de intenção. O arquivo precisa ser mono (canal único) com uma taxa de amostragem de 16 kHz.

Para experimentar esses recursos, exclua ou comente o corpo do método `RecognizeIntentAsync()` e adicione o seguinte código em seu lugar.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Revise o código para incluir sua chave de ponto de extremidade do LUIS, a região e a ID do aplicativo e adicione as intenção à Automação Residencial da mesma forma que antes. Altere `whatstheweatherlike.wav` ao nome de seu arquivo de áudio gravado. Em seguida, copie o arquivo de áudio para o diretório de build e execute o aplicativo.

Por exemplo, se você disser “Apague as luzes”, pausar e depois disser “Acenda as luzes” em seu arquivo de áudio gravado, talvez seja exibida uma saída de console semelhante à seguinte:

![Resultados do reconhecimento do LUIS de arquivos de áudio](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure o código neste artigo na pasta **samples/csharp/sharedcontent/console**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Reconhecer fala de um microfone](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)