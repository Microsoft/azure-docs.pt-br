---
title: Use emoticons Emoji com o Azure Mobile Engagement
description: "Como usar emoticons Emoji dentro de suas notificações de push"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 663317d7-3c93-4e8f-b13d-c6fb342124ee
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bbb7ce5e95b229a7505c5e97b6866d5a302a1d27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-emoji-emoticon-within-push-notifications"></a>Use o emoticon Emoji em notificações por Push
Você pode incluir emoticons Emoji em notificações por push em algumas etapas fáceis: 

1. Em primeiro lugar, que você precisa encontrar o Emoji que quer enviar na mensagem. Verifique se o Emoji que você está selecionando será compatível com o dispositivo de destino, já que os fabricantes de dispositivos levam algum tempo para adicionar Emojis recém-aprovados às plataformas de dispositivo. 
2. No **Windows** – você pode navegar para este [link](http://apps.timwhitlock.info/emoji/tables/unicode) e copiar o ícone 'Native'.
   
    ![][7] 
3. No **Mac**, é possível encontrar os Emojis no aplicativo de Dicionário em Editar -> Emoji e símbolos.
   
    ![][6] 
4. Vá até a guia **Reach** no portal do Azure Mobile Engagement. Selecione o tipo de sua notificação por push (sonda lançamento, etc). Para este exemplo, escolhemos um envio de anúncio.
5. Especifique os campos diferentes da notificação até alcançar o texto da notificação. Este é o lugar onde você irá adicionar seu emoticom Emoji. Você pode optar por colocá-lo no título, na mensagem ou em ambos. Arraste e solte ou copie o Emoji localizado nos locais acima. 
   
    ![][1]
6. Preencha os outros campos para a notificação e salve-o. 
7. Quando você executar um teste ou ativar o anúncio, verá uma notificação com o emoticon conforme especificado.   
   
    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

