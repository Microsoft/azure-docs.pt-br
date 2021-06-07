---
title: Assistentes de voz – serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, funcionalidades e restrições para assistentes de voz usando o kit de desenvolvimento de software de fala (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: fc85eec008ef099d63d538e4871a1a84573f5a18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790438"
---
# <a name="what-is-a-voice-assistant"></a>O que é um assistente de voz?

Os assistentes de voz que usam o serviço de fala permitem que os desenvolvedores criem interfaces de conversação naturais e humanas para seus aplicativos e experiências.

O serviço assistente de voz fornece uma interação rápida e confiável entre um dispositivo e uma implementação do assistente que usa (1) a [fala de linha direta](direct-line-speech.md) (por meio do serviço de bot do Azure) para adicionar recursos de voz aos bots ou, (2) comandos personalizados para cenários de comando de voz.

## <a name="choosing-an-assistant-solution"></a>Escolhendo uma solução de assistente

A primeira etapa para criar um assistente de voz é decidir o que ele deve fazer. O serviço de fala fornece várias soluções complementares para a criação de interações com o assistente. Você pode adicionar recursos de voz e saída de voz ao seu bot flexível e versátil criado usando o serviço de bot do Azure com o canal de [fala de linha direta](direct-line-speech.md) ou aproveitar a simplicidade de criar um aplicativo de [comandos personalizados](custom-commands.md) para cenários de comando de voz simples.

| Se você quiser... | Em seguida, considere... | Por exemplo... |
|-------------------|------------------|----------------|
|Conversa aberta com integração de habilidades robusta e controle de implantação completo | Bot do serviço de bot do Azure com canal de [fala de linha direta](direct-line-speech.md) | <ul><li>"Preciso ir para Seattle"</li><li>"Que tipo de pizza posso encomendar?"</li></ul>
|Comandos de voz ou conversas simples orientadas a tarefas com criação e hospedagem simplificadas | [Comandos personalizados](custom-commands.md) | <ul><li>"Ativar a luz de sobrecarga"</li><li>"Torná-lo 5 graus mais quente"</li><li>Outros exemplos [disponíveis aqui](https://speech.microsoft.com/customcommands)</li></ul>

É recomendável [direcionar a linha de fala](direct-line-speech.md) como a melhor opção padrão se você ainda não tiver certeza do que deseja que seu assistente manipule. Ele oferece integração com um conjunto avançado de ferramentas e auxílios de criação, como a [solução de assistente virtual e modelo empresarial](/azure/bot-service/bot-builder-enterprise-template-overview) e o [serviço de QnA Maker](../qnamaker/overview/overview.md) para criar padrões comuns e usar suas fontes de conhecimento existentes.

Os [comandos personalizados](custom-commands.md) facilitam a criação de aplicativos avançados de comando de voz otimizados para experiências de interação de voz primeiro. Ele fornece uma experiência de criação unificada, um modelo de hospedagem automático e complexidade relativamente menor, ajudando você a se concentrar na criação da melhor solução para seus cenários de comando de voz.

   ![Comparação de soluções do assistente](media/voice-assistants/assistant-solution-comparison.png "Comparação de soluções do assistente")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Arquitetura de referência para criar um assistente de voz usando o SDK de fala

   ![Diagrama conceitual do fluxo do serviço de orquestração do assistente de voz](media/voice-assistants/overview.png "O fluxo do assistente de voz")

## <a name="core-features"></a>Principais recursos

Independentemente de você escolher os comandos de [fala de linha direta](direct-line-speech.md) ou [personalizados](custom-commands.md) para criar suas interações com o assistente, você pode usar um conjunto avançado de recursos de personalização para personalizar seu assistente para sua marca, produto e personalidade.

| Categoria | Recursos |
|----------|----------|
|[Palavra-chave personalizada](./custom-keyword-basics.md) | Os usuários podem iniciar conversas com assistentes com uma palavra-chave personalizada como "Ei contoso". Um aplicativo faz isso com um mecanismo de palavra-chave personalizado no SDK de fala, que pode ser configurado com uma palavra-chave personalizada [que você pode gerar aqui](./custom-keyword-basics.md). Os assistentes de voz podem usar a verificação de palavra-chave do lado do serviço para melhorar a precisão da ativação da palavra-chave (versus o dispositivo sozinho).
|[Conversão de fala em texto](speech-to-text.md) | Os assistentes de voz convertem áudio em tempo real em texto reconhecido usando a [conversão de fala em texto](speech-to-text.md) do serviço de fala. Esse texto está disponível, como é transcrita, tanto para a implementação do assistente quanto para o aplicativo cliente.
|[Conversão de texto em fala](text-to-speech.md) | As respostas textuais do assistente são sintetizadas usando a [conversão de texto em fala](text-to-speech.md) do serviço de fala. Essa síntese é disponibilizada para o aplicativo cliente como um fluxo de áudio. A Microsoft oferece a capacidade de criar sua própria voz TTS multifuncional personalizada de alta qualidade que dá uma voz à sua marca. Para saber mais, [entre em contato conosco](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Introdução aos assistentes de voz

Oferecemos guias de início rápido projetados para que você execute códigos em menos de 10 minutos. Esta tabela inclui uma lista de guias de início rápido do assistente de voz, organizadas por idioma.

* [Início rápido: criar um assistente de voz personalizado usando a Direct line Speech](quickstarts/voice-assistants.md)
* [Início rápido: criar um aplicativo de comando de voz usando comandos personalizados](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>Exemplos de código e tutoriais

O código de exemplo para a criação de um assistente de voz está disponível no GitHub. Esses exemplos abrangem o aplicativo cliente para se conectar ao assistente em várias linguagens de programação populares.

* [Exemplos do assistente de voz no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [Tutorial: Ativar voz para seu assistente criado usando o serviço de bot do Azure com o SDK de fala do C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Tutorial: criar um aplicativo de comandos personalizados com comandos de voz simples](./how-to-develop-custom-commands-application.md)

## <a name="customization"></a>Personalização

Os assistentes de voz criados usando os serviços de fala do Azure podem usar a gama completa de opções de personalização.

* [Fala Personalizada](./custom-speech-overview.md)
* [Voz personalizada](how-to-custom-voice.md)
* [Palavra-chave personalizada](custom-keyword-overview.md)

> [!NOTE]
> As opções de personalização variam por idioma/localidade (consulte [idiomas com suporte](language-support.md)).

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
* [Saiba mais sobre comandos personalizados](custom-commands.md)
* [Saiba mais sobre a Direct line Speech](direct-line-speech.md)
* [Obter o SDK de Fala](speech-sdk.md)