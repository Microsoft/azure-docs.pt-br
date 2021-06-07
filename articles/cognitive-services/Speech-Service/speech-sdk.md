---
title: Sobre o SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: O SDK (Software Development Kit de fala) expõe muitos dos recursos do serviço de fala, facilitando o desenvolvimento de aplicativos habilitados para fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 65e55d8e9d81f4045dde29c943f034ed0a24bf4c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608078"
---
# <a name="about-the-speech-sdk"></a>Sobre o SDK de Fala

O SDK (Software Development Kit de fala) expõe muitos dos recursos do serviço de fala, para capacitar o desenvolvimento de aplicativos habilitados para fala. O SDK de fala está disponível em muitas linguagens de programação e em todas as plataformas.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Recursos de cenário

O SDK de fala expõe muitos recursos do serviço de fala, mas não todos eles. Os recursos do SDK de fala geralmente são associados a cenários. O SDK de fala é ideal para cenários em tempo real e não em tempo real, usando dispositivos locais, arquivos, armazenamento de BLOBs do Azure e até mesmo fluxos de entrada e saída. Quando um cenário não é atingível com o SDK de fala, procure uma alternativa de API REST.

### <a name="speech-to-text"></a>Conversão de fala em texto

A [conversão de fala em texto](speech-to-text.md) (também conhecida como reconhecimento de *fala*) transcreve fluxos de áudio para o texto que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Use a conversão de fala em texto com o [LUIS (Reconhecimento vocal)](../luis/index.yml) para derivar intenções do usuário de dados de fala transcrita e agir com base em comandos de voz. Use a [tradução de fala](speech-translation.md) para traduzir a entrada de fala para um idioma diferente com uma única chamada. Para obter mais informações, consulte [noções básicas de conversão de fala em texto](./get-started-speech-to-text.md).

O **reconhecimento de fala (Sr), a lista de frases, a intenção, a tradução e os contêineres locais** estão disponíveis nas seguintes plataformas:

  - C++/Windows & Linux & macOS
  - C# (Framework & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java (JRE e Android)
  - JavaScript (Brower e NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Go (somente SR)

### <a name="text-to-speech"></a>Conversão de texto em fala

Conversão de [texto em fala](text-to-speech.md) (também conhecida como *síntese de fala*) converte o texto em fala sintetizada semelhante à humana. O texto de entrada é um literal de cadeia de caracteres ou o uso da [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). Para obter mais informações sobre vozes padrão ou neural, consulte [linguagem de conversão de texto em fala e suporte de voz](language-support.md#text-to-speech).

A **conversão de texto em fala (TTS)** está disponível nas seguintes plataformas:

  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE e Android)
  - Python
  - Swift
  - Objective-C
  - A API REST de TTS pode ser usada em todas as outras situações.

### <a name="voice-assistants"></a>Assistentes de voz

Os [assistentes de voz](voice-assistants.md) que usam o SDK de fala permitem que você crie interfaces de conversação naturais e humanas para seus aplicativos e experiências. O SDK de fala fornece uma interação rápida e confiável que inclui a conversão de fala em texto, conversão de texto em fala e dados de conversação em uma única conexão. Sua implementação pode usar o canal de fala de linha direta da estrutura de bot ou o serviço de comandos personalizados integrados para a conclusão da tarefa. Além disso, os assistentes de voz podem usar vozes personalizadas criadas no [portal de voz personalizado](https://aka.ms/customvoice) para adicionar uma experiência de saída de voz exclusiva.

O suporte ao **Assistente de voz** está disponível nas seguintes plataformas:

  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (SDK de dispositivos de fala)
  - Go

#### <a name="keyword-recognition"></a>Reconhecimento de palavra-chave

O conceito de [reconhecimento de palavra-chave](./custom-keyword-basics.md) tem suporte no SDK de fala. Reconhecimento de palavra-chave é o ato de identificar uma palavra-chave em fala, seguida de uma ação após ouvir a palavra-chave. Por exemplo, "Ei Cortana" ativaria o assistente da Cortana.

O **reconhecimento de palavra-chave** está disponível nas seguintes plataformas:

  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (SDK de dispositivos de fala)
  - A funcionalidade de reconhecimento de palavra-chave pode funcionar com qualquer tipo de microfone, o suporte a reconhecimento de palavra-chave oficial, no entanto, está limitado atualmente às matrizes de microfone encontradas no hardware do Azure Kinect DK ou no SDK dos dispositivos

### <a name="meeting-scenarios"></a>Cenários de reunião

O SDK de fala é perfeito para transcrever cenários de reunião, seja de um único dispositivo ou de uma conversa de vários dispositivos.

#### <a name="conversation-transcription"></a>Transcrição de conversa

A [transcrição de conversa](conversation-transcription.md) habilita o reconhecimento de fala em tempo real (e assíncrono), a identificação do orador e a atribuição de frase para cada palestrante (também conhecido como *diarization*). É perfeito para transcrição de reuniões presenciais com a capacidade de distinguir os locutores.

A **transcrição de conversa** está disponível nas seguintes plataformas:

  - C++/Windows & Linux
  - C# (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (SDK de dispositivos de fala)

#### <a name="multi-device-conversation"></a>Conversa em vários dispositivos

Com a [conversa com vários dispositivos](multi-device-conversation.md), conecte vários dispositivos ou clientes em uma conversa para enviar mensagens baseadas em fala ou em texto, com suporte fácil para transcrição e tradução.

A **conversa de vários dispositivos** está disponível nas seguintes plataformas:

  - C++/Windows
  - C# (Framework & .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>Cenários de agente/personalizado

O SDK de fala pode ser usado para transcrever cenários de Call Center, onde os dados de telefonia são gerados.

#### <a name="call-center-transcription"></a>Transcrição de call center

A [transcrição do Call Center](call-center-transcription.md) é um cenário comum de conversão de fala em texto para transcrever grandes volumes de dados de telefonia que podem vir de vários sistemas, como o IVR (resposta interativa de voz). Os modelos de reconhecimento de fala mais recentes do serviço de fala do Excel na transcreveção desses dados de telefonia, mesmo em casos em que os dados são difíceis de entender.

A **transcrição do Call Center** está disponível por meio do serviço de fala em lotes por meio de sua API REST e pode ser usada em qualquer situação.

### <a name="codec-compressed-audio-input"></a>Entrada de áudio compactada por codec

Várias das linguagens de programação SDK de fala dão suporte a fluxos de entrada de áudio compactados por codec. Para obter mais informações, consulte <a href="/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">usar formatos de entrada de áudio compactados </a>.

A **entrada de áudio compactada por codec** está disponível nas seguintes plataformas:

  - C++/Linux
  - /Linux C#
  - Java/Linux, Android e iOS

## <a name="rest-api"></a>API REST

Embora o SDK de fala cubra muitos recursos de recurso do serviço de fala, em alguns cenários, talvez você queira usar a API REST.

### <a name="batch-transcription"></a>Transcrição de lote

A [transcrição em lote](batch-transcription.md) permite a transcrição de fala em texto assíncrona de grandes volumes de dados. A transcrição do lote só é possível da API REST. Além de converter áudio de fala em texto, a conversão de texto em lote também permite a análise de diarization e de sentimentos.

## <a name="customization"></a>Personalização

O serviço de fala oferece excelente funcionalidade com seus modelos padrão entre conversão de fala em texto, texto em fala e tradução de fala. Às vezes, talvez você queira aumentar o desempenho da linha de base para funcionar ainda melhor com seu caso de uso exclusivo. O serviço de fala tem uma variedade de ferramentas de personalização sem código que facilitam e permitem que você crie uma vantagem competitiva com modelos personalizados com base em seus próprios dados. Esses modelos só estarão disponíveis para você e para sua organização.

### <a name="custom-speech-to-text"></a>Conversão de fala em texto personalizada

Ao usar a conversão de fala em texto para reconhecimento e transcrição em um ambiente exclusivo, você pode criar e treinar modelos acústicos, de linguagem e de pronúncia personalizados para resolver o ruído de ambiente ou vocabulário específico do setor. A criação e o gerenciamento de modelos de Fala Personalizada sem código estão disponíveis por meio do [portal de fala personalizada](https://aka.ms/customspeech). Depois que o modelo de Fala Personalizada for publicado, ele poderá ser consumido pelo SDK de fala.

### <a name="custom-text-to-speech"></a>Conversão de texto em fala personalizada

Conversão de texto em fala personalizada, também conhecida como voz personalizada, é um conjunto de ferramentas online que permitem que você crie uma voz de um tipo reconhecível para sua marca. A criação e o gerenciamento de modelos de voz personalizados sem código estão disponíveis por meio do [portal de voz personalizado](https://aka.ms/customvoice). Depois que o modelo de voz personalizado for publicado, ele poderá ser consumido pelo SDK de fala.

## <a name="get-the-speech-sdk"></a>Obter o SDK de Fala

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Navegador](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Veja como reconhecer fala em C#](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)