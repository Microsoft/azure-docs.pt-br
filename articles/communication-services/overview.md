---
title: O que são os Serviços de Comunicação do Azure?
description: Saiba como os Serviços de Comunicação do Azure ajudam você a desenvolver experiências de usuário avançadas com comunicações em tempo real.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d680df0ec5e18cca4d7a42edf45dbd6a50be1472
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888599"
---
# <a name="what-is-azure-communication-services"></a>O que são os Serviços de Comunicação do Azure?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Os Serviços de Comunicação do Azure permitem que você adicione facilmente recursos de comunicação multimídia com voz, vídeo e telefonia por IP aos seus aplicativos. As bibliotecas de clientes dos Serviços de Comunicação também permitem que você adicione funcionalidade de chat e SMS às suas soluções de comunicação.

<br>

> [!VIDEO https://www.youtube.com/embed/49oshhgY6UQ]

<br>
<br>

Você pode usar os Serviços de Comunicação para comunicação de voz, vídeo, texto e dados em diversos cenários:

- Comunicação do navegador para navegador, navegador para aplicativo e aplicativo para aplicativo
- Usuários interagindo com bots ou outros serviços
- Usuários e bots interagindo pela rede de telefonia comutada pública

Cenários mistos têm suporte. Por exemplo, um aplicativo dos Serviços de Comunicação pode ter usuários falando de navegadores e de dispositivos de telefonia tradicionais ao mesmo tempo. Os Serviços de Comunicação também podem ser combinados com o Serviço de Bot do Azure para criar sistemas de IVR (resposta de voz interativa) controlados por bot.

## <a name="common-scenarios"></a>Cenários comuns

Se você é novo nos Serviços de Comunicação do Azure, os seguintes recursos são um ótimo ponto de partida:
<br>

| Recurso                               |Descrição                           |
|---                                    |---                                   |
|**[Criar um recurso dos Serviços de Comunicação](./quickstarts/create-communication-resource.md)**|Você pode começar a usar os Serviços de Comunicação do Azure usando o portal do Azure ou a biblioteca de clientes de Administração dos Serviços de Comunicação para provisionar seu primeiro recurso dos Serviços de Comunicação. Quando tiver sua cadeia de conexão de recurso dos Serviços de Comunicação, você poderá provisionar seus primeiros tokens de acesso do usuário.|
|**[Criar seu primeiro token de acesso do usuário](./quickstarts/access-tokens.md)**|Os tokens de acesso do usuário são usados para autenticar seus serviços em relação ao seu recurso dos Serviços de Comunicação do Azure. Esses tokens são provisionados e reemitidos usando a biblioteca de clientes de Administração dos Serviços de Comunicação.|
|**[Obter um número de telefone](./quickstarts/telephony-sms/get-phone-number.md)**|Você pode usar os Serviços de Comunicação do Azure para provisionar e liberar números de telefone. Esses números de telefone podem ser usados para iniciar chamadas de saída e criar soluções de comunicação por SMS.|
|**[Enviar um SMS de seu aplicativo](./quickstarts/telephony-sms/send.md)**|A biblioteca de clientes de SMS dos Serviços de Comunicação do Azure permite que você envie e receba mensagens SMS de seus aplicativos .NET e JavaScript.|
|**[Introdução às chamadas de voz e vídeo](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Os Serviços de Comunicação do Azure permitem que você adicione chamadas de voz e de vídeo aos seus aplicativos usando a biblioteca de clientes de Chamada. Essa biblioteca usa a plataforma WebRTC e permite que você estabeleça comunicações de ponto a ponto, multimídia e em tempo real em seus aplicativos.|
|**[Introdução ao chat](./quickstarts/chat/get-started.md)**|A biblioteca de clientes de Chat dos Serviços de Comunicação do Azure pode ser usada para integrar chat em tempo real aos seus aplicativos.|


## <a name="samples"></a>Exemplos

Os exemplos a seguir demonstram a utilização de ponta a ponta das bibliotecas de clientes dos Serviços de Comunicação do Azure. Sinta-se à vontade para usar esses exemplos para inicializar suas próprias soluções dos Serviços de Comunicação.
<br>

| Nome da amostra                               | Descrição                           |
|---                                    |---                                   |
|**[Exemplo de destaque de chamada de grupo](./samples/calling-hero-sample.md)**|Veja como as bibliotecas de cliente dos Serviços de Comunicação podem ser usadas para criar uma experiência de chamada de grupo.|
|**[Exemplo de destaque de chat de grupo](./samples/chat-hero-sample.md)**|Veja como as bibliotecas de clientes dos Serviços de Comunicação podem ser usadas para criar uma experiência de chat em grupo.|


## <a name="platforms-and-client-libraries"></a>Plataformas e bibliotecas de clientes

Os recursos a seguir ajudarão você a aprender sobre as bibliotecas de cliente dos Serviços de Comunicação do Azure:

| Recurso                               | Descrição                           |
|---                                    |---                                   |
|**[Bibliotecas de cliente e APIs REST](./concepts/sdk-options.md)**|As funcionalidades dos Serviços de Comunicação do Azure são organizadas conceitualmente em seis áreas, cada uma delas representada por uma biblioteca de clientes. Decida quais bibliotecas de clientes usar com base em suas necessidades de comunicação em tempo real.|
|**[Visão geral da biblioteca de clientes de chamada](./concepts/voice-video-calling/calling-sdk-features.md)**|Examine a visão geral da biblioteca de clientes de chamada dos Serviços de Comunicação.|
|**[Visão geral da biblioteca de clientes de chat](./concepts/chat/sdk-features.md)**|Examine a visão geral da biblioteca de clientes de chat dos Serviços de Comunicação.|
|**[Visão geral da biblioteca de clientes de SMS](./concepts/telephony-sms/sdk-features.md)**|Examine a visão geral da biblioteca de clientes de SMS dos Serviços de Comunicação.|

## <a name="compare-azure-communication-services"></a>Comparar os Serviços de Comunicação do Azure

Há dois outros produtos de comunicação da Microsoft que você pode considerar utilizar e que não são diretamente interoperáveis com os Serviços de Comunicação no momento:

 - As [APIs de Comunicação de Nuvem do Microsoft Graph](/graph/cloud-communications-concept-overview) permitem que as organizações criem experiências de comunicação ligadas a usuários do Azure Active Directory com licenças do M365. Isso é ideal para aplicativos vinculados ao Azure Active Directory ou quando você deseja estender experiências de produtividade no Microsoft Teams. Também há APIs para criar aplicativos e personalização dentro da [experiência do Teams.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - O [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) simplifica a adição de comunicação de dados e chat de baixa latência a jogos. Embora você possa criar sistemas de rede e chat para jogos com os Serviços de Comunicação, o PlayFab é uma opção específica para isso e gratuita no Xbox.


## <a name="next-steps"></a>Próximas etapas

 - [Criar um recurso dos Serviços de Comunicação](./quickstarts/create-communication-resource.md)