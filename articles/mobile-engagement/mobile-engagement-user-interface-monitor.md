---
title: "Interface do usuário do Azure Mobile Engagement - Monitor"
description: Saiba como monitorar dados em tempo real sobre seu aplicativo usando o Azure Mobile Engagement
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: b91ad89a-b89d-4377-abb0-cc2d16a2836d
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 5f8a02e35db93585e0fe46d77b3ad18b94c99597
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-real-time-data-about-your-application"></a>Como monitorar dados em tempo real sobre o seu aplicativo
Este artigo descreve a guia **MONITORAR** do portal do **Mobile Engagement**. Você usa o portal do **Mobile Engagement** para monitorar e gerenciar seus aplicativos móveis. Observe que, para começar a usar o portal, primeiro é preciso criar uma conta do **Azure Mobile Engagement** . 

A seção Monitorar da interface do usuário fornece informações de análise em tempo real e permite que você defina alertas quando os limites são atingidos para a maior parte das mesmas informações que estão disponíveis historicamente na seção [ANÁLISE](mobile-engagement-user-interface-analytics.md) da interface do usuário. Veja a seção **Glossário** no tópico [Conceitos](http://go.microsoft.com/fwlink/?LinkId=525555) para ver as definições de termos e abreviaturas na Análise e no Monitoramento (como os seguintes: Usuário Ativo, Novo Usuário, Usuário Retido, Sessão, Gráfico de Trajetória do Usuário, Mapa de Usuários, URLs de Acompanhamento, Tendências, Atividade, Eventos, Trabalho, Erro, Informações Adicionais, Falha e Informações do Aplicativo).

> [!NOTE]
> Muitas seções da interface do usuário do portal do **Mobile Engagement** contêm o botão **MOSTRAR AJUDA**. Pressione este botão para obter mais informações contextuais sobre uma seção.
> 
> 

## <a name="monitor---sessions-jobs-events-errors-and-crashes"></a>Monitorar - sessões, trabalhos, eventos, erros e falhas
Você pode ver quantos usuários estão atualmente na sessão e em telas específicas ou executando ações específicas. Você pode exibir atividades de usuário divididas em sessões, trabalhos, eventos, erros e falhas. Você pode ver as informações atuais e mostrar as informações da última hora, dia ou semana. Você pode ver todas as informações em cada categoria ou classificação especificamente segundo a sessão, trabalho, evento, erro e falha.  Monitoramento em tempo real é útil para uso durante eventos como uma campanha de envio por push para ver se há um aumento na ação à direita após enviar sua notificação de Push.

![Monitor1][14]  

## <a name="troubleshooting-with-monitor---events---details"></a>Solucionando problemas com o Monitor - Eventos - Detalhes
Gerar um evento em seu aplicativo do dispositivo de teste e encontrá-lo em Monitor - Eventos - Detalhes é uma das maneiras mais fáceis de encontrar a ID do dispositivo para o dispositivo de teste e confirmar que a integração de Segmentos, Monitoramento e Análise do Azure Mobile Engagement está funcionando em seu aplicativo. Uma vez que você tenha a ID de dispositivo do seu dispositivo de teste, você poderá adicioná-la a seus dispositivos de teste em "Minha conta – dispositivos". Se você não pode gerar um evento, certifique-se de que o Azure Mobile Engagement esteja corretamente integrado em seu aplicativo do Android/iOS/Web/Windows/Windows Phone com o SDK.

Para obter mais informações, veja: [Documentação do SDK][Link 5]

![Monitor2][15]  

## <a name="troubleshooting-with-monitor---crashes---details"></a>Solucionando problemas com Monitor - Falhas - Detalhes
Você pode examinar informações de falhas sobre seu aplicativo em Monitor - Falhas - Detalhes para ajudar a determinar por que seu aplicativo está falhando. Você também deve procurar problemas conhecidos com cada versão do SDK nas notas de versão para cada versão do SDK para Android/iOS/Web/Windows/Windows Phone.

Para obter mais informações, veja: [Documentação do SDK – Notas de versão][Link 5]

![Monitor3][16]

## <a name="monitor---alerts"></a>Monitor - Alertas
Você também pode especificar condições para alertas que serão enviados automaticamente para você por email ou mensagem instantânea. (Quaisquer serviços compatíveis com XMPP, como GTalk do Google ou iChat da Apple, têm suporte). Os alertas são baseados em um limite de detecção predefinido maior que (>) ou menor que (<) um número específico de sessões, trabalhos, eventos, erros ou falhas por hora, minuto ou segundo. Alertas podem monitorar todas as atividades de um determinado tipo, ou apenas para monitorar uma atividade de um trabalho, evento ou erro específico. 

Você também pode especificar uma taxa de detecção mínima, que é a quantidade mínima de minutos que vai separar duas notificações para o mesmo alerta para assegurar que quando o alerta for disparado, você nunca receberá mais de 1 notificação por intervalo especificado.

![Monitor4][17]

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
