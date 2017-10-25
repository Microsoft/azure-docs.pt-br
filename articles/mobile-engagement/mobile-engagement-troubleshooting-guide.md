---
title: "Guias de solução de problemas Azure Mobile Engagement"
description: "Guia de solução de problemas para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 93b5e3f4892f974bf9df28955956136528470e03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Guia de solução de problemas do Azure Mobile Engagement
## <a name="introduction"></a>Introdução
O guia de solução de problemas a seguir o ajudará a entender as causas raiz de alguns problemas comuns e permitirão que você solucione os problemas por conta própria. 

## <a name="general"></a>Geral
Em geral, você deve sempre verificar o seguinte:

1. Certifique-se de que você passou por todas as etapas necessárias para a integração, conforme descrito nos nossos [tutoriais de Introdução](mobile-engagement-windows-store-dotnet-get-started.md)
2. Você está usando a versão mais recente dos SDKs da plataforma. 
3. Teste em um dispositivo real e um emulador porque alguns problemas são específicos somente do emulador. 
4. Você não está atingindo nenhum dos limites/restrições do Mobile Engagement documentados [aqui](../azure-subscription-service-limits.md)
5. Se você não consegue se conectar ao back-end de serviço do Mobile Engagement ou estiver vendo que os dados não estão sendo carregados de modo contínuo, certifique-se de que não há nenhum incidente de serviço contínuo verificando [aqui](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problemas com o 'Monitor'
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Não estou vendo meu dispositivo aparecendo na guia Monitor
A guia Monitor mostra os dispositivos conectados à sua plataforma Mobile Engagement em tempo real. Se você estiver depurando em um dispositivo e emulador, você verá pelo menos uma sessão aqui. Se o aplicativo tiver sido distribuído, então você verá que o medidor de Sessões ativas reflete os dispositivos que estão conectados à plataforma em tempo real. 

Se você não está vendo seu dispositivo na guia Monitor então provavelmente há um problema de integração do SDK. Algumas etapas comuns a serem seguidas para solucionar problemas são as seguintes:

1. Certifique-se de que você esteja usando a cadeia de conexão correta no aplicativo móvel e que ela seja da seção de chaves do SDK e não da seção de chaves de API. A cadeia de conexão conecta seu aplicativo móvel à instância do aplicativo Mobile Engagement no qual você verá seu dispositivo na guia Monitor. 
2. Para a plataforma Windows - se a sua página substituir o método `OnNavigatedTo`, lembre-se de chamar `base.OnNavigatedTo(e)`.
3. Se você estiver integrando Mobile Engagement em um aplicativo móvel existente, você também pode garantir que você não está esquecendo de nenhuma etapa, observando as etapas de integração avançada [aqui](mobile-engagement-windows-store-integrate-engagement.md)
4. Certifique-se de que você está enviando a pelo menos uma tela/atividade, substituindo a página com EngagementActivity dependendo da plataforma que você está trabalhando conforme descrito nos [tutoriais de Introdução](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Estou vendo a guia Monitor mostrando uma sessão mesmo quando desconectei ou fechei meu aplicativo / emulador.
Se você for o único conectado à plataforma neste momento e você estiver usando um emulador para abrir o aplicativo, então isso provavelmente é devido a alguma peculiaridade do emulador. Em geral, você precisa garantir que você volta para a tela inicial do emulador para a sessão de aplicativo desconectar-se com êxito. Além disso, na plataforma Windows, durante a depuração com o Visual Studio, talvez convenha certificar-se, no Visual Studio, de ir até a barra de menus **Eventos de Ciclo de Vida** e clicar em **Suspender** para realmente fechar a sessão. Vejao [Tutorial do Windows](mobile-engagement-windows-store-dotnet-get-started.md) para obter mais detalhes. 

## <a name="analytics-issues"></a>Problemas de 'Análise'
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>Eu não estou vendo nenhum dado / dado atualizado na guia Análise
Os dados de análise são recalculados regularmente e essa atualização pode levar até 24. Esses dados não são em tempo real e você os verá atualizados nesse período de 24 horas.
Verifique, no entanto, que você está enviando pelo menos uma tela ou Atividade para o back-end da plataforma substituindo pelo menos uma página por `EngagementActivity` ou chamando `SendActivity` explicitamente. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Estou vendo a data/hora capturada de forma incorreta para um dispositivo na guia de Análise
O período de tempo para a Análise se baseia na data nas configurações de dispositivo dos usuários. Portanto, certifique-se de que o dispositivo tem a data definida corretamente. 

## <a name="segment-issues"></a>Problemas de 'Segmento'
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Criei um segmento e está aparecendo como desativado ou não mostra nenhum dado
A criação de segmento não é em tempo real no momento. Ela é calculada ao mesmo tempo em que os dados de análise são agregados e, portanto, pode levar até 24 horas. Você deve verificar novamente mais tarde, mas enquanto isso você também deve garantir que seus aplicativos móveis estão, de fato, enviando os dados enquanto você forma os segmentos. Por exemplo se um evento disser que 'foo' não está sendo enviado por nenhum dispositivo móvel, então não haverá nenhum dado de segmento para um segmento criado com EventName = foo como critério. Você também deve verificar a integração do SDK para garantir que seu aplicativo móvel está enviando os dados corretamente. 

## <a name="reach-or-push-notifications-issues"></a>Problemas de notificações por Push ou 'Reach'
### <a name="my-push-messages-are-not-being-delivered"></a>Minhas mensagens de push não estão sendo entregues
1. Tente enviar notificações para um dispositivo de teste primeiro para garantir que todos os componentes - aplicativo móvel, SDK e o serviço estão corretamente conectados e são capazes de enviar notificações por push. 
2. Sempre envie 'notificação de fora do aplicativo' mais simples primeiro por meio de uma campanha que não está agendada e nem tem qualquer critério público especificado. Novamente, isso é para provar que a conectividade de notificação está funcionando corretamente. 
3. Se você estiver tendo problemas na entrega de notificações no aplicativo, então também é uma boa primeira etapa tentar enviar uma notificação de fora do aplicativo pela primeira vez. 
4. Certifique-se de que o 'Native Push' está configurado corretamente para seu aplicativo móvel. Dependendo da plataforma isso envolverá chaves (Android, Windows) ou certificados (iOS). Consulte [Interface do usuário - Configurações](mobile-engagement-user-interface-settings.md)
5. As notificações fora do aplicativo também podem ser bloqueadas pelo usuário através do SO móvel então certifique-se que esse não é o caso. 
6. Certifique-se de que você não está definindo a opção *Ignorar Público, push será enviada aos usuários por meio da API* na seção **Campanha** de uma campanha de Reach porque isso garantirá que as notificações por push só podem ser enviadas por meio de APIs. 
7. Certifique-se de que você está testando sua campanha de push com ambos os dispositivos conectados via rede de operadora de telefone e WIFI para eliminar a conexão de rede como uma possível fonte de problemas.
8. Certifique-se de que a data/hora do sistema no seu dispositivo/emulador é correta porque qualquer dispositivo fora de sincronia também irá interferir com capacidade do Serviço de Notificação por Push para entregar notificações. 

Abaixo estão mais instruções de solução de problemas relacionados à plataforma:

1. **iOS** 
   
   * Verifique se os certificados são válidos e não expirados para notificações por Push do iOS. 
   * Certifique-se de que você está configurando corretamente um certificado de *Produção* em seu aplicativo Mobile Engagement. 
   * Certifique-se de que você esteja testando em um *dispositivo real, físico.* O simulador de iOS não pode processar mensagens por push.
   * Certifique-se de que o Identificador de pacote está configurado corretamente no aplicativo móvel. Veja as instruções [aqui](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
   * Durante o teste, use a distribuição "Ad Hoc" em seu perfil de provisionamento móvel. Você não poderá receber uma notificação se seu aplicativo for compilado usando "Depurar"
2. **Android**
   
   * Certifique-se de que você especificou o número correto de Projeto no arquivo Androidmanifest.xml do aplicativo móvel que é seguido pelo caractere \n. 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * Certifique-se de que não há permissões ausentes ou configuradas incorretamente no arquivo de Manifesto do Android 
   * Certifique-se de que o número do Projeto que você está adicionando ao seu aplicativo cliente é da mesma conta em que você obteve a Chave de Servidor GCM. Qualquer incompatibilidade entre os dois impedirá o envio dos pushes. 
   * Se você estiver recebendo notificações do sistema, mas não no aplicativo, então examine a seção [Especificar um ícone para notificações](mobile-engagement-android-get-started.md) já que você provavelmente não está especificando o ícone correto no arquivo de manifesto do Android. 
   * Se você estiver enviando uma notificação BigPicture, certifique-se de que, se você tiver servidores de imagens externas, eles precisam ser capazes de suportar HTTP "GET" e "HEAD".
3. **Windows**
   
   * Certifique-se de que você associou o aplicativo com um aplicativo da Windows Store válido. No Visual Studio – você terá que clicar com o botão direito do mouse no projeto e selecionar a opção "Associar o aplicativo com a Store" e selecionar o aplicativo que você criou na Windows Store. Este aplicativo da Windows Store deve ser o mesmo de onde você obteve as credenciais de push nativa para configurar no portal do Mobile Engagement.
   * Se você estiver recebendo notificações por push fora do aplicativo, mas não notificações no aplicativo com integração `EngagementOverlay` , então verifique se há um elemento de grade na raiz na sua página. O EngagementOverlay usa o primeiro elemento "Grid” que encontra no arquivo xaml para adicionar dois modos de exibição da web na sua página. Se você quiser localizar onde os modos de exibição da web serão definidos, você pode definir uma grade chamada "EngagementGrid", no entanto você terá que garantir que há suficiente altura e largura suficientes os dois modos de exibição web subsequentes que mostrarão a notificação e o seguinte anúncio como notificação no aplicativo:
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>Criei uma notificação por push/anúncio/campanha e mesmo depois que ele me enviou a notificação, ela está aparecendo como 'Ativa'. O que isso significa?
A **campanha** que você criou no Mobile Engagement é chamada assim porque é uma notificação por push sendo executada há muito tempo o que significa que quando novos dispositivos se conectam à sua plataforma do Mobile Engagement, eles receberão automaticamente a notificação que você configurar aqui, desde que eles atendam ao critério definido na campanha. Essa não é uma configuração de notificação única. Você precisará clicar manualmente no botão **Concluir** para encerrar a campanha de forma que ela não envie mais notificações. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>Criei uma campanha de push e estou recebendo notificações com êxito, no entanto, sempre que abro o aplicativo, obtenho a mesma notificação mesmo quando executei uma ação para a mesma antes?
Isso costuma acontecer durante o teste e se você estiver usando emuladores ou alguma estrutura de test como TestFlight. O que está acontecendo aqui é que, em todos as instâncias de execução do aplicativo, ele está adquirindo um novo DeviceID e enviando ao nosso back-end o que está fazendo com que a plataforma Mobile Engagement trate-o como um novo dispositivo e envie uma notificação. 

## <a name="getting-support"></a>Obtendo suporte
Se você não puder resolver o problema por conta própria, você pode:

1. Pesquise pelo seu problema nos threads existentes no fórum StackOverflow e no [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) e, se não entrá-lo, faça uma pergunta nesses fóruns. 
2. Se você encontrar um recurso ausente, então adicione/vote para a solicitação no nosso [Fórum UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Se você tiver o Suporte da Microsoft abra um incidente de suporte fornecendo os seguintes detalhes: 
   * ID de assinatura do Azure
   * Plataforma (por exemplo, iOS, Android, etc.)
   * ID do Aplicativo
   * ID da campanha (para problemas de notificação por push)
   * Id do Dispositivo
   * Versão do SDK do Mobile Engagement (por exemplo, Android SDK v2.1.0)
   * Detalhes do erro com a mensagem de erro e cenário exatos

