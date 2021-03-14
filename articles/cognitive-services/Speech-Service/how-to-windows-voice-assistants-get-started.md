---
title: Assistentes de voz no Windows-introdução
titleSuffix: Azure Cognitive Services
description: As etapas para começar a desenvolver um agente de voz do Windows, incluindo uma referência ao guia de início rápido do código de exemplo.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: b50b98095cdfe0e6ec19c89b57887ebc4a0f6317
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713041"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Introdução aos assistentes de voz no Windows

Este guia conduzirá você pelas etapas para começar a desenvolver um assistente de voz no Windows.

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Para começar a desenvolver um assistente de voz para Windows, você precisará certificar-se de que tem o ambiente de desenvolvimento adequado.

- **Visual Studio:** Será necessário instalar o [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Community Edition ou superior
- **Versão do Windows**: um PC com uma compilação de anel rápido do Windows Insider e a versão do Windows Insider do SDK do Windows. Este código de exemplo é verificado como funcionando no Build do Windows Insider versão 19025.vb_release_analog .191112-1600 usando SDK do Windows 19018. Qualquer compilação ou SDK acima das versões especificadas deve ser compatível.
- **Ferramentas de desenvolvimento UWP**: a carga de trabalho de desenvolvimento plataforma universal do Windows no Visual Studio. Consulte a página de [Get Set up](/windows/uwp/get-started/get-set-up) do UWP para preparar seu computador para o desenvolvimento de aplicativos UWP.
- **Um microfone funcional e saída de áudio**

## <a name="obtain-resources-from-microsoft"></a>Obter recursos da Microsoft

Alguns recursos necessários para um agente de voz completamente personalizado no Windows precisarão de recursos da Microsoft. O [exemplo de assistente de voz UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) fornece versões de exemplo desses recursos para desenvolvimento e teste iniciais, portanto, esta seção é desnecessária para o desenvolvimento inicial.

- **Modelo de palavra-chave:** A ativação por voz requer um modelo de palavra-chave da Microsoft na forma de um arquivo. bin. O arquivo. bin fornecido no exemplo do assistente de voz UWP é treinado na palavra-chave *contoso*.
- **Token de recurso de acesso limitado:** Como as APIs ConversationalAgent fornecem acesso ao áudio do microfone, elas são protegidas sob restrições de recursos de acesso limitado. Para usar um recurso de acesso limitado, será necessário obter um token de recurso de acesso limitado conectado à identidade do pacote do seu aplicativo da Microsoft.

## <a name="establish-a-dialog-service"></a>Estabelecer um serviço de diálogo

Para uma experiência completa do assistente de voz, o aplicativo precisará de um serviço de diálogo que

- Detectar uma palavra-chave em um determinado arquivo de áudio
- Ouvir a entrada do usuário e convertê-la em texto
- Fornecer o texto a um bot
- Traduza a resposta de texto do bot para uma saída de áudio

Esses são os requisitos para criar um serviço de diálogo básico usando a Direct line Speech.

- **Assinatura de serviços de fala:** Uma assinatura para serviços de fala cognitiva para conversões de fala a texto e de texto em fala. Experimente os serviços de fala gratuitamente [aqui](./overview.md#try-the-speech-service-for-free).
- **Bot do bot Framework:**  Um bot criado usando o bot Framework versão 4,2 ou superior que se inscreveu para [direcionar a fala de linha](./direct-line-speech.md) para habilitar a entrada e saída de voz. [Este guia](./tutorial-voice-enable-your-bot-speech-sdk.md) contém instruções passo a passo para fazer um "bot de eco" e assiná-lo para direcionar a fala de linha. Você também pode ir [aqui](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) para obter as etapas sobre como criar um bot personalizado e, em seguida, [seguir as mesmas etapas para](./tutorial-voice-enable-your-bot-speech-sdk.md) assiná-lo para direcionar a fala de linha, mas com o novo bot em vez do "bot de eco".

## <a name="try-out-the-sample-app"></a>Experimente o aplicativo de exemplo

Com a chave de assinatura dos serviços de fala e a ID do bot bot, você está pronto para experimentar o [exemplo de assistente de voz UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample). Siga as instruções no Leiame para executar o aplicativo e insira suas credenciais.

## <a name="create-your-own-voice-assistant-for-windows"></a>Criar seu próprio assistente de voz para Windows

Depois de receber o token de recurso de acesso limitado e o arquivo bin da Microsoft, você pode iniciar seu próprio assistente de voz no Windows.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Leia o guia de implementação do assistente de voz](windows-voice-assistants-implementation-guide.md)