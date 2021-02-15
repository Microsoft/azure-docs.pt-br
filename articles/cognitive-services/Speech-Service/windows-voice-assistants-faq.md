---
title: Assistentes de voz no Windows-perguntas frequentes
titleSuffix: Azure Cognitive Services
description: Perguntas comuns que muitas vezes surgem durante o desenvolvimento do Windows Voice Agent.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84456990"
---
# <a name="samples-and-faqs"></a>Exemplos e perguntas frequentes

## <a name="the-uwp-voice-assistant-sample"></a>O exemplo de assistente de voz UWP

O exemplo de assistente de voz UWP é um assistente de voz no Windows que serve para

- demonstrar o uso das APIs ConversationalAgent do Windows
- exibir as práticas recomendadas para um agente de voz no Windows
- fornecer um aplicativo adaptável e componentes reutilizáveis para seu aplicativo de agente MVP
- mostrar como a Direct line Speech, juntamente com o bot Framework ou comandos personalizados, pode ser usada junto com as APIs ConversationalAgent do Windows para uma experiência de agente de voz de ponta a ponta

A documentação fornecida com o aplicativo de exemplo percorre o caminho de código de ativação de voz e gerenciamento de agente, dos pré-requisitos de inicialização por meio de uma limpeza apropriada.

> [!div class="nextstepaction"]
> [Visite o repositório do GitHub para o exemplo de UWP](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>Como fazer contatar a Microsoft para obter recursos como tokens de recurso de acesso limitado e arquivos de modelo de palavra-chave?

Contate winvoiceassistants@microsoft.com para solicitar esses recursos.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>Meu aplicativo é exibido em uma pequena janela quando eu o ativo por voz. Como posso fazer a transição da exibição compacta para uma janela de aplicativo completa?

Quando seu aplicativo é ativado pela primeira vez por voz, ele é iniciado em uma exibição compacta. Leia as [diretrizes de design para a visualização de ativação de voz](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) para obter orientação sobre as diferentes exibições e transições entre elas para assistentes de voz no Windows.

Para fazer a transição da exibição compacta para a exibição de aplicativo completa, use a API appView `TryEnterViewModeAsync` :

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Por que os recursos do assistente de voz no Windows são habilitados somente para aplicativos UWP?

Considerando os riscos de privacidade associados a recursos como ativação de voz, os recursos da plataforma UWP são necessários permitem que os recursos do assistente de voz no Windows sejam suficientemente seguros.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>O exemplo do assistente de voz UWP usa a Direct line Speech. É necessário usar a Direct line Speech para meu assistente de voz no Windows?

O aplicativo de exemplo UWP foi desenvolvido usando a Direct line Speech e o SDK dos serviços de fala como uma demonstração de como usar um serviço de diálogo com o recurso do agente de conversação do Windows. No entanto, você pode usar qualquer serviço para verificação de palavra-chave local e na nuvem, conversão de fala em texto, caixa de diálogo bot e conversão de texto em fala.