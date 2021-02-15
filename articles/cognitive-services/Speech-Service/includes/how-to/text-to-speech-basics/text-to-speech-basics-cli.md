---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332400"
---
Neste início rápido, você aprende os padrões de design comuns para fazer uma síntese da conversão de texto em fala usando o SDK de Fala. Você começa fazendo a configuração e a sintetização básicas e passa para exemplos mais avançados de desenvolvimento de aplicativos personalizados, incluindo:

* Obter respostas como fluxos na memória
* Personalizar a taxa de bits e a taxa de amostragem de saída
* Enviar solicitações de sintetização usando SSML (linguagem de marcação de sintetização de voz)
* Usar vozes neurais

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Sintetizar uma fala em um alto-falante

Agora você está pronto para executar a CLI de Fala para sintetizar a fala de um texto. Na linha de comando, altere para o diretório que contém o arquivo binário da CLI de Fala. Em seguida, execute o comando a seguir.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

A CLI de Fala produzirá idioma natural em inglês por meio do alto-falante do computador.

## <a name="synthesize-speech-to-a-file"></a>Sintetizar fala em um arquivo

Execute o comando a seguir para alterar a saída do seu alto-falante para um arquivo `.wav`.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

A CLI de Fala produzirá idioma natural em inglês no arquivo de áudio `greetings.wav`.
No Windows, você pode reproduzir o arquivo de áudio digitando `start greetings.wav`.