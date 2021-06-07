---
title: Visão geral sobre Sintetização de Voz Personalizada – serviço de Fala
titleSuffix: Azure Cognitive Services
description: A Sintetização de Voz Personalizada é um recurso de conversão de texto em fala que permite que você crie uma voz sintética personalizada única para seus aplicativos, fornecendo seus dados de áudio como amostra.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 36885e4673b83d1db7972f03c4a6309f766206c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101713092"
---
# <a name="what-is-custom-neural-voice"></a>O que é a Sintetização de Voz Personalizada?

A Sintetização de Voz Personalizada é um recurso de [TTS](./text-to-speech.md) (conversão de texto em fala) que permite criar uma voz sintética personalizada exclusiva para seus aplicativos, fornecendo seus dados de áudio como amostra. A conversão de texto em fala converte o texto em fala sintética usando um modelo de machine learning que se parece com a voz escolhida. Com a [API REST](./rest-text-to-speech.md), você pode habilitar seus aplicativos para falar com [vozes predefinidas](./language-support.md#neural-voices) ou seus modelos de [voz personalizados](./how-to-custom-voice-prepare-data.md) desenvolvidos por meio do recurso de Sintetização de Voz Personalizada. A Sintetização de Voz Personalizada é baseada na tecnologia de TTS neural que cria uma voz de som natural que geralmente é indistinguível quando comparada com uma voz humana.
A voz realista e natural da Sintetização de Voz Personalizada pode representar marcas, personificar máquinas e permitir que os usuários interajam com aplicativos, mantendo uma conversa de maneira natural.

> [!NOTE]
> O recurso da Sintetização de Voz Personalizada requer registro e o acesso a ele é limitado com base nos critérios de qualificação e uso da Microsoft. Os clientes que desejam usar esse recurso devem registrar seus casos de uso por meio do [formulário de ingestão](https://aka.ms/customneural).

## <a name="the-basics-of-custom-neural-voice"></a>Noções básicas sobre a Sintetização de Voz Personalizada

A tecnologia TTS neural subjacente usada na Sintetização de Voz Personalizada consiste em três componentes principais: analisador de texto, modelo acústico neural e vocoder neural. Para gerar uma fala sintética natural com base no texto, primeiro é necessário inserir o texto no Analisador de Texto, que fornece a saída na forma de sequência de fonema. Um fonema é uma unidade básica de som que distingue uma palavra de outra em um idioma específico. Uma sequência de fonemas define as pronúncias das palavras fornecidas no texto. 

Em seguida, a sequência do fonema vai para o Modelo Acústico Neural para prever os recursos acústicos que definem os sinais da fala, como o timbre, o estilo de fala, a velocidade, as entonações e os padrões de estresse. Por fim, o Vocoder Neural converte os recursos acústicos em ondas audíveis para que a fala sintética seja gerada.

![Imagem de introdução para a Sintetização de Voz Personalizada.](./media/custom-voice/cnv-intro.png)

Os modelos de voz TTS neurais são treinados com o uso de redes neurais profundas com base nas amostras de gravação de vozes humanas. Neste [blog](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911), descrevemos como a TTS do Neural funciona com modelos de síntese de fala neural de ponta. O blog também explica como um modelo básico universal pode ser adaptado com menos de 2 horas de dados de fala (ou menos de duas mil declarações gravadas) de um orador específico e aprender a falar na voz do orador específico. Para ler sobre como um vocoder neural é treinado, confira esta [postagem no blog](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Com a funcionalidade de personalização da Sintetização de Voz Personalizada, você pode adaptar o mecanismo de TTS Neural para se adequar melhor aos seus cenários de usuário. Para criar uma Sintetização de Voz Personalizada, use o [Speech Studio](https://speech.microsoft.com/customvoice) para carregar o áudio gravado e os scripts correspondentes, treinar o modelo e implantar a voz em um ponto de extremidade personalizado. Dependendo do caso de uso, a Sintetização de Voz Personalizada pode ser usada para converter texto em fala em tempo real (por exemplo, usada em um assistente virtual inteligente) ou para gerar conteúdo de áudio offline (por exemplo, usada em audiolivro ou instruções em aplicativos de e-learning) com a entrada de texto fornecida pelo usuário. Isso é disponibilizado por meio da [API REST](./rest-text-to-speech.md), do [SDK de Fala](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall) ou de um [portal da Web](https://speech.microsoft.com/audiocontentcreation).

## <a name="terms-and-definitions"></a>Termos e definições

| **Termo**      | **Definição**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Modelo de voz   | Um modelo de conversão de texto em fala que pode imitar as características de vozes singulares de um palestrante específico. Um *modelo de voz* também é conhecido como uma *fonte de voz* ou *voz sintética*. Um modelo de voz é um conjunto de parâmetros em formato binário que não é legível pelo ser humano e que não contém gravações de áudio. Não é possível fazer a engenharia reversa para derivar ou construir o áudio de uma voz humana. |
| Ator de voz  | Indivíduos ou palestrantes específicos cujas vozes são gravadas e usadas para criar modelos de voz que se destinam a soar como a voz do ator de voz.                                                                                                                                                                                                                                                   |
| TTS padrão  | O método padrão ou "tradicional" de TTS que divide a linguagem falada em trechos fonéticos para que eles possam ser remixados e combinados usando programação clássica ou métodos estatísticos.                                                                                                                                                                                                    |
| TTS neural    | O TTS neural sintetiza a fala usando redes neurais profundas que "aprenderam" a maneira como a fonética é combinada na fala humana natural, em vez de usar a programação de procedimentos ou métodos estatísticos. Além das gravações de um ator de voz específico, o TTS neural usa uma biblioteca de origem/modelo base criado com gravações de voz de muitos palestrantes diferentes.          |
| Dados de treinamento | Um conjunto de dados de treinamento de sintetização de voz personalizada que inclui as gravações de áudio do ator de voz e as transcrições de texto associadas.                                                                                                                                                                                                                                                               |
| Persona       | Uma persona descreve quem você deseja que essa voz seja. Um bom design de persona informará toda a criação de voz se está escolhendo um modelo de voz disponível já criado ou começando do zero por meio da conversão e gravação de um novo ator de voz.                                                                                                |
| Script        | Um script é um arquivo de texto que contém as declarações a serem faladas pelo ator de voz. (O termo "*declarações*" engloba sentenças completas e frases curtas).                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Uso responsável da IA

Para saber como usar a Sintetização de Voz Personalizada com responsabilidade, confira a [nota de transparência](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context). As notas de transparência da Microsoft têm a finalidade de ajudar você a entender como funciona nossa tecnologia de IA, as escolhas que os proprietários do sistema podem fazer para influenciar o desempenho e o comportamento do sistema e a importância de pensar em todo o sistema, incluindo a tecnologia, as pessoas e o ambiente.

## <a name="next-steps"></a>Próximas etapas

* [Introdução à Voz Personalizada](how-to-custom-voice.md)
* [Criar e usar um ponto de extremidade de Voz Personalizada](how-to-custom-voice-create-voice.md)