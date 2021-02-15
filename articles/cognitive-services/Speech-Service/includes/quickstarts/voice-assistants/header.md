---
title: 'Início Rápido: Criar um assistente de voz personalizado – Serviço de Fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187470"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para criar um aplicativo de assistente de voz personalizado que se conecta a um bot que você já criou e configurou. Se você precisar criar um bot, confira o [tutorial relacionado](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) para ver orientações mais abrangentes.

Depois de atender a alguns pré-requisitos, conectar o assistente de voz personalizado leva apenas algumas etapas:
> [!div class="checklist"]
> * Criar um objeto `BotFrameworkConfig` na chave de assinatura e na região.
> * Criar um objeto `DialogServiceConnector` usando o objeto `BotFrameworkConfig` acima.
> * Com o objeto `DialogServiceConnector`, inicie o processo de escuta para um único enunciado.
> * Inspecione o `ActivityReceivedEventArgs` retornado.
