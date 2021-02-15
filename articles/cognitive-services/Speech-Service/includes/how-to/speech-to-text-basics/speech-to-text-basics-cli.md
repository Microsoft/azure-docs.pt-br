---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 905eacc3751b3d5d6c66a2fdb0e1391a747ab895
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327000"
---
Um dos principais recursos do serviço de Fala é a capacidade de reconhecer e transcrever a fala humana (frequentemente denominada conversão de fala em texto). Neste guia de início rápido, você aprende a usar a CLI de Fala em seus aplicativos e produtos para executar uma conversão de fala em texto de alta qualidade.

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Conversão de fala em texto de um microfone

Conecte e ligue o microfone do seu PC e desative todos os aplicativos que também possam usar o microfone. Alguns computadores têm um microfone interno, enquanto outros exigem a configuração de um dispositivo Bluetooth.

Agora você está pronto para executar a CLI de Fala para reconhecer a fala do microfone. Na linha de comando, altere para o diretório que contém o arquivo binário da CLI de Fala e execute o comando a seguir.

```bash
spx recognize --microphone
```

> [!NOTE]
> A CLI de Fala usa o inglês como padrão. Você pode escolher um idioma diferente [na tabela da Conversão de fala em texto](../../../../language-support.md).
> Por exemplo, adicione `--source de-DE` para reconhecer a fala em alemão.

Fale próximo ao microfone e você verá a transcrição de suas palavras em texto em tempo real. A CLI de Fala será interrompida após um período de silêncio ou quando você pressionar CTRL-C.

## <a name="speech-to-text-from-audio-file"></a>Conversão de fala em texto de um arquivo de áudio

A CLI de Fala pode reconhecer a fala em muitos formatos de arquivo e idiomas naturais. Neste exemplo, você pode usar um arquivo WAV (16kHz ou 8kHz, de 16 bits e PCM mono) que contenha fala em inglês. Ou, se quiser um exemplo rápido, baixe o arquivo <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> e copie-o para o mesmo diretório que o arquivo binário da CLI de Fala.

Você está pronto para executar a CLI de Fala para reconhecer a fala encontrada no arquivo de áudio executando o comando a seguir.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> A CLI de Fala usa o inglês como padrão. Você pode escolher um idioma diferente [na tabela da Conversão de fala em texto](../../../../language-support.md).
> Por exemplo, adicione `--source de-DE` para reconhecer a fala em alemão.

A CLI de Fala mostrará uma transcrição do texto da fala na tela.