---
title: "Interface do Usuário do Azure Mobile Engagement - Alcance - Conteúdo"
description: "Saiba como gerenciar o conteúdo exclusivo dos diferentes tipos de campanhas de notificação por push no Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: add64f06-43c9-475c-8722-51cd00bb844b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3741a43b74af5846e95e42d8a7b533621e780f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Como gerenciar o conteúdo exclusivo dos diferentes tipos de campanhas de notificação por push
Você pode usar a seção de conteúdo de uma nova campanha de alcance para modificar o conteúdo de anúncios, pesquisas, envia dados e lado a lado (apenas no Windows Phone). A configuração de conteúdo de campanhas de Envio por Push é específica para o tipo de campanha. 

### <a name="content-types"></a>Tipos de conteúdo:
* Anúncios
* Pesquisas
* Envios de dados por push
* Blocos (apenas no Windows Phone)

## <a name="content-of-announcements"></a>Conteúdo de anúncios
 ![Reach-Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Escolha o tipo de anúncio:
* Somente notificação: é uma notificação simples padrão. Isso significa que se um usuário clicar nela, nenhum modo de exibição adicional será exibido, mas apenas a ação associada a ela ocorrerá.
* Anúncio de texto: é uma notificação que leva o usuário a dar uma olhada em uma exibição de texto.
* Anúncio pela: é uma notificação que leva o usuário a dar uma olhada em uma exibição da Web.

### <a name="see-also"></a>Consulte também
* [Alcance ‑ Instruções ‑ Anúncios][Link 3] 

### <a name="about-web-view-announcements"></a>Sobre os anúncios de exibição na Web:
As ocorrências do padrão "{deviceid}" no código HTML ou no código JavaScript que você fornecer aqui serão substituídas automaticamente pelo identificador do dispositivo que estiver exibindo o anúncio. Essa é uma maneira fácil de recuperar identificadores de dispositivos do Azure Mobile Engagement em um serviço Web externo hospedado no seu back office.
Se você quiser criar uma exibição da Web em tela inteira (sem os botões Ação e Sair padrão que fornecemos), poderá usar as seguintes funções do código de JavaScript da exibição da Web do anúncio: 

* executar a ação de anúncio: ReachContent.actionContent()
* sair do anúncio: ReachContent.exitContent()

### <a name="choose-your-action"></a>Escolha a ação:
### <a name="about-action-urls"></a>Sobre as URLs de ação:
Qualquer URL que possa ser interpretada pelo sistema operacional de destino do dispositivo pode ser usada como uma URL de ação.
Qualquer URL dedicada que possa dar suporte a seu aplicativo (por exemplo, para fazer com que os usuários mudem para uma determinada tela) também pode ser usada como uma URL de ação.
Cada ocorrência do padrão {deviceid} é substituída automaticamente pelo identificador do dispositivo de execução da ação. Esta é uma maneira fácil de recuperar identificadores de dispositivos do Azure Mobile Engagement por meio de um serviço Web externo hospedado no seu back office.

* **Ações do Android + iOS**
  * Abrir uma página Web
  * http://\[web-site-domain\] 
  * Exemplo:http://www.azure.com
  * Enviar um email
  * mailto:\[e-mail-recipient\]?subject=\[subject\]&body=\[message\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * Enviar um SMS
  * sms:\[número-de-telefone\] 
  * Exemplo:sms:2125551212
  * Discar um número de telefone
  * tel:\[número-de-telefone\] 
  * Exemplo:tel:2125551212
* **Somente ações do Android**
  * Baixar um aplicativo da Play Store
  * market://details?id=\[app package\] 
  * Exemplo:market://details?id=com.microsoft.office.word
  * Iniciar uma pesquisa localizada geograficamente
  * geo:0,0?q=\[search query\] 
  * Exemplo:geo:0,0?q=starbucks,paris
* **Somente ações do iOS**
  * Baixar um aplicativo da App Store
  * http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
  * Exemplo:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
  * Ações do Windows
  * Abrir uma página Web
  * http://\[web-site-domain\] 
  * Exemplo:http://www.azure.com
  * Enviar um email
  * mailto:\[e-mail-recipient\]?subject=\[subject\]&body=\[message\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * Enviar um SMS (necessário o aplicativo da Skype Store)
  * sms:\[número-de-telefone\] 
  * Exemplo:sms:2125551212
  * Discar um número de telefone (necessário o aplicativo da Skype Store)
  * tel:\[número-de-telefone\] 
  * Exemplo:tel:2125551212
  * Baixar um aplicativo da Play Store
  * ms-windows-store:PDP?PFN=\[app package ID\] 
  * Exemplo: ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
  * Iniciar uma pesquisa do Bing Mapas
  * bingmaps:?q=\[consulta de pesquisa\] 
  * Exemplo:bingmaps:? q=starbucks,paris
  * Usar um esquema personalizado
  * \[esquema personalizado\]://\[parâmetros do esquema personalizado\] 
  * Exemplo:myCustomProtocol://myCustomParams
  * Usar um pacote de dados (necessário o aplicativo da Store para leitura de extensão)
  * \[pasta\]\[dados\].\[extensão\] 
  * Example:myfolderdata.txt

### <a name="build-a-tracking-url"></a>Criar uma URL de rastreamento:
* Consulte a seção "Configurações" da <UI Documentation> para obter instruções sobre a criação de uma URL de rastreamento que permitirá que os usuários baixem um de seus outros aplicativos.

### <a name="define-the-texts-of-your-announcement"></a>Definir os textos do anúncio
Preencha o título, o conteúdo e os textos de botão do seu anúncio. Você pode direcionar para um público-alvo de uma campanha futura com base nos comentários de alcance de como os usuários responderam a essa campanha. O público-alvo pode ser baseado nos comentários sobre essa campanha, tenha sido ela apenas enviada por push, respondida, acionada ou encerrada.

### <a name="see-also"></a>Consulte também
* [Documentação da interface do usuário – Alcance – Novo Critério de Envio por Push][Link 28]

## <a name="content-of-polls"></a>Conteúdo das pesquisas
![Reach-Content2][31] 

Preencha o título, a descrição e os textos de botão do seu anúncio. Em seguida, adicione as perguntas e opções de respostas para as suas perguntas.
Você pode direcionar para um público-alvo de uma campanha futura com base nos comentários de alcance de como os usuários responderam a essa campanha. O público-alvo direcionado pode ser baseado no fato desta campanha ter sido apenas enviada por push, respondida, acionada ou encerrada. O público-alvo também pode ser baseado nos comentários de resposta de pesquisa, onde a pergunta e resposta de opção são usadas como critérios.

### <a name="see-also"></a>Consulte também
* [Documentação da interface do usuário – Alcance – Novo Critério de Envio por Push][Link 28]

## <a name="content-of-data-pushes"></a>Conteúdo de envio de dados por push
![Reach-Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Escolha o tipo de dados:
* Texto
* Dados binários
* Dados Base64

### <a name="define-the-content-of-your-data"></a>Defina o conteúdo de seus dados
* Se você optou por enviar dados de texto, copie e cole o texto na caixa "conteúdo".
* Se você optou por enviar dados binários ou base64, use o botão "carregar o arquivo" para carregar o seu arquivo.
* Você pode direcionar para um público-alvo de uma campanha futura com base nos comentários de alcance de como os usuários responderam a essa campanha. O público-alvo direcionado pode ser baseado no fato desta campanha ter sido apenas enviada por push, respondida, acionada ou encerrada.

### <a name="see-also"></a>Consulte também
* [Documentação da interface do usuário – Alcance – Novo Critério de Envio por Push][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Conteúdo de blocos (somente para o Windows Phone)
![Reach-Content4][33]

### <a name="define-the-content-of-your-tile"></a>Definir o conteúdo do seu bloco
A carga de bloco é o texto a ser exibido no bloco do seu aplicativo em dispositivos Windows Phone.
Um envio de lado a lado é a versão do serviço de Notificação por Push da Microsoft (MPNS) um envio por push nativo para o Windows Phone. O tipo de envio por push do bloco é o único tipo de envio que não tem uma resposta e, portanto, o público-alvo de futuras campanhas não pode ser criado com os resultados de uma campanha de envio por push do bloco. 

### <a name="see-also"></a>Consulte também
* [Documentação da API – API de Alcance – Envio por Push Nativo][Link 4]

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

