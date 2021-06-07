---
title: Direct Line Speech – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, funcionalidades e restrições dos assistentes de voz que usam a Direct Line Speech com o SDK (Software Development Kit) de Fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6eb689c2e93b5a9cac932e428245410ba35e77d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611786"
---
# <a name="what-is-direct-line-speech"></a>O que é o Direct Line Speech?

O **Direct Line Speech** é uma solução robusta e completa para a criação de um assistente de voz flexível e extensível. Ele é equipado com a Bot Framework e o canal do Direct Line Speech, que é otimizado para a interação de voz de entrada e de saída com bots.

Os [assistentes de voz](voice-assistants.md) ouvem os usuários e realizam uma ação em resposta, geralmente falando de volta. Eles usam o [reconhecimento de fala](speech-to-text.md) para transcrever a fala do usuário e reagir ao reconhecimento de linguagem natural do texto. Essa ação geralmente inclui a saída falada do assistente, gerada com [conversão de texto em fala](text-to-speech.md).

O Direct Line Speech oferece os mais altos níveis de personalização e sofisticação para assistentes de voz. Ele foi criado para cenários de conversação aberta, natural ou híbrida dos dois, com realização de tarefas ou uso de comando e controle. Esse alto grau de flexibilidade torna a solução mais complexa. Por isso, para situações com tarefas de escopo bem definido que usam entrada de idioma natural, pode ser melhor usar os [Comandos Personalizados](custom-commands.md) para criar uma solução simplificada.

## <a name="getting-started-with-direct-line-speech"></a>Introdução ao Direct Line Speech

As primeiras etapas para criar um assistente de voz com o Direct Line Speech são: [obter uma chave de assinatura de fala](overview.md#try-the-speech-service-for-free), criar um bot associado à assinatura e conectar o bot ao canal do Direct Line Speech.

   ![Diagrama conceitual do fluxo do serviço de orquestração do Direct Line Speech](media/voice-assistants/overview-directlinespeech.png "Fluxo do Canal de Fala")

Para obter um guia passo a passo completo sobre como criar um assistente de voz simples usando o Direct Line Speech, confira [o tutorial para habilitar a fala de bots com o SDK de Fala e o canal do Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

Também oferecemos guias de início rápido para você começar rapidamente a executar código e conhecer as APIs. Esta tabela inclui uma lista de guias de início rápido do assistente de voz organizadas por linguagem e plataforma.

| Guia de Início Rápido | Plataforma | Referência de API |
|------------|----------|---------------|
| C#, UWP | Windows | [Procurar](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Procurar](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Procurar](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Código de exemplo

Código de exemplo para a criação de assistentes de voz está disponível no GitHub. Esses exemplos tratam do aplicativo cliente para se conectar ao assistente em várias linguagens de programação populares.

* [Exemplos de assistente de voz (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [Tutorial: habilitar a voz em assistentes com o SDK de Fala, C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Personalização

Os assistentes de voz criados com o serviço de Fala podem usar a gama completa de opções de personalização disponíveis para [conversão de fala em texto](speech-to-text.md), [conversão de texto em fala](text-to-speech.md) e [seleção de palavra-chave personalizada](./custom-keyword-basics.md).

> [!NOTE]
> As opções de personalização variam por idioma/localidade. Confira os [idiomas compatíveis](./language-support.md).

O Direct Line Speech e a funcionalidade associada para assistentes de voz são um complemento ideal para a [Solução de Assistente Virtual e Enterprise Template](/azure/bot-service/bot-builder-enterprise-template-overview). Embora o Direct Line Speech funcione com qualquer bot compatível, esses recursos oferecem uma linha de base reutilizável para experiências de conversa de alta qualidade, bem como habilidades e modelos de suporte comuns para a introdução rápida.

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](./speech-sdk.md)
* [Serviço de Bot do Azure](/azure/bot-service/)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
* [Obter o SDK de Fala](speech-sdk.md)
* [Criar e implantar um bot básico](/azure/bot-service/bot-builder-tutorial-basic-deploy)
* [Obter a Solução de Assistente Virtual e o Enterprise Template](https://github.com/Microsoft/AI)
