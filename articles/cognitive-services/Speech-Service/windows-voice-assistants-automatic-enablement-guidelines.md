---
title: Diretrizes de privacidade para assistentes de voz no Windows
titleSuffix: Azure Cognitive Services
description: As instruções para habilitar a ativação de voz para um agente de voz por padrão.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90987391"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Diretrizes de privacidade para assistentes de voz no Windows

É importante que os usuários recebam informações claras sobre como seus dados de voz são coletados e usados e é importante que eles recebam controle sobre se e como essa coleção acontece. Essas facetas principais de privacidade – *divulgação* e *consentimento* – são especialmente importantes para os assistentes de voz integrados ao Windows, considerando a natureza sempre ouvindo de seu uso.

Os desenvolvedores que criam assistentes de voz no Windows devem incluir elementos de interface do usuário claros em seus aplicativos que refletem os recursos de escuta do assistente.

> [!NOTE]
> A falha em fornecer a divulgação e o consentimento apropriados para um aplicativo assistente, incluindo após as atualizações do aplicativo, pode fazer com que o assistente fique indisponível para ativação de voz até que os problemas de privacidade sejam resolvidos.

## <a name="minimum-requirements-for-feature-inclusion"></a>Requisitos mínimos para inclusão de recursos

Os usuários do Windows podem ver e controlar a disponibilidade de seus aplicativos assistente no **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![Captura de tela mostra opções para controlar a disponibilidade do Cortana. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Uma entrada de configuração de privacidade de ativação do Windows Voice para um aplicativo assistente")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Para se tornar qualificado para inclusão nesta lista, contate a Microsoft em winvoiceassistants@microsoft.com para começar. Por padrão, os usuários precisarão habilitar explicitamente a ativação por voz para um novo assistente no **`Settings > Privacy > Voice Activation`** , ao qual um aplicativo pode vincular o link com `ms-settings:privacy-voiceactivation` . Um aplicativo permitido aparecerá na lista depois que ele tiver sido executado e usado as `Windows.ApplicationModel.ConversationalAgent` APIs. Suas configurações de ativação de voz poderão ser modificadas depois que o aplicativo tiver obtido o consentimento do microfone do usuário.

Como as configurações de privacidade do Windows incluem informações sobre como a ativação de voz funciona e tem interface do usuário padrão para controlar a permissão, a divulgação e o consentimento são ambos atendidos. O assistente permanecerá nessa lista de permissões, desde que não:

* Induzir ou misinformr o usuário sobre ativação de voz ou manipulação de dados de voz pelo assistente
* Interfere indevidamente com outro assistente
* Interromper qualquer outra política relevante da Microsoft

Se qualquer um dos anteriores for descoberto, a Microsoft poderá remover um assistente da lista de permissões até que os problemas sejam resolvidos.

> [!NOTE]
> Em todos os casos, a permissão de ativação de voz requer permissão de microfone. Se um aplicativo de assistente não tiver acesso ao microfone, ele não será elegível para ativação de voz e aparecerá nas configurações de privacidade de ativação de voz em um estado desabilitado.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Requisitos adicionais para inclusão no consentimento do microfone

Os autores do assistente que desejam tornar mais fácil e suave que os usuários aceitem a ativação de voz podem fazer isso atendendo aos requisitos adicionais para atender adequadamente à divulgação e ao consentimento sem uma viagem extra para a página de configurações. Depois de aprovado, a ativação de voz ficará disponível imediatamente quando um usuário conceder permissão de microfone ao aplicativo assistente. Para se qualificar para isso, um aplicativo assistente deve fazer o seguinte **antes** de solicitar o consentimento do microfone (por exemplo, usando a `AppCapability.RequestAccessAsync` API):

1. Forneça uma indicação clara e proeminente ao usuário que o aplicativo gostaria de escutar na voz do usuário por uma palavra-chave, *mesmo quando o aplicativo não está em execução* e gostaria do consentimento do usuário
1. Inclua informações relevantes sobre o uso de dados e políticas de privacidade, como um link para uma política de privacidade oficial
1. Evite qualquer diretiva ou palavra à esquerda (por exemplo, "clique em Sim no seguinte prompt") no fluxo de experiência que divulga o comportamento de captura de áudio

Se um aplicativo realiza todas as opções acima, ele é elegível para habilitar o recurso de ativação de voz junto com o consentimento do microfone. Entre em contato winvoiceassistants@microsoft.com para obter mais informações e para examinar uma experiência de uso inicial.

> [!NOTE]
> A ativação de voz acima do bloqueio não está qualificada para habilitação automática com acesso ao microfone e ainda exigirá que um usuário visite a página de privacidade ativação de voz para habilitar o acesso de bloqueio acima para um assistente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre as práticas recomendadas para assistentes de voz no Windows](windows-voice-assistants-best-practices.md)