---
title: Perguntas frequentes sobre o Direct fala de linha
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para as perguntas mais populares sobre assistentes virtuais de voz, primeiro usando o canal de fala de linha direta.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: bd7e1f87fea03d0aac7fd9f746b777e3b15e917e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606631"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Assistentes de voz-first virtuais Preview: Perguntas frequentes

Se você não encontrar respostas para suas perguntas neste documento, fazer check-out [outras opções de suporte](support.md).

## <a name="general"></a>Geral

**P: O que é direta fala de linha?**

**R:** O `DialogServiceConnector` do SDK de fala fornece bidirecional, comunicação assíncrona com os bots estão conectados ao canal de fala de linha direta no Bot Framework. Esse canal fornece acesso coordenado a fala em texto e texto para fala a fala dos serviços do Azure que permitem que os bots para se tornar totalmente a voz no, out experiências conversacionais de voz. Com o suporte para palavras de ativação e verificação de ativação de Word, essa solução possibilita criar assistentes de virtuais de voz, primeiro altamente personalizáveis para sua marca ou produto.

**P: Como fazer para começar?**

**R:** A melhor maneira para começar a criação de um Assistente de voz-first virtual é iniciar com [criando um bot básico do Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Em seguida, conecte seu bot para o [canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Depuração

**P: Recebo um erro 401 ao conectar-se e nada funciona. Sei que minha chave de assinatura de fala é válido. O que está acontecendo?**

**R:** Na visualização, fala de linha direta tem limitações específicas nas quais assinaturas você pode usar. Verifique se você estiver usando o **fala** recurso (Microsoft.CognitiveServicesSpeechServices, "Fala") e *não* o **serviços Cognitivos** recurso ( Microsoft.CognitiveServicesAllInOne, "Todos os serviços Cognitivos"). Somente [um subconjunto de regiões de serviços de fala](regions.md#voice-first-virtual-assistants) têm suporte atualmente para fala de linha direta.

![Corrija a assinatura de fala de linha direta](media/voice-first-virtual-assistants/faq-supported-subscription.png "exemplo de uma assinatura compatível de fala")

**P: Posso obter texto de reconhecimento de fala de linha direta, mas vejo um erro de '1011' e nada do meu bot. Por quê?**

**R:** Esse erro indica um problema de comunicação entre o bot e fala de linha direta. Certifique-se de que você já [conectados o canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [adicionou suporte de protocolo de Streaming](https://aka.ms/botframework/addstreamingprotocolsupport) ao seu bot (com o suporte de soquete da Web relacionado) e, em seguida, verifique se seu bot está respondendo às solicitações recebidas solicitações do canal.

**P: Esse código não funciona e/ou estou recebendo um erro diferente ao usar um DialogServiceConnector. O que devo fazer?**

**R:** Com base no arquivo de log fornece substancialmente mais detalhes e pode ajudar a acelerar as solicitações de suporte. Para habilitar essa funcionalidade, consulte [como usar o arquivo de log](how-to-use-logging.md).

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)
