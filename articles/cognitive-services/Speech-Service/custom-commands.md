---
title: Comandos personalizados – serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, funcionalidades e restrições para comandos personalizados, uma solução para a criação de aplicativos de voz.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 40736764cf9cec6f652e0147eb25d83f15c5bee2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024935"
---
# <a name="what-is-custom-commands"></a>O que são comandos personalizados?

Aplicativos como [assistentes de voz](voice-assistants.md) ouvem os usuários e tomam uma ação em resposta, muitas vezes falando de volta. Eles usam a [fala para o texto](speech-to-text.md) para transcrever a fala do usuário e, em seguida, tomar medidas sobre a compreensão do idioma natural do texto. Essa ação geralmente inclui a saída falada do Assistente gerado com [conversão de texto em fala](text-to-speech.md). Os dispositivos se conectam aos assistentes com o objeto do SDK de fala `DialogServiceConnector` .

Os **comandos personalizados** facilitam a criação de aplicativos avançados de comando de voz otimizados para experiências de interação de voz primeiro. Ele fornece uma experiência de criação unificada, um modelo de hospedagem automático e complexidade relativamente menor, ajudando você a se concentrar na criação da melhor solução para seus cenários de comando de voz.

Os comandos personalizados são mais adequados para os cenários de conclusão de tarefas ou de comando e controle, particularmente correspondentes aos dispositivos de Internet das Coisas (IoT), dispositivos de ambiente e periféricos. Exemplos incluem soluções para hospedagem, varejo e indústrias de automóveis, permitindo que você crie as melhores experiências controladas por voz na sala para seus convidados, gerencie o inventário na sua loja e controle a funcionalidade no carro enquanto estiver em movimento.

> [!TIP]
> Veja nossas demonstrações de exemplo em nossa página de aterrissagem [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) .

Se você estiver interessado em criar aplicativos de conversa complexos, você será incentivado a experimentar a estrutura de bot usando a [solução de assistente virtual](/azure/bot-service/bot-builder-enterprise-template-overview). Você pode adicionar voz a qualquer bot da estrutura de bot usando a Direct line Speech.

Bons candidatos a comandos personalizados têm um vocabulário fixo com conjuntos bem definidos de variáveis. Por exemplo, as tarefas de automação doméstica, como controlar um termostato, são ideais.

   ![Exemplos de cenários de conclusão de tarefas](media/voice-assistants/task-completion-examples.png "exemplos de conclusão de tarefas")

## <a name="getting-started-with-custom-commands"></a>Introdução aos comandos personalizados

Nosso objetivo com comandos personalizados é reduzir sua carga cognitiva para aprender todas as diferentes tecnologias e concentrar-se em criar seu aplicativo de comando de voz. Primeira etapa para usar comandos personalizados para <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">criar um recurso <span class="docon docon-navigate-external x-hidden-focus"></span> de fala do Azure </a>. Você pode criar seu aplicativo de comandos personalizados no Speech Studio e publicá-lo, após o qual um aplicativo no dispositivo pode se comunicar com ele usando o SDK de fala.

#### <a name="authoring-flow-for-custom-commands"></a>Fluxo de criação para comandos personalizados
   ![Fluxo de criação para comandos personalizados](media/voice-assistants/custom-commands-flow.png "O fluxo de criação de comandos personalizados")

Siga nosso início rápido para que seu primeiro aplicativo de comandos personalizados execute código em menos de 10 minutos.

* [Criar um assistente de voz usando comandos personalizados](quickstart-custom-commands-application.md)

Depois de concluir o início rápido, Explore nossos guias de instruções para obter etapas detalhadas para criar, desenvolver, depurar, implantar e integrar um aplicativo de comandos personalizados.

## <a name="building-voice-assistants-with-custom-commands"></a>Criando assistentes de voz com comandos personalizados
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
* [Exibir nosso repositório de assistentes de voz no GitHub para obter exemplos](https://aka.ms/speech/cc-samples)
* [Vá para o Speech Studio para experimentar comandos personalizados](https://speech.microsoft.com/customcommands)
* [Obter o SDK de Fala](speech-sdk.md)