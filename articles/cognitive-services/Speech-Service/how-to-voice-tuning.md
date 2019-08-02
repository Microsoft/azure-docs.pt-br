---
title: Ajuste da saída de conversão de texto em fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Habilite o registro em log no SDK de fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4cf2338d76ce31f44eaf3fb235e5f8796602d819
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562749"
---
# <a name="fine-tune-text-to-speech-output"></a>Ajustar a saída da conversão de texto em fala

Os serviços de fala do Azure permitem ajustar a velocidade, a pronúncia, o volume, a densidade e a delimitação da saída de texto em fala usando a [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). O SSML é uma linguagem de marcação baseada em XML que usa marcas para informar o serviço sobre qual recurso requer ajuste. A mensagem SSML é enviada no corpo de cada solicitação para o serviço de conversão de texto em fala. Para simplificar o processo de personalização, os serviços de fala agora oferecem uma ferramenta de [ajuste de voz](https://aka.ms/voicetuning) que permite que você inspecione visualmente e ajuste as saídas de texto em fala em tempo real.

A ferramenta de ajuste de voz dá suporte às vozes [padrão](language-support.md#standard-voices), [neural](language-support.md#text-to-speech)e [personalizada](how-to-customize-voice-font.md)da Microsoft.

## <a name="get-started-with-the-voice-tuning-tool"></a>Introdução à ferramenta de ajuste de voz

Antes de começar a ajustar a saída de conversão de texto em fala com a ferramenta de ajuste de voz, você precisará concluir estas etapas:

1. Crie um [conta Microsoft gratuito](https://account.microsoft.com/account) se você ainda não tiver um.
2. Crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma. Clique em **Iniciar gratuitamente**e crie uma nova conta do Azure usando seu conta Microsoft.

3. Crie uma assinatura dos serviços de fala no portal do Azure. As instruções passo a passo sobre [como criar um recurso de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) estão disponíveis.
   >[!NOTE]
   >Quando você cria um recurso de fala no portal do Azure, as informações de localização do Azure precisam corresponder à região de voz TTS. A voz TTS de neural dá suporte a um subconjunto de locais do Azure. Para obter uma lista completa de suporte, consulte [regiões](regions.md#text-to-speech).

   >[!NOTE]
   >Você precisa ter uma chave F0 ou S0 criada no portal do Azure antes de poder usar o serviço. O ajuste de voz **não** dá suporte à [chave de avaliação gratuita de 30 dias](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Entre no portal de [ajuste de voz](https://aka.ms/voicetuning) e conecte sua assinatura dos serviços de fala. Escolha uma assinatura de serviços de fala única e, em seguida, crie um projeto.
5. Selecione **novo ajuste**. Depois, siga estas etapas:

   * Localize e selecione **todas as assinaturas**.  
   * Selecione **conectar-se a assinatura existente**.  
     ![Conecte uma assinatura](./media/custom-voice/custom-voice-connect-subscription.png)existente.
   * Insira sua chave de assinatura dos serviços de fala do Azure e selecione **Adicionar**. As chaves de assinatura estão disponíveis no portal de personalização de fala da [página de assinatura](https://go.microsoft.com/fwlink/?linkid=2090458). Você também pode obter as chaves no painel gerenciamento de recursos na [portal do Azure](https://portal.azure.com/).
   * Se você tiver mais de uma assinatura de serviços de fala que planeja usar, repita essas etapas para cada assinatura.

## <a name="customize-the-text-to-speech-output"></a>Personalizar a saída de conversão de texto em fala

Agora que você criou contas e vinculou sua assinatura, você pode começar a ajustar a saída de conversão de texto em fala.

1. Escolha uma voz.
2. Insira o texto que você deseja editar.
3. Antes de começar a fazer edições, reproduza o áudio para ter uma ideia da saída.
4. Selecione a palavra/frase que você deseja refinar e comece experimentando as diferentes funções baseadas em SSML.

>[!TIP]
> Para obter informações detalhadas sobre como ajustar o SSML e ajustar a saída de voz, consulte [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Limitações

O ajuste de voz neural é ligeiramente diferente do ajuste para vozes padrão e personalizadas.

* Intonation não tem suporte para vozes neurais.
* Os recursos de volume e pitch funcionam apenas com sentenças completas. Esses recursos não estão disponíveis no nível de palavra.
* Por taxa, algumas vozes neurais podem ser ajustadas com base em palavras, enquanto outras exigem que você selecione sentenças inteiras.

> [!TIP]
> A ferramenta de ajuste de voz fornece informações contextuais sobre recursos e ajustes.

## <a name="next-steps"></a>Próximas etapas
* [Criar um recurso de fala no Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Iniciar ajuste de voz](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md)
