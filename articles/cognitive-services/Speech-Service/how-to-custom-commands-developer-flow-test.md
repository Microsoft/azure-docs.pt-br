---
title: Teste seu aplicativo de comandos personalizados
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprende diferentes abordagens para testar um aplicativo de comandos personalizados.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0bd21f55fee4d8487826deae23093ede293c8c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021806"
---
# <a name="test-your-custom-commands-application"></a>Testar seu aplicativo de comandos personalizados

Neste artigo, você aprende diferentes abordagens para testar um aplicativo de comandos personalizados.

## <a name="test-in-the-portal"></a>Testar no portal

O teste no portal é a maneira mais simples e rápida de verificar se o aplicativo de comando personalizado funciona conforme o esperado. Depois que o aplicativo for treinado com êxito, clique `Test` no botão para iniciar o teste.

> [!div class="mx-imgBorder"]
> ![Testar no portal](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Testar com o cliente do assistente do Windows Voice

O cliente do assistente de voz do Windows é um aplicativo Windows Presentation Foundation (WPF) em C# que torna mais fácil testar as interações com o bot antes de criar um aplicativo cliente personalizado.

A ferramenta pode aceitar uma ID de aplicativo de comando Personalizada. Ele permite que você teste a conclusão da tarefa ou o cenário de comando e controle hospedado no serviço de comandos personalizados.

Para configurar o cliente, faça checkout do [cliente do assistente do Windows Voice](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf).

> [!div class="mx-imgBorder"]
> ![WVAC criar perfil](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Testar com fala aplicativos cliente habilitados para SDK 
O SDK (Software Development Kit de fala) expõe muitos dos recursos do serviço de fala, o que permite desenvolver aplicativos habilitados para fala. Ele também está disponível em muitas linguagens de programação e em todas as plataformas.

Para configurar um aplicativo cliente UWP (Plataforma Universal do Windows) com o SDK de fala e integrá-lo ao seu aplicativo de comando personalizado:  
- [Como: integrar com um aplicativo cliente usando o SDK de fala](./how-to-custom-commands-setup-speech-sdk.md)
- [Como: enviar atividade para o aplicativo cliente](./how-to-custom-commands-send-activity-to-client.md)
- [Como configurar pontos de extremidade da Web](./how-to-custom-commands-setup-web-endpoints.md)

Para outras linguagens de programação e plataformas:
- [Linguagens de programação SDK de fala, plataformas, capacidades de cenário](./speech-sdk.md)
- [Códigos de exemplo do assistente de voz](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Consulte os exemplos no GitHub](https://aka.ms/speech/cc-samples)