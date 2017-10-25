---
title: "Azure Mobile Engagement - Interface do usuário - Reach"
description: "Como entrar em contato com os usuários do seu aplicativo com notificações por push usando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ce30456e41ff1a2f4824bcb64246ee115fdd1ef7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Como entrar em contato com os usuários do seu aplicativo com notificações por push
Este artigo descreve a guia **ALCANCE** do portal do **Mobile Engagement**. Você usa o portal do **Mobile Engagement** para monitorar e gerenciar seus aplicativos móveis. Observe que, para começar a usar o portal, primeiro é preciso criar uma conta do **Azure Mobile Engagement** . Para obter mais informações, veja [Criar uma conta do Azure Mobile Engagement](mobile-engagement-create.md).

A seção Reach da interface do usuário é a ferramenta de gerenciamento de campanha Push onde você pode criar/editar/ativar/finalizar/monitorar e obter estatísticas sobre campanhas de notificação de envio e os recursos que também podem ser acessados via a API do Reach (e alguns elementos do nível baixo API de envio). Lembre-se de que, se você estiver usando as APIs ou a interface do usuário, você precisará integrar o Azure Mobile Engagement e o Reach em seu aplicativo em cada plataforma com o SDK antes que você possa usar as campanhas Reach.

> [!NOTE]
> Muitas seções da interface do usuário do portal do **Mobile Engagement** contêm o botão **MOSTRAR AJUDA**. Pressione este botão para obter mais informações contextuais sobre uma seção.
> 
> 

## <a name="four-types-of-push-notifications"></a>Quatro tipos de notificações por Push
1. Anúncios - permitem que sejam enviadas mensagens de anúncio para usuários que redirecioná-las para outro local dentro de seu aplicativo ou para enviá-las para uma página da Web ou armazenamento fora de seu aplicativo. 
2. Pesquisas - permitem coletar informações dos usuários finais ao fazer perguntas a eles.
3. Envio de dados - permite enviar um arquivo de dados binário ou base64. As informações contidas em um envio de dados são enviadas ao seu aplicativo para modificar a experiência atual dos usuários em seu aplicativo. Seu aplicativo precisa ser capaz de processar os dados em um envio de dados.

## <a name="campaign-details"></a>Detalhes da campanha
Você pode editar, clonar, excluir ou ativar campanhas que ainda não foram ativadas passando o mouse sobre os nomes ou você pode clicar para abri-las. Você pode clonar campanhas que já foram ativadas passando o mouse sobre os nomes ou você pode clicar para abri-las. No entanto, você não pode alterar uma campanha depois que ela tiver sido ativada.

![Reach1][18]

## <a name="reach-feedback"></a>Comentários do Reach
Clique em **Estatísticas** para ver os detalhes de uma Campanha de alcance. O modo de exibição **Simples** fornece uma representação visual na forma de um gráfico de barras da coluna sobre o que aconteceu após a ativação de uma campanha. O modo de exibição **Avançado** fornece detalhes mais granulares sobre a campanha por push. Esses detalhes não estarão disponíveis se você estiver enviando uma campanha de teste, ou seja, um envio por push para um dispositivo de teste. Veja como você deve interpretar esses detalhes:

1. **Enviada por push** - Essa opção especifica o número de mensagens enviadas por push para os dispositivos. Esse número dependerá do público-alvo especificado durante a criação da campanha de push. Se você não especificar qualquer público-alvo, esse envio por push ocorrerá para todos os dispositivos registrados. Como todos os outros serviços de envio por push, não enviamos diretamente por push aos dispositivos, em vez disso, os enviamos para as respectivas plataformas específicas de Serviços de notificação por push (PNS - GCM/APNS/WNS) para que elas possam oferecer as notificações aos dispositivos. 
2. **Entregues** - Especifica o número de mensagens que são entregues com êxito pelo PNS ao dispositivo e confirmadas como recebidas pelo SDK do Mobile Engagement. 
   
   *Motivos para a contagem Entregues ser menor do que a contagem de Enviada por push:*
   
   1. Se o usuário tiver desinstalado o aplicativo a partir do dispositivo, mas o PNS não souber sobre isso no momento do envio por push para o PNS, a mensagem será descartada.
   2. Se o dispositivo tiver o aplicativo, mas os próprios dispositivos ficarem offline por longos períodos, o PNS não conseguirá entregar a mensagem para o dispositivo. 
   3. Se a mensagem for entregue ao dispositivo, mas o SDK do Mobile Engagement no aplicativo não reconhecer o conteúdo da mensagem, ele descartará essa mensagem. Isso pode ocorrer se a personalização da notificação no aplicativo gerar uma exceção que possamos capturar no SDK e descartar a mensagem. Isso também pode ocorrer se o aplicativo no dispositivo estiver usando uma versão do SDK do Mobile Engagement que não é capaz de entender a versão mais recente da mensagem de envio por push enviada da plataforma. Porém, isso ocorre apenas quando o aplicativo tiver sido atualizado após o envio da notificação a partir da plataforma do serviço. A guia **Avançado** informará quantas mensagens foram descartadas. 
   4. Em dispositivos iOS, às vezes as mensagens não serão entregues se o dispositivo estiver com bateria fraca ou se o aplicativo estiver consumindo uma quantidade significativa de energia durante o processamento de notificações remotas. Essa é uma limitação dos dispositivos iOS.   
3. **Exibidas** - especifica o número de mensagens mostradas com êxito para o usuário do aplicativo no dispositivo na forma de um sistema de notificação de push/fora do aplicativo no centro de notificação ou de uma notificação no aplicativo dentro do aplicativo móvel.  A guia **Avançado** informará quantas foram notificações do sistema e quantas foram notificações no aplicativo. 
   
   *Motivos para a contagem Exibidas ser menor que a contagem Entregues (aguardando exibição)*
   
   1. Se a campanha de notificação tinha uma data de término nela, é possível que a notificação tenha sido entregue, mas na hora de abri-la e exibi-la ao usuário, ela já estava expirada e, portanto, nunca foi exibida.   
   2. Se a notificação for uma notificação interna do aplicativo, ela só será exibida quando o usuário do aplicativo abrir o aplicativo. Quando o usuário do aplicativo não tiver aberto o aplicativo, o SDK relatará que a notificação foi entregue mas que ainda não foi exibida até o aplicativo ser aberto. 
   3. Se a notificação for uma notificação interna do aplicativo e estiver configurada para ser exibida em uma atividade/tela específica, ela também será relatada como entregue, mas não será entregue até que o usuário abra o aplicativo em uma tela específica. 
4. **Interações do usuário** - especifica o número de mensagens com as quais o usuário do aplicativo interagiu e incluirá as mensagens que foram acionadas ou encerradas. 
   
   * *O usuário do aplicativo pode tomar uma ação com relação a uma notificação de uma das seguintes maneiras:*
     
     1. Se a notificação for uma notificação do sistema/fora do aplicativo ou uma notificação no aplicativo enviada como somente notificação, o usuário do aplicativo clicará na notificação.
     2. Se a notificação for uma notificação no aplicativo com um texto, modo de exibição da Web ou pesquisa, o usuário do aplicativo clicará no botão Ação na notificação.
     3. Se a notificação for uma notificação no aplicativo com um modo de exibição na Web, o usuário do aplicativo clicará em uma URL no modo de exibição da Web [somente Android]
   * *O usuário do aplicativo pode sair de uma notificação de qualquer uma das seguintes maneiras:*
     
     1. Clicando no botão Fechar diretamente na notificação. 
     2. Deslizando para fechar ou excluindo a notificação. 
     3. Geralmente, as notificações no aplicativo com o conteúdo de texto/Web e pesquisas são exibidas para o usuário do aplicativo em um processo de duas etapas. Primeiro eles recebem uma notificação e, quando clicam nela, veem o conteúdo de texto/Web/pesquisa subsequente. O usuário do aplicativo pode sair de uma notificação usando qualquer uma dessas etapas e os detalhes no modo de exibição Avançado captura isso. 
5. **Acionadas** - especifica o número de mensagens que foram acionadas explicitamente pelo usuário do aplicativo. Esse é o número mais interessante, pos informa quantos usuários do aplicativo ficaram interessados pela mensagem que você enviou com a notificação. 

> [!NOTE]
> Nas plataformas iOS e Windows, se o usuário estiver com o aplicativo aberto ,e a campanha for do tipo "Qualquer hora", é possível que as notificações fora do aplicativo e no aplicativo sejam exibidas ao mesmo tempo. Isso pode causar uma contagem de Exibidas maior do que de Entregues. Se o usuário interagir ou executar ações na notificação, até mesmo a contagem de Interações do usuário/Acionadas pode ser maior do que de Entregues. 
> 
> 

![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

