---
title: Assistentes de voz no Windows-visão geral
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos assistentes de voz no Windows, incluindo recursos e recursos de desenvolvimento disponíveis.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 9e8b009ecc2181edfaad5da3d8d05ad0c1909051
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024748"
---
# <a name="voice-assistants-on-windows"></a>Assistentes de voz no Windows

No Windows 10 versão 2004 e superior, os aplicativos do assistente de voz podem aproveitar as APIs do Windows ConversationalAgent para obter uma experiência completa do assistente habilitado para voz.

## <a name="voice-assistant-features"></a>Recursos do assistente de voz

Os aplicativos de agente de voz podem ser ativados por uma palavra-chave falada para habilitar uma experiência orientada por voz sem intervenções. A ativação de voz funciona quando o aplicativo é fechado e quando a tela é bloqueada.

Além disso, o Windows fornece um conjunto de configurações de privacidade de ativação de voz que fornece aos usuários o controle de ativação de voz e acima da ativação de bloqueio por aplicativo.

Após a ativação de voz, o Windows gerenciará vários agentes ativos corretamente e notificará cada assistente de voz se eles forem interrompidos ou desativados. Isso permite que os aplicativos gerenciem interrupções e outros eventos entre agentes corretamente.

## <a name="how-does-voice-activation-work"></a>Como funciona a ativação de voz?

O AAR (tempo de execução de ativação do agente) é o processo contínuo no Windows que gerencia a ativação do aplicativo em uma palavra-chave ou um botão de clique falado. Ele começa com o Windows, contanto que haja pelo menos um aplicativo no sistema que esteja registrado no sistema. Os aplicativos interagem com AAR por meio das APIs do ConversationalAgent no SDK do Windows.

Quando o usuário fala uma palavra-chave, a palavra-chave software ou hardware spotter no sistema notifica AAR de que uma palavra-chave foi detectada, fornecendo uma ID de palavra-chave. O AAR, por sua vez, envia uma solicitação para BackgroundService para iniciar o aplicativo com a ID do aplicativo correspondente.

### <a name="registration"></a>Registro

Na primeira vez em que um aplicativo ativado por voz é executado, ele registra sua ID do aplicativo e informações de palavra-chave por meio das APIs ConversationalAgent. AAR registra todas as configurações no mapeamento global com a palavra-chave hardware ou software spotter no sistema, permitindo que ele detecte a palavra-chave do aplicativo. O aplicativo também é [registrado com o serviço em segundo plano](/windows/uwp/launch-resume/register-a-background-task).

Observe que isso significa que um aplicativo não pode ser ativado por voz até que seja executado uma vez e o registro tenha sido permitido para ser concluído.

### <a name="receiving-an-activation"></a>Recebendo uma ativação

Após receber a solicitação de AAR, o serviço de segundo plano inicia o aplicativo. O aplicativo recebe um sinal por meio do método ciclo de vida OnBackgroundActivated em `App.xaml.cs` com um argumento de evento exclusivo. Esse argumento informa ao aplicativo que ele foi ativado pelo AAR e que ele deve iniciar a verificação de palavra-chave.

Se o aplicativo verificar a palavra-chave com êxito, ele poderá fazer com que uma solicitação seja mostrada em primeiro plano. Quando essa solicitação for realizada com sucesso, o aplicativo exibirá a interface do usuário e continuará sua interação com ele.

AAR ainda sinaliza aplicativos ativos quando sua palavra-chave é falada. Em vez de sinalizar por meio do método ciclo de vida no `App.xaml.cs` , no entanto, ele sinaliza um evento nas APIs do ConversationalAgent.

### <a name="keyword-verification"></a>Verificação de palavra-chave

A palavra-chave spotter que dispara o aplicativo para iniciar obteve baixo consumo de energia, simplificando o modelo de palavra-chave. Isso permite que a palavra-chave spotter seja "Always on" sem um alto impacto de energia, mas também significa que a palavra-chave spotter provavelmente terá um grande número de "false aceitos", em que detecta uma palavra-chave, embora nenhuma palavra-chave tenha sido falada. É por isso que o sistema de ativação de voz inicia o aplicativo em segundo plano: para dar ao aplicativo uma chance de verificar se a palavra-chave foi falada antes de interromper a sessão atual do usuário. O AAR salva o áudio desde alguns segundos antes de a palavra-chave ser encontrada e a torna acessível ao aplicativo. O aplicativo pode usar isso para executar uma palavra-chave mais confiável spotter no mesmo áudio.

## <a name="next-steps"></a>Próximas etapas

- **Examine as diretrizes de design:** Nossas [diretrizes de design](windows-voice-assistants-best-practices.md) destacam o trabalho principal necessário para fornecer as melhores experiências possíveis para ativação de voz no Windows 10.
- **Visite a página Introdução:** Comece [aqui](how-to-windows-voice-assistants-get-started.md) para as etapas para começar a implementar os assistentes de voz no Windows, desde a configuração de seu ambiente de desenvolvimento até o guia de introdução à implementação.
- **Experimente o aplicativo de exemplo**: para experimentar esses recursos em primeira mão, visite a página de [exemplo do assistente de voz UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) e siga as etapas para obter o cliente de exemplo em execução.