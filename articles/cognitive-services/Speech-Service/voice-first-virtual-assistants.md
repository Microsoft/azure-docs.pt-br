---
title: Os assistentes virtuais da primeira voz personalizados (versão prévia) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, funcionalidades e restrições para assistentes virtuais de primeira voz personalizados usando o canal Direct line Speech no bot Framework e o Software Development Kit (SDK) de serviços cognitivas.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 8a12c9e5d569092440552958554681c0b1d79590
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552548"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Sobre a visualização de voz personalizada-primeiro assistentes virtuais

Assistentes virtuais personalizados usando os Serviços de Fala do Azure capacitam os desenvolvedores a criar interfaces de conversação natural, semelhante à humana, para seus aplicativos e suas experiências. O Canal de Fala de Direct Line do Bot Framework aprimora essas funcionalidades fornecendo um ponto de entrada coordenado e orquestrado para um bot compatível que permite a interação com entrada e saída de voz com baixa latência e alta confiabilidade. Esses bots podem usar o Reconhecimento vocal da Microsoft (LUIS) para a interação do idioma natural. A Direct line Speech é acessada por dispositivos que usam o kit de desenvolvimento de software de fala (SDK).

   ![Diagrama conceitual do fluxo do serviço de orquestração de fala de linha direta](media/voice-first-virtual-assistants/overview.png "O fluxo do canal de fala")


A Direct line Speech e sua funcionalidade associada para assistentes virtuais de primeiro a voz personalizados são um suplemento ideal para a [solução de assistente virtual e Enterprise template](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Embora a Direct line Speech possa funcionar com qualquer bot compatível, esses recursos fornecem uma linha de base reutilizável para experiências de conversa de alta qualidade, bem como habilidades e modelos de suporte comuns para a introdução rápida.


## <a name="core-features"></a>Principais recursos

| Categoria | Recursos |
|----------|----------|
|[Palavra de ativação personalizada](speech-devices-sdk-create-kws.md) | Você pode permitir que os usuários iniciem conversas com bots usando uma palavra-chave personalizada como "Ei contoso". Essa tarefa é realizada com um mecanismo personalizado do Word de ativação no SDK de fala, que pode ser configurado com uma palavra de ativação personalizada [que você pode gerar aqui](speech-devices-sdk-create-kws.md). O canal de fala de linha direta inclui verificação de palavra de ativação do lado do serviço que melhora a precisão da ativação de palavra de ativação versus o dispositivo sozinho.
|[Fala para texto](speech-to-text.md) | O canal de fala de linha direta inclui a transcrição em tempo real de áudio em texto reconhecido usando a [conversão de fala em texto](speech-to-text.md) dos serviços de fala do Azure. Esse texto está disponível tanto para o bot quanto para seu aplicativo cliente quando ele é transcrita.
|[Conversão de texto em fala](text-to-speech.md) | As respostas textuais do bot serão sintetizadas usando a [conversão de texto em fala](text-to-speech.md) dos serviços de fala do Azure. Essa síntese será disponibilizada para seu aplicativo cliente como um fluxo de áudio. A Microsoft oferece a capacidade de criar sua própria voz TTS personalizada de alta qualidade do neural que dá uma voz à sua marca, para saber mais [entre em contato conosco](mailto:mstts@microsoft.com).
|[Fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Como um canal na estrutura de bot, a Direct line Speech permite uma conexão tranqüila e perfeita entre o aplicativo cliente, um bot compatível e os recursos dos serviços de fala do Azure. Para obter mais informações sobre como configurar o bot para usar o canal de fala de linha direta, consulte [sua página na documentação do bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para a criação de um assistente virtual de voz primeiro está disponível no GitHub. Esses exemplos abrangem o aplicativo cliente para se conectar ao bot em várias linguagens de programação populares.

* [Exemplos do assistente virtual de voz primeiro (SDK)](https://aka.ms/csspeech/samples)
* [Início rápido: assistentes virtuais da primeira vozC#()](quickstart-virtual-assistant-csharp-uwp.md)
* [Início rápido: primeiro os assistentes virtuais de voz (Java)](quickstart-virtual-assistant-java-jre.md)
* [Cliente de fala de linhaC#direta (, WPF)](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client)

## <a name="customization"></a>Personalização

Os assistentes virtuais da primeira voz criados com o uso dos serviços de fala do Azure podem usar a gama completa de opções de personalização disponíveis para conversão de texto em [texto](speech-to-text.md), [Text-to-Speech](text-to-speech.md)e [seleção de palavra-chave personalizada](speech-devices-sdk-create-kws.md).

> [!NOTE]
> As opções de personalização variam por idioma/localidade (consulte [idiomas com suporte](supported-languages.md)).

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](speech-sdk-reference.md)
* [Serviço de Bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
* [Obtenha o SDK de fala](speech-sdk.md)
* [Criar e implantar um bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obter a solução de assistente virtual e o modelo empresarial](https://github.com/Microsoft/AI)
