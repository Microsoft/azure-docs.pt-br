---
title: O que são os Serviços de Comunicação do Azure?
description: Saiba como os Serviços de Comunicação do Azure ajudam você a desenvolver experiências de usuário avançadas com comunicações em tempo real.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0efdf48e78d0cc48e288bea354f5de5f9635c760
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106833"
---
# <a name="what-is-azure-communication-services"></a>O que são os Serviços de Comunicação do Azure?

> [!IMPORTANT]
> Os aplicativos que você cria usando os Serviços de Comunicação do Azure podem se comunicar com o Microsoft Teams. Para saber mais, visite nossa documentação de [Interoperabilidade do Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md).


Os Serviços de Comunicação do Azure permitem que você adicione facilmente recursos de comunicação multimídia com voz, vídeo e telefonia por IP aos seus aplicativos. As bibliotecas de SDK dos Serviços de Comunicação também permitem que você adicione funcionalidade de chat e SMS às suas soluções de comunicação.

<br>

> [!VIDEO https://www.youtube.com/embed/apBX7ASurgM]

<br>
<br>

Você pode usar os Serviços de Comunicação para comunicação de voz, vídeo, texto e dados em diversos cenários:

- Comunicação do navegador para navegador, navegador para aplicativo e aplicativo para aplicativo
- Usuários interagindo com bots ou outros serviços
- Usuários e bots interagindo pela rede de telefonia comutada pública

Cenários mistos têm suporte. Por exemplo, um aplicativo dos Serviços de Comunicação pode ter usuários falando de navegadores e de dispositivos de telefonia tradicionais ao mesmo tempo. Os Serviços de Comunicação também podem ser combinados com o Serviço de Bot do Azure para criar sistemas de IVR (resposta de voz interativa) controlados por bot.

## <a name="common-scenarios"></a>Cenários comuns

Os recursos a seguir são um ótimo ponto de partida para começar a usar os Serviços de Comunicação do Azure.
<br>

| Recurso                               |Descrição                           |
|---                                    |---                                   |
|**[Criar um recurso dos Serviços de Comunicação](./quickstarts/create-communication-resource.md)**|Você pode começar a usar os Serviços de Comunicação do Azure usando o portal do Azure ou o SDK dos Serviços de Comunicação para provisionar seu primeiro recurso dos Serviços de Comunicação. Quando tiver sua cadeia de conexão de recurso dos Serviços de Comunicação, você poderá provisionar seus primeiros tokens de acesso do usuário.|
|**[Obter um número de telefone](./quickstarts/telephony-sms/get-phone-number.md)**|Você pode usar os Serviços de Comunicação do Azure para provisionar e liberar números de telefone. Esses números de telefone podem ser usados para iniciar chamadas de saída e criar soluções de comunicação por SMS.|

Depois de criar um recurso dos Serviços de Comunicação, você pode começar a criar cenários de cliente, como chamada de voz e vídeo ou bate-papo com texto.

| Recurso                               |Descrição                           |
|---                                    |---                                   |
|**[Criar seu primeiro token de acesso do usuário](./quickstarts/access-tokens.md)**|Os tokens de acesso do usuário são usados para autenticar seus serviços em relação ao seu recurso dos Serviços de Comunicação do Azure. Esses tokens são provisionados e reemitidos usando o SDK dos Serviços de Comunicação.|
|**[Introdução às chamadas de voz e vídeo](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Os Serviços de Comunicação do Azure permitem que você adicione chamadas de voz e de vídeo aos seus aplicativos usando o SDK de Chamada. Essa biblioteca usa a plataforma WebRTC e permite que você estabeleça comunicações de ponto a ponto, multimídia e em tempo real em seus aplicativos.|
|**[Ingressar seu aplicativo de chamada em uma reunião do Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Os Serviços de Comunicação do Azure podem ser usados para criar experiências de reunião personalizadas que interagem com o Microsoft Teams. Os usuários das suas soluções dos Serviços de Comunicação podem interagir com os participantes do Teams usando voz, vídeo, chat e compartilhamento de tela.|
|**[Introdução ao chat](./quickstarts/chat/get-started.md)**|O SDK de Chat dos Serviços de Comunicação do Azure pode ser usado para integrar chat em tempo real aos seus aplicativos.|
|**[Enviar um SMS de seu aplicativo](./quickstarts/telephony-sms/send.md)**|O SDK de SMS dos Serviços de Comunicação do Azure permite que você envie e receba mensagens SMS de seus aplicativos .NET e JavaScript.|

## <a name="samples"></a>Exemplos

Os exemplos a seguir demonstram a utilização de ponta a ponta do SDK dos Serviços de Comunicação do Azure. Sinta-se à vontade para usar esses exemplos para inicializar suas próprias soluções dos Serviços de Comunicação.
<br>

| Nome da amostra                               | Descrição                           |
|---                                    |---                                   |
|**[Exemplo de destaque de chamada de grupo](./samples/calling-hero-sample.md)**|Veja como as bibliotecas de SDK dos Serviços de Comunicação podem ser usadas para criar uma experiência de chamada em grupo.|
|**[Exemplo de destaque de chat de grupo](./samples/chat-hero-sample.md)**|Veja como as bibliotecas do SDK dos Serviços de Comunicação podem ser usadas para criar uma experiência de chat em grupo.|


## <a name="platforms-and-sdk-libraries"></a>Plataformas e bibliotecas de SDK

Os seguintes recursos ajudarão você a aprender sobre as bibliotecas de SDK dos Serviços de Comunicação do Azure:

| Recurso                               | Descrição                           |
|---                                    |---                                   |
|**[Bibliotecas de SDK e APIs REST](./concepts/sdk-options.md)**|As funcionalidades dos Serviços de Comunicação do Azure são organizadas conceitualmente em seis áreas, cada uma delas representada por um SDK. Decida quais bibliotecas de SDK usar com base em suas necessidades de comunicação em tempo real.|
|**[Visão geral do SDK de Chamada](./concepts/voice-video-calling/calling-sdk-features.md)**|Examine a visão geral do SDK de Chamada dos Serviços de Comunicação.|
|**[Visão geral do SDK de chat](./concepts/chat/sdk-features.md)**|Examine a visão geral do SDK de Chat dos Serviços de Comunicação.|
|**[Visão geral do SDK de SMS](./concepts/telephony-sms/sdk-features.md)**|Examine a visão geral do SDK de SMS dos Serviços de Comunicação.|

## <a name="other-microsoft-communication-services"></a>Outros Serviços de Comunicação da Microsoft

Há dois outros produtos de comunicação da Microsoft que você pode considerar utilizar e que não são diretamente interoperáveis com os Serviços de Comunicação no momento:

 - As [APIs de Comunicação de Nuvem do Microsoft Graph](/graph/cloud-communications-concept-overview) permitem que as organizações criem experiências de comunicação vinculadas a usuários do Azure Active Directory com licenças do Microsoft 365. Isso é ideal para aplicativos vinculados ao Azure Active Directory ou quando você deseja estender experiências de produtividade no Microsoft Teams. Também há APIs para criar aplicativos e personalização dentro da [experiência do Teams.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - O [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) simplifica a adição de comunicação de dados e chat de baixa latência a jogos. Embora você possa criar sistemas de rede e chat para jogos com os Serviços de Comunicação, o PlayFab é uma opção específica para isso e gratuita no Xbox.


## <a name="next-steps"></a>Próximas etapas

 - [Criar um recurso dos Serviços de Comunicação](./quickstarts/create-communication-resource.md)
