---
title: "Interface de usuário do Azure Mobile Engagement - Página inicial"
description: Saiba como gerenciar seus aplicativos e projetos existentes usando o Azure Mobile Engagement
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0f15cb975f57f6f5cab12d5118ff50a6fab14388
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-your-existing-application-and-projects"></a>Como gerenciar seus aplicativos e projetos existentes
Este artigo descreve a página **Início** do portal do **Mobile Engagement**. Você usa o portal do **Mobile Engagement** para monitorar e gerenciar seus aplicativos móveis. Observe que, para começar a usar o portal, primeiro é preciso criar uma conta do **Azure Mobile Engagement** . 

Para acessar a página inicial, clique em **Inicial** na parte superior esquerda da página. Ela contém a lista de todos os aplicativos que fazem parte da coleção escolhida. Nesta página tem-se uma visão geral de seus aplicativos.

A página inicial também contém todos os seus projetos que podem incluir quaisquer aplicativos que estejam em sua conta. Observe que qualquer pessoa pode acessar a página inicial da interface do usuário ao criar uma conta, mas você precisa conceder permissão aos outros usuários para que tenham acesso aos seus aplicativos personalizados em **Meus projetos**.

Você também pode exibir o gráfico de comparação para os aplicativos selecionados. Ou optar por exibir o gráfico de comparação para os aplicativos selecionados em um projeto.

![Home1][0]

## <a name="my-applications"></a>Meus aplicativos
A visão geral rápida dos seus aplicativos permite selecionar quais aplicativos você gostaria de abrir para exibir a faixa de opções detalhada. Você pode clicar no nome do seu aplicativo para retornar para o local da faixa de opções visitado recentemente em seu aplicativo ou clicar no ícone de engrenagem para ir diretamente para a página “Configurações” do seu aplicativo. Você pode pesquisar, filtrar ou classificar as informações exibidas nas tabelas de aplicativos. Você também pode arrastar e soltar os cabeçalhos de coluna para alterar a ordem.

Entre outras coisas, a visão geral dos seus aplicativos inclui:

* **Nova tendência de usuários**: evolução de novos usuários nas últimas duas semanas.
* **Usuários ativos**: número de usuários ativos nos últimos 30 dias.
* **Tendência de usuários ativos**: evolução de usuários ativos nas últimas duas semanas.
* **Sessões**: uma sessão é um uso do aplicativo executado por um usuário, desde o momento em que ele começa a usá-lo até quando ele para.
* **Tendências de sessão**: evolução de sessões nas duas últimas semanas.

Após clicar em um aplicativo, você pode começar a monitorar e gerenciar seus aplicativos por meio da interface do usuário. Por exemplo:    

* [Monitorar dados em tempo real sobre o seu aplicativo](mobile-engagement-user-interface-monitor.md)
* [Analisar dados históricos sobre seu aplicativo](mobile-engagement-user-interface-analytics.md)
* [Criar e gerenciar segmentos de usuários para identificar padrões de uso](mobile-engagement-user-interface-segments.md)
* [Entrar em contato com os usuários do seu aplicativo com notificações por push](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>Meus projetos
Você pode usar os projetos para agrupar seus aplicativos e conceder permissões a outros usuários para que acessem seus aplicativos. Conceder permissões a outros usuários disponibilizando o endereço de e-mail. O botão de **Novo projeto** permite que você crie um novo projeto, inserindo apenas um “nome” e uma “descrição” do novo projeto. Quando um projeto é criado, você pode clicar no nome do projeto para editar o nome e a descrição do produto e selecionar todos os aplicativos que você deseja ver neste projeto.

![Home6][60]

As funções incluem:

* **Visualizador**: Um Visualizador é um usuário que só pode visualizar os aplicativos associados a um Projeto. Um Visualizador pode acessar os dados da análise e monitoramento e examinar os resultados de Reach. Um Visualizador não pode alterar qualquer informação, nem gerenciar Aplicativos ou Usuários. Um Visualizador não pode criar ou alterar o status da campanha Reach.
* **Desenvolvedor**: Um Desenvolvedor é um usuário que pode fazer tudo que um Visualizador pode fazer, bem como gerenciar Aplicativos. Um Desenvolvedor pode ativar e desativar aplicativos, alterar informações de aplicativos (como o pacote e assinatura) e criar campanhas Reach. Um Desenvolvedor não pode gerenciar Usuários.
* **Administrador**: Um Administrador é um usuário que pode fazer tudo o que um Desenvolvedor pode fazer, bem como gerenciar Usuários. Administrador: Um Administrador pode convidar usuários para ingressar em um projeto, pode alterar as funções de usuário e pode alterar informações do projeto. Permissões de nível de aplicativo também podem ser definidas em “configurações”.

Clique em um projeto para exibir todos os aplicativos que fazem parte deste projeto. A imagem a seguir exibe o gráfico de comparação para os aplicativos selecionados.

![Home2][3]

## <a name="see-also"></a>Consulte também
* [Conceitos][Link 6]
* [Serviço do Guia de Solução de Problemas][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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
