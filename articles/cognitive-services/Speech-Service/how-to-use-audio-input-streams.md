---
title: Conceitos de fluxo de entrada de áudio do SDK de Fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos da API de fluxo de entrada de áudio do SDK de Fala.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.custom: devx-track-csharp
ms.openlocfilehash: 87fa97dafe9de4a23f5eaadfd4083cd1ca517cde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026583"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Sobre a API de fluxo de entrada de áudio do SDK de Fala

A API de **fluxo de entrada de áudio** do SDK de fala fornece uma maneira de transmitir áudio para os reconhecedores em vez de usar o microfone ou as APIs de arquivo de entrada.

As etapas a seguir são necessárias ao usar fluxos de entrada de áudio:

- Identificar o formato do fluxo de áudio. O formato deve ter suporte no SDK de Fala e no serviço de Fala. Atualmente, apenas a configuração a seguir tem suporte:

  As amostras de áudio estão no formato PCM, um canal, 16 bits por amostra, 8000 ou 16000 amostras por segundo (16000 ou 32000 bytes por segundo), dois alinhamentos de bloco (16 bits, incluindo o preenchimento de um exemplo).

  O código correspondente no SDK para criar o formato de áudio tem esta aparência:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000; // or 8000
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Verifique se seu código fornece os dados de áudio BRUTOs de acordo com essas especificações. Além disso, garanta que amostras de 16 bits cheguem no formato little-endian. Também há suporte para amostras assinadas. Se os dados de origem do áudio não corresponderem aos formatos com suporte, o áudio deverá ser transcodificado no formato exigido.

- Crie sua própria classe de fluxo de entrada de áudio derivada de `PullAudioInputStreamCallback`. Implemente os membros `Read()` e `Close()`. A assinatura de função exata é dependente de idioma, mas o código será semelhante a este exemplo de código:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Crie uma configuração de áudio com base em seu formato e fluxo de entrada de áudio. Passe sua configuração de fala normal e a configuração de entrada de áudio ao criar seu reconhecedor. Por exemplo:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/)
- [Veja como reconhecer fala em C#](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)