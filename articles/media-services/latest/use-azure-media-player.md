---
title: Reprodução com o Player de Mídia do Azure – Azure | Microsoft Docs
description: Este tópico oferece uma visão geral do Player de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: 6157f59d96770298d947a1cf6ca28aa343e1a0d4
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311817"
---
# <a name="azure-media-player-overview"></a>Visão geral do Player de Mídia do Azure

O Player de Mídia do Azure é um player de vídeo da Web criado para reproduzir conteúdo de mídia dos Serviços de Mídia do Microsoft Azure em uma grande variedade de navegadores e dispositivos. O Player de Mídia do Azure usa padrões do setor, como HTML5, MSE (Media Source Extensions) e EME (Encrypted Media Extensions) para fornecer uma experiência avançada de streaming adaptável. Quando esses padrões não estão disponíveis em um dispositivo ou em um navegador, o Player de Mídia do Azure usa Flash e Silverlight como tecnologias de fallback. Independentemente da tecnologia de reprodução usada, os desenvolvedores terão uma interface unificada do JavaScript para acessar APIs. Isso permite que o conteúdo fornecido pelos Serviços de Mídia do Azure seja executado em vários dispositivos e navegadores sem esforço adicional.

Os Serviços de Mídia do Microsoft Azure permitem que o conteúdo seja fornecido com os formatos de streaming DASH, Smooth Streaming e HLS para reprodução de conteúdo. O Player de Mídia do Azure leva em conta esses vários formatos e executa automaticamente o melhor link com base nos recursos da plataforma/navegador. Os Serviços de Mídia também permitem a criptografia dinâmica de ativos com criptografia PlayReady ou criptografia de envelope de 128 bits AES. O Player de Mídia do Azure permite a descriptografia do conteúdo criptografado com PlayReady e AES de 128 bits, quando configurado adequadamente. 

> [!NOTE]
> A reprodução de HTTPS é necessária para conteúdo criptografado Widevine.

[Inicie sua avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Use a página de demonstração do Player de Mídia do Azure

### <a name="start-using"></a>Comece a usar

Você pode usar a [página de demonstração do Player de Mídia do Azure](https://aka.ms/azuremediaplayer) para reproduzir amostras dos Serviços de Mídia do Azure ou seu próprio stream.  

Para reproduzir um novo vídeo, cole uma URL diferente e pressione **Atualizar**.

Para configurar várias opções de reprodução (por exemplo, tecnologia, linguagem ou criptografia), pressione **Opções Avançadas**.

![Player de Mídia do Azure](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorar o diagnóstico de um stream de vídeo

Você pode usar a página de demonstração do [Player de Mídia do Azure](https://aka.ms/azuremediaplayer) para monitorar o diagnóstico de um stream de vídeo. 

![Diagnóstico do Player de Mídia do Azure](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Configurar o Player de Mídia do Azure em seu HTML

O Player de Mídia do Azure é fácil de configurar. São necessários apenas alguns minutos para obter a reprodução básica de conteúdo de mídia de sua conta dos Serviços de Mídia. Consulte a [Documentação do Player de Mídia do Azure](https://aka.ms/ampdocs) para obter detalhes de como instalar e configurar o Player de Mídia do Azure. 

## <a name="next-steps"></a>Próximas etapas

- [Documentação do Player de Mídia do Azure](https://aka.ms/ampdocs)
- [Amostras do Player de Mídia do Azure](https://aka.ms/ampsamples)
