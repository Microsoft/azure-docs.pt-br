---
title: "Interface do usuário do Azure Mobile Engagement - Configurações"
description: "Como gerenciar as configurações globais do seu aplicativo usando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: af5c81df2b9f288161b38625d3ac2adde8fb195d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>Como gerenciar as configurações globais do seu aplicativo
As opções do menu **Configurações** disponíveis para um aplicativo variam, dependendo da plataforma do aplicativo e das permissões que você recebeu para o aplicativo. As configurações incluem: Detalhes, Projetos, Push Nativo, Velocidade do Push, Marca (informações do aplicativo) e Pressão Comercial. A opção de menu Marca (informações do aplicativo) da seção Configurações pode ser gerenciada pelo seu aplicativo (usando o SDK) ou por seu back-end (usando a API do Dispositivo). 

> [!NOTE]
> Muitas seções da interface do usuário do portal do **Mobile Engagement** contêm o botão **MOSTRAR AJUDA**. Pressione este botão para obter mais informações contextuais sobre uma seção.
> 
> 

## <a name="details"></a>Detalhes
Permite que você altere o nome e a descrição do seu aplicativo e exiba o proprietário do aplicativo e as permissões da função. 

A configuração de análise permite exibir ou alterar o dia de início das semanas e o tempo de retenção em dia(s).

  ![settings1][46]

## <a name="projects"></a>Projetos
Permite selecionar todos os projetos em que você quer que o aplicativo apareça. 

Você também pode pesquisar um projeto e exibir o nome, descrição, proprietário e as permissões da função de qualquer projeto de que seu aplicativo faz parte.

Para obter mais informações, veja: [Documentação da Interface do Usuário – Página Inicial][Link 13]

  ![settings3][48]

## <a name="native-push"></a>Push nativo
Permite o registro de um novo certificado ou a exclusão de um certificado existente para o uso com o envio nativo. O Push nativo permite o Azure Mobile Engagement a enviar por push para o seu aplicativo a qualquer momento, mesmo quando ele não está em execução. 

Após fornecer as credenciais ou certificados para pelo menos um serviço de push nativo, pode-se selecionar "Sempre" ao criar de campanhas Reach, bem como usar o parâmetro "notificação" na API de envio.

### <a name="apple-push-notification-service-apns"></a>Serviço de notificação por push da Apple (APNS)
Para habilitar o envio nativo usando o serviço de notificação por push da Apple você precisará registrar seu certificado. Você precisará especificar o tipo de certificado como desenvolvimento (DES) ou de produção (PROD). Em seguida, você precisará carregar o certificado e a senha.

Para obter mais informações, veja: [Documentação do SDK – iOS – Como preparar seu aplicativo para notificações por push da Apple][Link 5]

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>Serviço de notificação por push do Windows (WPNS)
Para habilitar o envio nativo usando o serviço de notificação do Windows, você deve fornecer as credenciais do seu aplicativo. Você precisará do seu identificador de segurança do pacote (SID) e da chave secreta.

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud Messaging para Android (GCM)
Para habilitar o push nativo usando o GCM, você precisa seguir as instruções do Google. Em seguida, cole uma chave simples de API do servidor, configurada sem restrições de IP. Requer integração com o SDK para Android v1.12.0 +.

Para obter mais informações, consulte: 

* [Documentação do SDK Android Como integrar o GCM][Link 5]
* [Guia do GCM do desenvolvedor do Google](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>Dispositivo Amazon Messaging para Android (ADM)
Para habilitar o envio nativo usando o ADM, é preciso fornecer a <OAuth credentials> do Amazon que consiste em uma ID de cliente e o segredo do cliente (requer integração com o SDK para Android versão 1.0+).

Para obter mais informações, consulte: 

* [Documentação do SDK Android Como integrar o ADM][Link 5]
* [Documentação do ADM Amazon Developer](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>Velocidade de push
Mostra a velocidade de push atual do seu aplicativo e permite que você defina a velocidade de push do seu aplicativo.

  ![settings7][52]

## <a name="tag-app-info"></a>Marca (informações do aplicativo)
![settings11][56]

## <a name="commercial-pressure"></a>Pressão comercial
![settings12][57]

## <a name="see-also"></a>Consulte também
* [Conceitos][Link 6]
* [Serviço do Guia de Solução de Problemas][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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

