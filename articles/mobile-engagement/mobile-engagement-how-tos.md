---
title: "Interface do usuário do Azure Mobile Engagement - Passo a passo do Reach"
description: "Visão geral da Interface de usuário para o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 33a0a9d0c399cb7f0a791c4c16dde2e2d62364ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Como começar a usar e gerenciar pushes para chegar aos seus usuários finais
Depois que o SDK é totalmente integrado ao seu aplicativo, você pode começar a usar a seção Reach da interface do usuário para enviar notificações por Push aos usuários do seu aplicativo.  

## <a name="do-your-first-push-notification-campaign"></a>Faça sua primeira campanha de notificação por push
* Confirme que o seu Reach está integrado ao seu aplicativo com o SDK. 
* Selecione seu aplicativo

![First1][1]

* Vá para a seção "Reach" e clique em "Novo comunicado"

![First2][2]

* Criar uma nova campanha e nomeie-a
  
![First3][3]

* Selecione como a notificação deve ser entregue, como no aplicativo somente

![First4][4]

* Criar a mensagem que deseja enviar por push

![First5][5]

* Você pode escrever um título nas notificações (opcional).
* Gravar o conteúdo da mensagem por push.
* Você pode carregar uma imagem. Lembre-se de que o tamanho do arquivo não pode exceder 32.768 bytes.
* Você também tem a capacidade de selecionar mais opções, mas para manter o foco deste tutorial, veremos isso mais tarde.
* Selecione o tipo de conteúdo como Notificação apenas

![First6][6]

* Crie sua campanha de envio e ele aparecerá na lista de campanha.

![First7][7]

## <a name="test-your-push-notification-campaign"></a>Testar sua campanha de notificação por push
![Test1][8]

* Registre seu dispositivo.
* Clique na caixa de seleção do dispositivo que deseja enviar por push.
* Clique no botão "Testar" para enviar o envio por push para o dispositivo.

![Test2][9]

* Ativar a campanha

![Test3][10]

* Agora que você criou sua campanha basta ativá-la para a notificação ser enviada para os usuários.

## <a name="send-personalized-pushes"></a>Enviar pressionamentos personalizados
* Este exemplo cria um esforço em que um código de desconto personalizado é inserido em uma notificação por push.

![Personalize1][11]

A personalização funciona substituindo um marcador por uma marca de informações do aplicativo, então, você precisará certificar-se de que o usuário tem as informações do aplicativo apropriado e definido primeiro. Neste exemplo, os usuários de destino terão uma marca definida de informações de aplicativo chamada rebate_code.
Como você pode ver acima, o conteúdo da notificação por push inclui o ${rebate_code} de marcador que indicará que ela deve ser substituída pelo conteúdo real da marca de informações do aplicativo.

> [!WARNING]
> Se a marca de informações de aplicativo não estiver definida para o usuário, o usuário não receberá o envio por push.

* Result

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Você pode personalizar ainda mais o texto da notificação
![Personalize3][13]

* Incluindo o título da notificação,
* E o conteúdo da mensagem.
* Escolha o tipo de comunicado (exibição de texto ou da Web)

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>O corpo de um comunicado também pode ser personalizado com:
* A URL da ação, caso você deseje personalizar a página de aterrissagem
* O título
* O corpo da mensagem.

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Diferenciar a sua notificação por push (estando ou não no aplicativo)
* Escolha o tipo de notificação você enviará por push, selecione seu aplicativo, vá para a seção "Reach", selecione ou crie uma campanha de envio por push e vá para a seção "Notificação".
* Clique no "modo de entrega" desejado.
* Clique na caixa de seleção "Restringir atividades" quando desejar que a notificação ocorra em atividades específicas (telas).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Modo de entrega "Somente fora do aplicativo"
![Differentiate2][16]

O modo de entrega "Somente fora do aplicativo" fornece notificação por push quando o aplicativo é fechado. Esta é a notificação por push padrão.
Ao selecionar "Somente fora do aplicativo", você deve ter fornecido os certificados da plataforma que o seu aplicativo está compilando (APNS ou GCM).

### <a name="see-also"></a>Consulte também
* [Apple Push Notification Service – Certificados](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging – Certificado](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>Modo de entrega "Apenas no aplicativo"
![Differentiate3][17]

O modo de entrega "Apenas no aplicativo" fornece notificação por push quando o aplicativo é executado.
Para esta notificação, você não precisa passar pelo sistema APNS e GCM.
Você pode usar o sistema de entrega no aplicativo para alcançar os seus usuários finais.
Você pode personalizar completamente a notificação e decidir em qual atividade (tela) a notificação será exibida.

### <a name="anytime-delivery-mode"></a>Modo de entrega a "Qualquer hora"
Você pode escolher um modo de entrega a "Qualquer hora", e isso garante alcançar o seu usuário final se o aplicativo estiver em execução ou não.
Ao selecionar a "Qualquer hora", você já deve ter fornecido os certificados da plataforma em que o seu aplicativo está complementando (APNS ou GCM). 

## <a name="schedule-a-push-campaign"></a>Agendar uma campanha de envio por push
### <a name="plan-to-start-a-campaign"></a>Plano para iniciar uma campanha
![Shedule1][18]

É 21 de março e você tem um comunicado para fazer e ele está planejado para o dia 22 de março à meia-noite. Você não precisa estar na frente da interface para realizar um envio por push! Você pode planejar com antecedência o exato minuto em que as notificações serão enviadas.

* Desmarque a caixa de seleção "Nenhum" e selecione uma hora de início 
* Escolha a data e a hora em que você deseja iniciar a campanha de envio por push.

### <a name="plan-to-end-a-campaign"></a>Plano para terminar uma campanha
![Shedule2][19]

Você deseja que a sua campanha seja encerrada no dia 25 de março às 15h, mas sabe que não estará disponível para fazê-lo.
Você não precisa estar na frente da interface para realizar um envio por push! Você pode planejar com antecedência o exato minuto que sua campanha será interrompida.

* Clique na caixa de seleção "Nenhum" ou selecione uma hora de término
* Escolha a data e a hora em que você deseja terminar a campanha de envio por push.

### <a name="end-a-campaign-manually"></a>Terminar uma campanha manualmente
![Shedule3][20]

Por padrão, as caixas de seleção "Nenhum" estão marcadas.
Isso significa que a campanha começará assim que você ativá-la na seção de Reach e terminará quando você a terminar na seção de Reach.

> [!NOTE]
> As campanhas criadas sem uma data de término armazenam o envio por push localmente no dispositivo e mostram-no na próxima vez em que o aplicativo é aberto, mesmo que a campanha seja encerrada manualmente.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Aprimorar uma notificação por Push com uma Exibição de texto
### <a name="what-is-a-text-view"></a>O que é uma Exibição de texto?
![TextView1][21]

Uma exibição de texto é um pop-up com conteúdo de texto. Essa janela pop-up aparece depois que o usuário final tiver clicado em uma notificação por push.
Uma exibição de texto permite apresentar mais conteúdo ao usuário final. Essa também é a oportunidade de apresentar um plano de ação, como saltar para uma página do seu aplicativo, redirecionando para um Repositório, abrindo uma página da Web, enviando um email, iniciando uma pesquisa localizada geograficamente, etc...

### <a name="example-text-view"></a>Exemplo: Exibição de texto
* Crie a sua campanha de notificação por Push na seção "Reach" e atribua um nome à sua campanha

![TextView2][22]

* Escreva a mensagem que será exibida na notificação.
* Selecione o tipo de conteúdo do comunicado de "texto"

![TextView3][23]

> [!NOTE]
> Quando você envia por push uma exibição de texto, ela sempre vem com uma notificação pela primeira vez. 

* Definir o texto (depois de ter selecionado o conteúdo do comunicado de texto, a subseção será exibida, permitindo que você defina o texto a ser exibido.)

![TextView4][24]

* Escreva o título que aparece na parte superior da mensagem.
* Escreva o conteúdo principal da exibição de texto.
* Escreva o conteúdo que será exibido no botão de ação (um botão de ação habilita o aplicativo a fazer uma ação específica, como abrir uma página do aplicativo, redirecionar para um repositório de aplicativos ou qualquer tipo de fonte que você possa fornecer).
* Escreva o conteúdo que será exibido no botão Sair (clicando no botão Sair, o modo de texto desaparecerá).
* Crie sua campanha de notificação por push e ela aparecerá na lista de campanha.

![TextView5][25]

* Ative sua campanha de notificação por push para enviar a exibição de texto para seus usuários.

![TextView6][26]

* Result

![TextView7][27]

* O usuário recebe a notificação e clica nela.
* A exibição de texto aparece como um pop-up permitindo que o usuário interaja com ele.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Aprimorar uma notificação por push com uma exibição da web
### <a name="what-is-a-web-view"></a>O que é uma exibição da Web?
![WebView1][28]

Uma exibição da web é um pop-up com conteúdo da web. Essa janela pop-up aparece quando o usuário final tiver clicado em uma notificação por push.
Uma exibição da web permite que você tenha mais interação com o usuário final.
Essa também é a oportunidade de apresentar um plano de ação, como redirecionamento para a Loja de Aplicativos, abrir uma página da Web, enviar um email, iniciar uma pesquisa localizada geograficamente, etc...

### <a name="example-web-view"></a>Exemplo: Exibição da Web
* Crie a sua campanha de envio por push na seção “Reach" e atribua um nome à sua campanha.

![WebView2][29]

* Escreva a mensagem que será exibida na notificação.
* Selecione o tipo de conteúdo do comunicado como "web"

![WebView3][30]

### <a name="about-announcement-types"></a>Sobre os tipos de comunicado:
* Somente notificação: é uma notificação simples padrão. Isso significa que se um usuário clicar nela, nenhum modo de exibição adicional será exibido, mas apenas a ação associada a ela ocorrerá.
* Anúncio de texto: é uma notificação que leva o usuário a dar uma olhada em uma exibição de texto.
* Anúncio pela: é uma notificação que leva o usuário a dar uma olhada em uma exibição da Web.
  Selecione o conteúdo de "Comunicado da Web".

> [!NOTE]
> quando você envia por push uma exibição da web, ela sempre vem com uma notificação pela primeira vez.

* Defina o conteúdo da web (depois de ter selecionado o conteúdo de comunicado da web, será exibida a subseção, permitindo que você defina o conteúdo da exibição da web a ser exibido.)

![WebView4][31]

* Escreva o título que aparecerá na parte superior da mensagem (opcional).
* Escreva o código HTML aqui.
* Clique no botão de modo da fonte de edição para alternar a edição e ver como ele parece.
* Escreva o conteúdo que será exibido no botão de ação (um botão de ação habilita o aplicativo a fazer uma ação específica, como abrir uma página do aplicativo, redirecionar para um Repositório ou qualquer tipo de fonte que você possa fornecer).
* Escreva o conteúdo que será exibido no botão Sair (clicando no botão Sair, a exibição da web desaparecerá).
* Result

![WebView5][32]

* O usuário recebe a notificação e clica nela.
* A exibição de texto aparece como um pop-up permitindo que o usuário interaja com ele.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

