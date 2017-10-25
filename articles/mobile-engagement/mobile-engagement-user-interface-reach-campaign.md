---
title: "Interface do usuário do Azure Mobile Engagement - Alcance - Campanha"
description: "Saiba como criar e gerenciar campanhas de notificação por push usando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fc88db8db11d1ed12fa95c2087c9a32b21bf4de5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Como criar e gerenciar campanhas de notificação por push
Você pode usar a seção Alcance da interface do usuário para criar uma nova campanha de envio com uma fórmula complexa, fornecendo todas as informações necessárias para enviar uma notificação por push. As opções de uma campanha de Envio por push variam um pouco dependendo dos quatro tipos de campanha: Anúncios, Pesquisas, Envio de Dados por Push e Blocos (apenas Windows Phone).

### <a name="option-applies-to"></a>As Opções se Aplicam a:
* Idiomas: todos (Anúncios, Pesquisas, Envio de Dados por Push, Blocos)
* Campanha: todos (Anúncios, Pesquisas, Envio de Dados por Push, Blocos)
* Notificação: anúncios, pesquisas
* Conteúdo: exclusivo para cada tipo de campanha
* Público-alvo: todos (Anúncios, Pesquisas, Envio de Dados por Push, Blocos)
* Período: anúncios, pesquisas, blocos
* Teste: todos (Anúncios, Pesquisas, Envio de Dados por Push, Blocos)

![Reach-Campaign1][20]

## <a name="languages"></a>Idiomas
Você pode usar o menu suspenso de idiomas para enviar uma versão diferente do seu envio por Push para dispositivos que estejam configurados para usar diferentes idiomas. Por padrão, todos os dispositivos receberão o mesmo envio por Push, independentemente do idioma definido para uso. Os usuários com seus dispositivos definidos para um idioma diferente receberão a versão de idioma padrão do envio por Push. Muitas das opções de campanha de envio permitem que você especifique um conteúdo alternativo para cada um dos idiomas adicionais selecionados. 

![Reach-Campaign2][21]

### <a name="language-differences-apply-to"></a>As diferenças de idioma se aplicam a:
* Idiomas: idiomas exclusivos podem ser selecionados além do idioma padrão
* Campanha: a mesma para todos os idiomas
* Notificação: exclusiva para cada idioma além do idioma padrão
* Conteúdo: exclusivo para cada idioma além do idioma padrão
* Público-alvo: pode ser filtrado por um critério de idioma separado
* Período: o mesmo para todos os idiomas
* Teste: pode ser enviado para cada idioma por vez

### <a name="supported-languages"></a>Idiomas com suporte:
* Árabe (ar) 
* Búlgaro (bg) 
* Catalão (ca) 
* Chinês (zh) 
* Croata (h) 
* Tcheco (cs) 
* Dinamarquês (da) 
* Holandês (nl) 
* Inglês (en) 
* Finlandês (fi) 
* Francês (fr) 
* Alemão (de) 
* Grego (el) 
* Hebraico (he) 
* Híndi (Olá) 
* Húngaro (hu) 
* Indonésio (id) 
* Italiano (it) 
* Japonês (ja) 
* Coreano (ko) 
* Letão (lv) 
* Lituano (lt) 
* Malaio (macroidioma) (ms) 
* Norueguês Bokmål (nb) 
* Polonês (pl) 
* Português (Portugal) 
* Romeno (ro) 
* Russo (ru) 
* Sérvio (sr) 
* Eslovaco (discos) 
* Esloveno (sl) 
* Espanhol (es) 
* Sueco (sv) 
* Tagalo (NFA) 
* Tailandês (th) 
* Turco (tr) 
* Ucraniano (uk) 
* Vietnamita (vi) 

## <a name="campaign"></a>Campanha
Você pode usar a seção de campanha para definir o nome e a categoria da sua campanha, bem como se você planeja ignorar a seção público de uma campanha de envio e, em vez disso, enviar essa campanha por meio da API do Reach (e alguns elementos com o API de envio de nível baixo). As categorias podem ser usadas com um modelo de notificação personalizado para notificações do controle no aplicativo com base nas configurações predefinidas. Você pode obter uma lista existente de "categorias" por meio da API do Reach.

> [!WARNING]
> Se você usar a opção "Ignorar Público, o push será enviado para os usuários por meio da API" na seção "Campanha" de uma campanha Reach, a campanha NÃO será enviada automaticamente e você precisará enviá-la manualmente através da API do Reach.

![Reach-Campaign3][22]

### <a name="option-applies-to"></a>As Opções se Aplicam a:
* Nome: todos
* Categoria: anúncios, pesquisas
* Ignorar Público-alvo, o envio por push será feito aos usuários por meio da API: todos

## <a name="notification"></a>Notificação
Você pode usar a seção Notificação para definir as configurações básicas para seu envio por push, incluindo: o título do Envio por push, a mensagem, uma imagem no aplicativo ou se elas são dispensáveis. Muitas configurações de notificação são específicas para a plataforma do dispositivo. Você pode selecionar se o envio por push será feito “no aplicativo” ou “fora do aplicativo” ou ambos. (Lembre-se de que os usuários podem “aceitar" ou “recusar" envios por push de “fora do aplicativo" com o nível de Sistema Operacional em seus dispositivos e o Azure Mobile Engagement não poderá substituir essa configuração. Lembre-se também de que a API de Alcance manipula o envio por push "no aplicativo" e "fora do aplicativo". A API de Push pode ser usada para lidar com envios por push de "fora do aplicativo" também.) Os envios por push podem ser personalizados com imagens ou conteúdo HTML, incluindo links de vínculos profundos fora de seu aplicativo ou em outro local em seu aplicativo (SDK do Android 2.1.0 ou categorias de finalidade posteriores necessárias). Você pode alterar o ícone ou o emblema do iOS e enviar o conteúdo da web ou de texto (um pop-up com conteúdo html, link de URL para outro local dentro ou fora do aplicativo). Você também pode fazer com que os dispositivos Android toquem ou vibrem com o envio por Push. (Lembre-se de que você precisará das permissões corretas do SDK no seu arquivo de manifesto do Android para que o dispositivo toque ou vibre). Atualmente, não há nenhum padrão no setor para tamanhos de “Foto Grande" do Android, haja visto que os tamanhos das telas são diferentes em cada dispositivo, porém imagens de 400 x 100 funciona bem em quase todos os tamanhos de tela.

### <a name="delivery-types"></a>Tipos de entrega:
* Somente fora do aplicativo: a notificação será enviada quando o usuário não estiver usando o aplicativo.
* A notificação do fora aplicativo requer apenas um certificado da Apple ou do Google (certificado APNS ou GCM).
* Somente no aplicativo: a notificação aparecerá somente quando o aplicativo estiver sendo executado.
* A notificação usa o sistema de entrega Capptain para alcançar o usuário. Você pode personalizar totalmente o visual do layout/exibição de seu envio por push.
* A qualquer momento: essa opção garante que você envie uma notificação tanto quando o aplicativo estiver em execução ou não.

![Reach-Campaign4][23]

### <a name="option-applies-to"></a>As Opções se Aplicam a:
* Notificação: anúncios, pesquisas

## <a name="content"></a>Conteúdo
Você pode usar a seção Conteúdo para modificar o conteúdo de Anúncios, Pesquisas, Envio de Dados por Push e Blocos (somente no Windows Phone). A configuração de conteúdo de campanhas de envio é específica para o tipo de campanha. 

### <a name="see-also"></a>Consulte também
* [Documentação da Interface do Usuário ‑ Alcance ‑ Enviar conteúdo por push][Link 29]

![Reach-Campaign5][24]

## <a name="audience"></a>Público-alvo
Você pode usar a seção Público-alvo para definir uma lista padrão de itens para limitar sua campanha ou limitar sua campanha com base em critérios personalizados. O conjunto de opções padrão para limitar seu público permite que você envie para usuários novos ou antigos ou somente a usuários de envio por push nativo. Você também pode definir uma cota para limitar o número de usuários que receberão o envio por push. Você pode Editar manualmente a expressão de como sua campanha é filtrada para incluir um ou mais critérios para os usuários de destino. Você pode digitar manualmente uma expressão de público-alvo. Essa expressão deve definir explicitamente a relação entre os critérios. Um critério é descrito por um identificador que deve começar com uma letra maiúscula e não pode conter espaços. A relação entre os critérios pode ser descrita utilizando os operadores ‘e’, ‘ou’, ‘não’, bem como '(', ')'. Exemplo: "Critérion1 ou (Critérion1 e não Critérion2)".

> [!NOTE]
> Com um grande público incluído em campanhas, a verificação de direcionamento do lado do servidor pode ser lenta, especialmente se você tentar iniciar várias campanhas ao mesmo tempo.

* Se possível, só inicie uma campanha por vez.
* No máximo, inicie apenas quatro campanhas por vez.
* Envie por push apenas para seus usuários ativos (caixa de seleção "Envolver apenas os usuários que podem ser contatados por meio de envio por push nativo" e "Envolver apenas os usuários ativos") para que somente os usuários que ainda tenham o aplicativo instalado e que o utilizem sejam verificados.
  Quando seu público-alvo for definido, você poderá usar o botão de simulação para descobrir quantos usuários receberão esse envio por push. Isso irá calcular o número de usuários conhecidos potencialmente direcionados para esse público-alvo (essa é uma estimativa com base em uma amostra aleatória de usuários). Lembre-se de que os usuários que desinstalaram o aplicativo também fazem parte desse público-alvo, mas não poderão ser alcançados.

### <a name="see-also"></a>Consulte também
* [Documentação da interface do usuário – Alcance – Novo Critério de Envio por Push][Link 28]

![Reach-Campaign6][25]

### <a name="edit-expression"></a>Editar expressão
![Reach-Campaign7][26]

### <a name="limit-your-audience-option-applies-to"></a>A opção para limitar seu público-alvo se aplica a:
* Envolver somente um subconjunto de usuários: todos (Anúncios, Pesquisas, Envios de dados por push, Blocos)
* Envolver apenas usuários antigos: todos (Anúncios, Pesquisas, Envios de dados por push, Blocos)
* Envolver apenas usuários novos: todos (Anúncios, Pesquisas, Envios de dados por push, Blocos)
* Envolver apenas usuários inativos: anúncios, pesquisas, blocos
* Envolver apenas usuários ativos: todos (Anúncios, Pesquisas, Envios de dados por push, Blocos)
* Envolver apenas os usuários que podem ser alcançados usando Push Nativo: anúncios, pesquisas

## <a name="time-frame"></a>Período
Você pode usar a seção Período para definir quando o envio por push será feito ou pode deixar o período em branco para iniciar a campanha imediatamente. Lembre-se de que usar o fuso horário dos usuários finais pode iniciar a campanha um dia antes que o esperado para os usuários finais na Ásia e fazer pequenos lotes de envios por push aos poucos até que o período definido para sua campanha coincida com todos os fusos horários do mundo. Usar o fuso horário dos usuários finais também pode causar atrasos em campanhas pois é preciso requisitar o horário do telefone antes de iniciar o envio por push.

> [!NOTE]
> Campanhas sem uma data de término podem armazenar localmente em cachês de envios e ainda exibi-los após as suas campanhas completas manualmente. Para evitar esse comportamento, especifique uma hora de término para as campanhas.

### <a name="see-also"></a>Consulte também
* [Alcance ‑ Instruções – Agendamento][Link 3] 

![Reach-Campaign8][27]

### <a name="settings-apply-to"></a>As configurações se aplicam a:
* Período: anúncios, pesquisas, blocos

## <a name="test"></a>Teste
Você pode usar a seção Teste para fazer esse envio por push para o seu próprio dispositivo de teste antes de salvar a campanha. Se você tiver configurado algum idioma personalizado para a campanha, poderá testar o envio para cada idioma. Você pode configurar um dispositivo de teste para "Minha Conta".

> [!NOTE]
> Nenhum dado do lado do servidor é registrado quando você usa o botão “testar” envios, os dados são registrados apenas para campanhas de envio real.

### <a name="see-also"></a>Consulte também
* [Documentação da Interface do Usuário ‑ Minha Conta][Link 14]

![Reach-Campaign9][28]

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

