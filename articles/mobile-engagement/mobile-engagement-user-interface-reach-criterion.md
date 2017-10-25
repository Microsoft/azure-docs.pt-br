---
title: "Interface do usuário do Azure Mobile Engagement - Alcance - Critério"
description: "Saiba como usar critérios de direcionamento para campanhas de envio por push para selecionar um subconjunto de seus usuários usando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 803b44721d0ab1ac7b5a8074e18857fc57adb724
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Como usar critérios de direcionamento para enviar por push campanhas para selecionar um subconjunto de seus usuários
Direcionar seu público-alvo por critérios específicos com o botão "Novos Critérios" é um dos conceitos mais poderosos no Azure Mobile Engagement que ajuda a enviar importantes notificações por push que os clientes responderão em vez de enviar spam a todos. Você pode limitar seu público-alvo com base em critérios padrão e simular envios por push para determinar quantas pessoas receberão a notificação.

**Consulte também:**

* [Documentação da Interface do Usuário ‑ Alcance ‑ Nova Campanha por Push][Link 27]

## <a name="audience-criteria-can-include"></a>Os critérios de público-alvo podem incluir:
* **Aspectos técnicos: **é possível direcionar com base nas mesmas informações técnicas vistas nas seções Análise e Monitoramento. **Veja também:** [Documentação da Interface do Usuário ‑ Análise][Link 15], [Documentação da Interface do Usuário ‑ Monitoramento][Link 16]
* **Local:** os aplicativos que usam "Relatórios de localização em tempo real” com isolamento geográfico podem usar a localização geográfica como um critério para direcionar para um público-alvo a partir da localização do GPS. A chamada de "Relatórios de Localização de Área Lenta" também pode ser usada para direcionar para um público-alvo da localização do celular (“Relatórios de localização em tempo real” e “Relatórios de Localização de Área Lenta” devem ser ativados no SDK). **Consulte também:** [Documentação do SDK ‑ iOS ‑ Integração][Link 5], [Documentação do SDK ‑ Android ‑ Integração][Link 5]
* **Comentários do Alcance:** você pode direcionar para o público-alvo com base nos comentários sobre notificações anteriores de alcance e nos comentários de Anúncios, Pesquisas e Envio de Dados por Push. Isso permite direcionar melhor para seu público-alvo depois de duas ou três campanhas de alcance em relação ao que poderia ser feito da primeira vez. Também pode ser usado para filtrar os usuários que já receberam uma notificação com conteúdo semelhante, definindo uma campanha para NÃO ser enviada aos usuários que já receberam uma campanha anterior específica. Você pode até mesmo excluir os usuários incluídos em uma campanha específica que ainda esteja ativa do recebimento de novos envios por Push. **Consulte também:** [Documentação da Interface do Usuário ‑ Alcance ‑ Enviar Conteúdo por Push][Link 29]
* **Instalar Rastreamento:** você pode rastrear informações com base no local onde seus usuários instalaram o seu Aplicativo. **Consulte também:** [Documentação da interface do usuário ‑ Configurações][Link 20]
* **Perfil do Usuário:** você pode direcionar com base nas informações de usuário padrão e pode direcionar com base em informações do aplicativo personalizado que você criou. Isso inclui usuários que estão conectados no momento e os usuários que responderam perguntas específicas que você fez a eles para definir o próprio aplicativo, em vez de apenas a forma como eles responderam a campanhas anteriores. Todas as Informações do Aplicativo definidas para seu aplicativo são mostradas nesta lista.
* Segmentos: também será possível direcionar com base em segmentos que você tenha criado com base no comportamento de um usuário específico com diversos critérios. Todos os segmentos definidos para o seu aplicativo aparecem nessa lista. **Consulte também:** [Documentação da Interface do Usuário ‑ Segmentos][Link 18]
* **Informações do Aplicativo:** as Marcas de Informações de Aplicativo Personalizadas podem ser criadas nas “Configurações" para controlar o comportamento do usuário. **Consulte também:** [Documentação da interface do usuário ‑ Configurações][Link 20]

## <a name="example"></a>Exemplo:
Se você quiser enviar por push um anúncio apenas para o subconjunto de seus usuários que executaram uma ação no aplicativo de compra.

1. Vá para a página de configurações do aplicativo, selecione o menu "Informações do Aplicativo" e selecione "Nova informação do aplicativo"
2. Registre uma nova informação de aplicativo booliano chamada "inAppPurchase"
3. Defina as informações de aplicativo como "verdadeiro" quando o usuário executar com êxito uma compra no aplicativo (usando a função sendAppInfo("inAppPurchase",...))
4. Se você não quiser fazer isso no seu aplicativo, poderá fazer no seu back-end usando a API do dispositivo)
5. Em seguida, bastará criar o seu anúncio, com um critério limitando seu público-alvo aos usuários que tenham "inAppPurchase" definido como "verdadeiro")

> [!NOTE]
> O direcionamento com base em critérios diferentes das marcas de informações de aplicativo requer que o Azure Mobile Engagement colete informações dos dispositivos de seus usuários antes de efetuar o envio e, portanto, pode causar um atraso. As opções de configuração de envio por push complexas (como atualização de notificações) também podem atrasar o envio por push. Usar uma campanha "monoestável" da API de envio por Push é o método de envio mais rápido no Azure Mobile Engagement. Usar as marcas de informações de aplicativo apenas como critérios de envio por push para uma campanha de Alcance (a partir da API de Alcance ou da interface do usuário) é o próximo método mais rápido, já que as marcas de informações do aplicativo estão armazenadas no lado do servidor. Usar outros critérios de direcionamento para uma campanha de envio é o método mais flexível, porém o método mais lento do envio por push, já que o Azure Mobile Engagement tem que consultar os dispositivos para enviar a campanha.

![Reach-Criterion1][29] 

## <a name="criterion-options-apply-to"></a>As Opções de Critério se Aplicam a:
* **Técnicos**     
* Nome do firmware: nome do firmware
* Versão do firmware: versão do firmware
* Modelo do dispositivo: modelo do dispositivo
* Fabricante do dispositivo: fabricante do dispositivo
* Versão do aplicativo: versão do aplicativo
* Nome da operadora: nome da operadora, indefinido
* País da operadora: país da operadora, indefinido
* Tipo de rede: tipo de rede
* Localidade: localidade
* Tamanho da tela: tamanho da tela
* **Localidade**      
* Última área conhecida: país, região, localidade
* Isolamento geográfico em tempo real: lista de POIs (Nome, Ações), POI circular (Nome, Latitude, Longitude, Raio em metros)
* **Comentários de alcance**     
* Comentários de notificação: anúncio, comentários
* Comentários da pesquisa: pesquisa, comentários
* Comentários de resposta da pesquisa: comentários de resposta da pesquisa, pergunta, alternativa
* Comentários de Push de Dados: push de dados, comentários
* **Instalar o Rastreamento**     
* Loja: loja, indefinido
* Fonte: fonte, indefinido
* **Perfil do usuário**     
* Sexo: masculino ou feminino, indefinido
* Data de nascimento: operador, data, indefinido
* Aceitação: verdadeiro ou falso, indefinido
* **Informações do aplicativo**      
* Cadeia de caracteres: cadeia de caracteres, indefinido
* Data: operador, data, indefinido
* Inteiro: operador, número, indefinido
* Booliano: verdadeiro ou falso, indefinido
* **Segmento**    
* Nome dos segmentos (da lista suspensa), Exclusão (usuários de destino que não fazem parte deste segmento).

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

