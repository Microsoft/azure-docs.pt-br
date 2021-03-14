---
title: Visão geral de voz neural personalizada-serviço de fala
titleSuffix: Azure Cognitive Services
description: A voz neural personalizada é um recurso de conversão de texto em fala que permite que você crie uma voz sintética personalizada única para seus aplicativos, fornecendo seus próprios dados de áudio como um exemplo.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 36885e4673b83d1db7972f03c4a6309f766206c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713092"
---
# <a name="what-is-custom-neural-voice"></a>O que é voz neural personalizada?

A voz neural personalizada é um recurso de [conversão de texto em fala](./text-to-speech.md) (TTS) que permite criar uma voz sintética personalizada única para seus aplicativos, fornecendo seus próprios dados de áudio como um exemplo. A conversão de texto em fala converte o texto em fala sintética usando um modelo de aprendizado de máquina que parece uma voz escolhida. Com a [API REST](./rest-text-to-speech.md), você pode habilitar seus aplicativos para falar com [vozes predefinidas](./language-support.md#neural-voices) ou seus próprios modelos de [voz personalizados](./how-to-custom-voice-prepare-data.md) desenvolvidos por meio do recurso de voz neural personalizado. A voz neural personalizada é baseada na tecnologia de TTS de neural que cria uma voz de som natural que geralmente é indistinguíveis quando comparada com uma voz humana.
A voz realista e natural de som da voz neural personalizada pode representar marcas, máquinas personifys e permitir que os usuários interajam com aplicativos de forma natural.

> [!NOTE]
> O recurso de voz neural personalizada requer registro, e o acesso a ele é limitado com base nos critérios de qualificação e uso da Microsoft. Os clientes que desejam usar esse recurso são necessários para registrar seus casos de uso por meio do [formulário de ingestão](https://aka.ms/customneural).

## <a name="the-basics-of-custom-neural-voice"></a>Noções básicas de voz neural personalizada

A tecnologia TTS do neural subjacente usada para voz neural personalizada consiste em três componentes principais: analisador de texto, modelo acústico neural e vocoder neural. Para gerar uma fala sintética natural a partir do texto, o texto é a primeira entrada no analisador de texto, que fornece a saída na forma de sequência de fonema. Um fonema é uma unidade básica de som que distingue uma palavra de outra em um idioma específico. Uma sequência de fonemas define as pronúncias das palavras fornecidas no texto. 

Em seguida, a seqüência do fonema entra no modelo acústico acústica para prever os recursos acústicos que definem os sinais de fala, como o timbre, o estilo de fala, velocidade, intonations e padrões de estresse. Por fim, o vocoder neural converte os recursos acústicos em ondas audíveis para que a fala sintética seja gerada.

![Imagem de introdução para voz neural personalizada.](./media/custom-voice/cnv-intro.png)

Os modelos de voz TTS da neural são treinados usando redes neurais profundas com base nos exemplos de gravação de vozes humanas. Neste [blog](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911), descrevemos como a TTS do neural funciona com modelos de síntese de fala neural de ponta. O blog também explica como um modelo de base universal pode ser adaptado com menos de 2 horas de dados de fala (ou menos de 2.000 gravados declarações) de um palestrante de destino e aprender a falar na voz do orador de destino. Para ler sobre como um vocoder neural é treinado, consulte a [postagem no blog](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Com o recurso de personalização da voz neural personalizada, você pode adaptar o mecanismo de TTS de neural para se adequar melhor aos seus cenários de usuário. Para criar uma voz neural personalizada, use o [Speech Studio](https://speech.microsoft.com/customvoice) para carregar o áudio gravado e os scripts correspondentes, treinar o modelo e implantar a voz em um ponto de extremidade personalizado. Dependendo do caso de uso, a voz neural personalizada pode ser usada para converter texto em fala em tempo real (por exemplo, usado em um assistente virtual inteligente) ou gerar conteúdo de áudio offline (por exemplo, usado como no livro de áudio ou instruções em aplicativos de e-learning) com a entrada de texto fornecida pelo usuário. Isso é disponibilizado por meio da [API REST](./rest-text-to-speech.md), do [SDK de fala](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall)ou de um [portal da Web](https://speech.microsoft.com/audiocontentcreation).

## <a name="terms-and-definitions"></a>Termos e definições

| **Termo**      | **Definição**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Modelo de voz   | Um modelo de conversão de texto em fala que pode imitar as características de vozes exclusivas de um palestrante de destino. Um *modelo de voz* também é conhecido como uma *fonte de voz* ou *voz sintética*. Um modelo de voz é um conjunto de parâmetros em formato binário que não é legível pelo homem e não contém gravações de áudio. Não é possível fazer a engenharia reversa para derivar ou construir o áudio de uma voz humana. |
| Ator de voz  | Indivíduos ou palestrantes de destino cujas vozes são registradas e usadas para criar modelos de voz que se destinam a soar como a voz de talentos de voz.                                                                                                                                                                                                                                                   |
| TTS padrão  | O método padrão ou "tradicional" de TTS que divide a linguagem falada em trechos fonéticos para que eles possam ser remisturados e correspondidos usando programação clássica ou métodos estatísticos.                                                                                                                                                                                                    |
| TTS de neural    | A TTS do neural sintetiza a fala usando redes neurais profundas que têm "aprendido" a maneira como as fonéticas são combinadas em fala humana natural, em vez de usar a programação de procedimentos ou métodos estatísticos. Além das gravações de um talento de voz de destino, a linguagem TTS usa uma biblioteca de origem/modelo base criado com gravações de voz de muitos alto-falantes diferentes.          |
| Dados de treinamento | Um conjunto de registros de treinamento de voz neural personalizado que inclui as gravações de áudio do talento de voz e as transcrições de texto associadas.                                                                                                                                                                                                                                                               |
| Persona       | Uma pessoa descreve quem você deseja que essa voz seja. Um bom design de persona informará toda a criação de voz se estiver escolhendo um modelo de voz disponível já criado ou começando do zero por meio da conversão e gravação de um novo talento de voz.                                                                                                |
| Script        | Um script é um arquivo de texto que contém o declarações a ser falado pelo seu talento de voz. (O termo "*declarações*" abrange frases completas e frases mais curtas.)                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Uso responsável da IA

Para saber como usar a voz neural personalizada com responsabilidade, consulte a [Nota de transparência](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context). As notas de transparência da Microsoft têm a finalidade de ajudá-lo a entender como funciona nossa tecnologia ia, as escolhas que os proprietários do sistema podem fazer para influenciar o desempenho e o comportamento do sistema e a importância de pensar em todo o sistema, incluindo a tecnologia, as pessoas e o ambiente.

## <a name="next-steps"></a>Próximas etapas

* [Introdução à Voz Personalizada](how-to-custom-voice.md)
* [Criar e usar um ponto de extremidade de voz personalizado](how-to-custom-voice-create-voice.md)