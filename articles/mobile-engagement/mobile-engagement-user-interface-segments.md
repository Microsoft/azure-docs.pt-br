---
title: "Azure Mobile Engagement - Interface do usuário - Segmentos"
description: "Como criar e gerenciar segmentos de usuários para identificar padrões de uso usando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 087f4a1fef420abe9669f8dfe2b84c7a847ce263
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Como criar e gerenciar segmentos de usuários para identificar padrões de uso
Este artigo descreve a guia **SEGMENTOS** do portal do **Mobile Engagement**. Você usa o portal do **Mobile Engagement** para monitorar e gerenciar seus aplicativos móveis.

A seção de segmentos da interface do usuário permite que você trabalhe na segmentação dos usuários com base no comportamento diferente e na análise que você pode obter do aplicativo e também pode acessar por meio da API de segmentos. Os segmentos são primeiramente calculados 24 horas após terem sido criados e serão recalculados com base nas informações de análise mais recentes a cada 24 horas. Após um segmento ter sido calculado, ele exibe um gráfico de “Histórico diário" todos os dias.

> [!NOTE]
> Muitas seções da interface do usuário do portal do **Mobile Engagement** contêm o botão **MOSTRAR AJUDA**. Pressione este botão para obter mais informações contextuais sobre uma seção.
> 
> 

## <a name="create-segments"></a>Criação segmentos
Você pode criar um segmento com base em até 10 critérios em um período específico de até 60 dias passados na seção de análise. Por exemplo, você pode criar um segmento com base em quem tiver visualizado determinadas páginas ou pesquisado um conteúdo específico em seu aplicativo nos últimos 10 dias. Essas informações estão disponíveis na seção de análise. Assim, você pode usá-las para criar um segmento e, em seguida, configurar uma notificação por push para atingir esse subconjunto de usuários para que eles voltem para o aplicativo. 

> [!NOTE]
> Após um segmento ter sido calculado, ele não pode ser editado; ele só pode ser clonado (copiado) ou destruído (excluído). Um segmento pode ser clonado dentro do mesmo aplicativo (com o mesmo AppID) e também podem ser clonado em outros aplicativos (com um AppID diferente). 

 ![segments1][35] 

## <a name="examples-segments"></a>Exemplos de segmentos
 ![segments2][36]

Os segmentos permitem segmentar os usuários finais do seu aplicativo.
Segmentar os usuários é uma importante estratégia de marketing. O Azure Mobile Engagement permite que você obtenha os dados dos históricos e crie segmentos personalizados. Essa poderosa ferramenta permite que você saiba mais sobre a experiência dos clientes em seu aplicativo. Você pode facilmente analisar os segmentos e usá-los como destinos de envio.
Um caso de uso comum é quando você deseja enviar uma notificação para estimular os usuários finais a avaliar o seu aplicativo na loja. Em vez de enviar uma notificação para todos os seus usuários finais, você pode criar um segmento que especifique somente os usuários que usaram o aplicativo todos os dias no último mês e que têm tido um ótima experiência. Quando você envia notificações por push menos extremamente específicas, você pode obter um melhor ROI.

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Segmentos que você pode criar com base nos elementos principais do Azure Mobile Engagement:
* Evento: crie um segmento que tem como alvo um determinado evento do aplicativo que ocorrereu mais de duas vezes por semana. 
* Sessão: crie um segmento de usuários que usaram o aplicativo mais de 5 vezes na semana passada.
* Atividade: crie um segmento de usuários que usaram uma página ou conteúdo mais ou menos 10 vezes no mês passado.
* Trabalho: crie um segmento de usuários que concluíram um trabalho mais de duas vezes ao dia.
* Falha: crie um segmento de todos os usuários que tiveram uma falha de mais de 10 vezes na semana passada. (Você poderia enviar este segmento com uma desculpa ou até mesmo um cupom!)
* Erro: crie um segmento de todos os usuários que tiveram um erro mais de100 vezes nos últimos 3 dias.
* Informações do aplicativo: crie um segmento que tem como alvo as informações do aplicativo personalizadas que ocorreram durante os últimos 25 dias.
  
  ![segments4][38]

Para usar o segmento de maneira ideal, você deve ter feito uma integração personalizada do SDK no seu aplicativo com um plano de marcação de marcas “Informações de aplicativo".
Em seguida, vá para a home inicial da interface, selecione o aplicativo desejado e clique na seção "Segmentos".

1. Selecione a seção "Segmentos".
2. Clique no botão “Novo segmento" para criar um novo segmento.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Crie um segmento simples com base nas informações de "Sessão".
Crie um segmento de todos os usuários finais que usaram o seu aplicativo pelo menos 50 vezes na última semana. A partir daí, localize apenas os usuários finais que passaram pelo menos 30 segundos no seu aplicativo por sessão. Isso mostrará todos os usuários finais que tiveram uma experiência positiva em seu aplicativo. Em seguida, o segmento criado poderia ser usado para enviar uma notificação para esses usuários finais pedindo para avaliarem seu aplicativo na loja.

 ![segments5][39]

1. Nomeie seu segmento para localizá-lo rapidamente na lista de "Segmento".
2. Clique no botão "Criar".
   
   ![segments6][40]

Selecione a sessão.

 ![segments7][41]

1. Selecione o período de “Última semana".
2. Clique em Avançar.
   
   ![segments8][42]
3. Selecione o operador que é relevante na lista: =; ≥, ≤.
4. Insira o número desejado.
5. Selecione a ocorrência que você deseja. 
6. Clique em Avançar.
   Este exemplo está definido de modo que na última semana corresponda aos usuários que fizeram pelo menos 50 sessões.
   
   ![segments9][43]

Para a segmentação de sessão, você pode escolher a duração da sessão como um critério.

1. Selecione o operador na lista.
2. Forneça a duração por sessão.
3. Clique em Avançar.
   Neste exemplo, diz que de todas as sessões que foram segmentadas na seção de ocorrência, selecione apenas os usuários que gastaram mais de 30 segundos por sessão.
   
   ![segments10][44]

Nomeie o critério para recuperá-la no funil completo e clique em Concluir.

 ![segments11][45]

Quando você terminar de configurar o critério, ele aparecerá no funil do segmento.
Como um segmento é baseado em dados de análise, os segmentos são calculados uma vez por dia.
Neste exemplo, 47,7% do total dos usuários finais correspondente ao critério. Eles devem ser os usuários que tiveram uma boa experiência e provavelmente darão uma classificação alta se você lhes enviar uma notificação pedindo para avaliarem o aplicativo na loja.

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

