---
title: Fala de linha direta-serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, funcionalidades e restrições para assistentes de voz usando a Direct line Speech com o kit de desenvolvimento de software de fala (SDK).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 501a0e954efbdbc9a0396fd94c915efaf0fde9fc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601120"
---
# <a name="what-is-direct-line-speech"></a>O que é o Direct Line Speech?

A **Direct line Speech** é uma solução robusta e de ponta a ponta para a criação de um assistente de voz flexível e extensível. Ele é equipado com a estrutura de bot e seu canal de fala de linha direta, que é otimizado para a interação de voz para voz e saída com bots.

Os [assistentes de voz](voice-assistants.md) ouvem os usuários e tomam uma ação em resposta, geralmente falando de volta. Eles usam a [fala para o texto](speech-to-text.md) para transcrever a fala do usuário e, em seguida, tomar medidas sobre a compreensão do idioma natural do texto. Essa ação geralmente inclui a saída falada do Assistente gerado com [conversão de texto em fala](text-to-speech.md).

A Direct line Speech oferece os mais altos níveis de personalização e sofisticação para assistentes de voz. Ele foi projetado para cenários de conversação que são abertos, naturais ou híbridos dos dois com conclusão de tarefa ou uso de comando e controle. Esse alto grau de flexibilidade vem com uma maior complexidade, e os cenários que têm como escopo tarefas bem definidas usando a entrada de idioma natural podem querer considerar [comandos personalizados](custom-commands.md) para uma experiência de solução simplificada.

## <a name="getting-started-with-direct-line-speech"></a>Introdução à fala de linha direta

As primeiras etapas para criar um assistente de voz usando a Direct line Speech são [obter uma chave de assinatura de fala](overview.md#try-the-speech-service-for-free), criar um novo bot associado a essa assinatura e conectar o bot ao canal de fala de linha direta.

   ![Diagrama conceitual do fluxo do serviço de orquestração de fala de linha direta](media/voice-assistants/overview-directlinespeech.png "O fluxo do canal de fala")

Para obter um guia passo a passo completo sobre como criar um assistente de voz simples usando a Direct line Speech, consulte [o tutorial para habilitar a fala de seu bot com o SDK de fala e o canal de fala de linha direta](tutorial-voice-enable-your-bot-speech-sdk.md).

Também oferecemos guias de início rápido projetados para que você esteja executando código e aprendendo as APIs rapidamente. Esta tabela inclui uma lista de guias de início rápido do assistente de voz organizadas por linguagem e plataforma.

| Guia de Início Rápido | Plataforma | Referência de API |
|------------|----------|---------------|
| C#, UWP | Windows | [Procurar](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Procurar](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Procurar](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para a criação de um assistente de voz está disponível no GitHub. Esses exemplos abrangem o aplicativo cliente para se conectar ao assistente em várias linguagens de programação populares.

* [Exemplos de assistente de voz (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [Tutorial: Ativar voz de seu assistente com o SDK de fala, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Personalização

Os assistentes de voz criados com o uso do serviço de fala podem usar a gama completa de opções de personalização [disponíveis para a](speech-to-text.md)seleção de [texto para fala](text-to-speech.md)e de [palavra-chave personalizada](./custom-keyword-basics.md).

> [!NOTE]
> As opções de personalização variam por idioma/localidade (consulte [idiomas com suporte](./language-support.md)).

A Direct line Speech e sua funcionalidade associada para assistentes de voz são um complemento ideal para a [solução de assistente virtual e Enterprise template](/azure/bot-service/bot-builder-enterprise-template-overview). Embora a Direct line Speech possa funcionar com qualquer bot compatível, esses recursos fornecem uma linha de base reutilizável para experiências de conversa de alta qualidade, bem como habilidades e modelos de suporte comuns para a introdução rápida.

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](./speech-sdk.md)
* [Serviço de Bot do Azure](/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
* [Obter o SDK de Fala](speech-sdk.md)
* [Criar e implantar um bot básico](/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obter a solução de assistente virtual e o modelo empresarial](https://github.com/Microsoft/AI)
