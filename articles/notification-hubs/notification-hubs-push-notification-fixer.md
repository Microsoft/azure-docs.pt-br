---
title: "Hubs de Notificação do Azure - Diretrizes de Diagnóstico"
description: "Diretrizes sobre como diagnosticar problemas comuns com Hubs de Notificação do Azure."
services: notification-hubs
documentationcenter: Mobile
author: ysxu
manager: erikre
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 32e3a2e6f840afd865375a622cfae0d33ba65090
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs---diagnosis-guidelines"></a>Hubs de Notificação do Azure - Diretrizes de Diagnóstico
## <a name="overview"></a>Visão geral
Uma das perguntas mais comuns que ouvimos de nossos clientes de Hubs de Notificação do Azure é como descobrir por que eles não veem uma notificação enviada de seu back-end do aplicativo no dispositivo cliente - onde e por quê as notificações foram canceladas e como corrigir isso. Neste artigo, passaremos pelos vários motivos por que as notificações podem ser abandonadas ou não terminar nos dispositivos. Também veremos maneiras em que você pode analisar e descobrir a causa raiz. 

Em primeiro lugar, é fundamental entender como os Hubs de Notificação do Azure envia notificações para os dispositivos.
![][0]

Em um fluxo de notificação de envio típico, a mensagem é enviada do **back-end do aplicativo** para o **Hub de Notificação do Azure (NH)** que por sua vez faz algum processamento em todos os registros levando em conta as marcas configuradas e expressões de marca para determinar “destinos” ou seja, todos os registros que devem receber a notificação por push. Esses registros podem ser distribuídos em qualquer uma ou todas as nossas plataformas com suporte - iOS, Google, Windows, Windows Phone, Kindle e Baidu para Android na China. Depois que as metas forem estabelecidas, o NH enviará notificações, divididas em vários lotes de registros, para a plataforma de dispositivo específica **Serviço de Notificação por Push (PNS)** - por exemplo, APNS para Apple, GCM para Google, etc. O NH autentica com o respectivo PNS com base nas credenciais definidas no Portal Clássico do Azure na página Configurar Hub de Notificação. O PNS encaminha as notificações para os respectivos **dispositivos cliente**. Essa é a forma de plataforma recomendada para enviar notificações por push e observar que o segmento final da entrega da notificação ocorre entre a plataforma PNS e o dispositivo. Portanto, temos quatro componentes principais - *cliente*, *back-end do aplicativo*, *Hubs de Notificação do Azure (NH)* e *Serviços de Notificação por Push (PNS)* e qualquer um deles pode fazer com que as notificações sejam descartadas. Mais detalhes sobre essa arquitetura estão disponíveis em [Visão Geral dos Hubs de Notificação].

Falha para entregar notificações pode ocorrer durante o teste/teste inicial que pode indicar um problema de configuração da fase ou pode acontecer na produção onde todas ou algumas das notificações podem estar sendo descartadas indicando um problema maior no aplicativo ou problema no padrão de mensagens. Na seção abaixo veremos vários cenários de notificações descartadas desde comuns ao tipo mais raro, que talvez você encontre óbvio e outros não tão óbvios. 

## <a name="azure-notifications-hub-mis-configuration"></a>Configuração incorreta do Hub de Notificações do Azure
Os Hubs de Notificação do Azure precisam se autenticar no contexto do aplicativo do desenvolvedor para poder enviar notificações com êxito ao respectivo PNS. Isso é possibilitado pelo desenvolvedor criando uma conta de desenvolvedor com as respectivas plataformas (Google, Apple Windows, etc.) e, em seguida, registrando seu aplicativo onde eles obtém credenciais que devem ser configuradas no portal, na seção Configuração dos Hubs de Notificação. Se nenhuma notificação estiver passando, a primeira etapa deve ser garantir que as credenciais corretas sejam configuradas no Hub de Notificação, comparando-as com o aplicativo criado em sua conta de desenvolvedor específica de plataforma. Você achará os [Tutoriais de Introdução] úteis para acessar esse processo de maneira passo a passo. Aqui estão algumas configurações incorretas comuns:

1. **Geral**
   
    a) Certifique-se de que o nome de seu hub de notificação (sem erros de grafia) é o mesmo:
   
   * Onde você está registrando do cliente, 
   * Onde você está enviando notificações de back-end,  
   * Onde você configurou as credenciais PNS e 
   * Cujas credenciais SAS que você configurou no cliente e no back-end. 
     
     b) Certifique-se de que você está usando as cadeias de configuração SAS corretas no cliente e no back-end do aplicativo. Como regra geral, você deve estar usando o **DefaultListenSharedAccessSignature** no cliente e **DefaultFullSharedAccessSignature** no back-end do aplicativo (que concede permissão para enviar notificações para o NH)
2. **Configuração do Serviço de Notificação por Push da Apple (APNS)**
   
    Você deve manter dois hubs diferentes - um para produção e outro para fins de teste. Isso significa carregar o certificado que você pretende usar no ambiente de área restrita para um hub separado e o certificado que você pretende usar na produção para um hub separado. Não tente carregar tipos diferentes de certificados no mesmo hub porque pode causar falhas de notificação na linha. Se você estiver em uma posição em que tenha carregado inadvertidamente diferentes tipos de certificado no mesmo hub, é recomendável excluir o hub e começar do zero. Se por algum motivo, não é possível excluir o hub, na pior das hipóteses, você deve excluir todos os registros existentes do hub. 
3. **Configuração do Google Cloud Messaging (GCM)** 
   
    a) Certifique-se de que você está ativando o "Google Cloud Messaging para Android" em seu projeto de nuvem. 
   
    ![][2]
   
    b) Certifique-se de criar uma “Chave de Servidor” enquanto obtém as credenciais que o NH usará para autenticar com o GCM. 
   
    ![][3]
   
    c) Certifique-se de que você tenha configurado o "ID do Projeto" no cliente que é uma entidade totalmente numérica que você pode obter do painel de controle:
   
    ![][1]

## <a name="application-issues"></a>Problemas de aplicativos
1) **Marcas/Expressões de marca**

Se você estiver usando expressões de marca ou marcas para segmentar seu público-alvo, sempre é possível que, quando você estiver enviando a notificação, não tenha nenhum destino encontrado com base em expressões de marcas/marcas que você está especificando em sua chamada de envio. É melhor examinar os registros para verificar se há marcas que correspondem ao enviar notificação e, em seguida, verifique o recebimento de notificação somente de clientes com esses registros. Por exemplo Se todos os registros com NH foram feitos com a marca "Política" e você está enviando uma notificação com a marca "Esportes", ele não será enviado para qualquer dispositivo. Um caso complexo pode envolver a expressões de marca onde você tenha registrado apenas com a "Marca A" OU "Marca B", mas durante o envio de notificações, você tem como alvo a "Marca A & Marca B". Na seção de dicas de diagnóstico automático abaixo, há maneiras em que você pode examinar seus registros junto com as marcas que eles têm. 

2) **Problemas do modelo**

Se você estiver usando modelos que garantam você está seguindo as diretrizes descritas em [Diretrizes de modelo]. 

3) **Registros inválidos**

Supondo que o Hub de Notificação foi configurado corretamente e que qualquer expressões de marcas/marca foi usada corretamente, resultando na localização de destinos válidos para o qual as notificações precisam ser enviadas, o NH aciona vários lotes de processamento em paralelo - cada lote de envio de mensagens para um conjunto de registros. 

> [!NOTE]
> Como fazemos o processamento em paralelo, nós não garantimos a ordem em que as notificações serão entregues. 
> 
> 

Agora o Hub de Notificações do Azure foi otimizado para um modelo de entrega de mensagem "no máximo uma vez". Isso significa que podemos tentar uma eliminação de duplicação para que nenhuma notificação seja entregues mais de uma vez em um dispositivo. Para garantir isso, vamos examinar os registros e garantir que somente uma mensagem seja enviada por um identificador de dispositivo antes de realmente enviar a mensagem ao PNS. Como cada lote é enviado ao PNS, que por sua vez está aceitando e validando os registros, é possível que o PNS detecte um erro com um ou mais registros em um lote, retorna um erro para o Azure NH e interrompe o processamento, assim, descartando completamente esse lote. Isso é especialmente verdadeiro com o APNS que usa um protocolo de fluxo TCP. Embora estejamos otimizados para uma entrega “no máximo uma vez”, neste caso, removemos o registro com falha de nosso banco de dados e tentamos novamente a entrega da notificação para o restante dos dispositivos no lote.

Você pode obter informações de erro para a tentativa de entrega com falha em um registro usando as APIs REST dos Hubs de Notificação do Azure: [Por telemetria de mensagem: obter telemetria de mensagem de notificação](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [Comentários dos PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Veja [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) para obter um código de exemplo.

## <a name="pns-issues"></a>Problemas PNS
Depois que a mensagem de notificação tenha sido recebida pelo respectivo PNS, é sua responsabilidade entregar a notificação para o dispositivo. Os Hubs de Notificação do Azure estão fora da imagem aqui e não têm controle ou se a notificação estiver sendo entregue para o dispositivo. Como os serviços de notificação de plataforma são bastante robustos, as notificações tendem a acessar os dispositivos em alguns segundos do PNS. No entanto, se o PNS está diminuindo, os Hubs de Notificação do Azure aplicam uma estratégia de retirada exponencial e se o PNS permanece inacessível por 30 minutos, temos uma política em vigor para expirar e descartar essas mensagens permanentemente. 

Se um PNS tenta entregar uma notificação, mas o dispositivo está offline, a notificação é armazenada pelo PNS para um período de tempo limitado e entregue ao dispositivo quando ele estiver disponível. Apenas uma notificação recente de um aplicativo específico é armazenada. Se várias notificações são enviadas quando o dispositivo estiver offline, cada nova notificação faz com que a notificação anterior seja descartada. Esse comportamento de manter somente a notificação mais recente é chamado de notificações de coalescência no APNS e recolhimento no GCM (que usa uma chave de recolhimento). Se o dispositivo permanecer offline por um longo tempo, nenhuma notificação que estava sendo armazenada para ele é descartada. Origem - [Diretrizes do APNS] & [Diretrizes do APNS]

Com os Hubs de Notificação do Azure - você pode transmitir uma chave de coalescência por meio do cabeçalho HTTP usando a API `SendNotification` genérica (por exemplo, para o SDK do .NET – `SendNotificationAsync`) que também usa cabeçalhos HTTP que são transmitidos no estado em que estão para o respectivo PNS. 

## <a name="self-diagnose-tips"></a>Dicas de diagnóstico automático
Aqui examinaremos os vários caminhos para diagnosticar e verificar a causa raiz de problemas do Hub de Notificação:

### <a name="verify-credentials"></a>Verifique as credenciais
1. **Portal do desenvolvedor do PNS**
   
    Verifique-os no respectivo portal do desenvolvedor PNS (WNS, APNS, GCM, etc.) usando nossos [Tutoriais de Introdução].
2. **Portal clássico do Azure**
   
    Vá para a guia Configurar para revisar e corresponder com as credenciais obtidas no portal do desenvolvedor do PNS. 
   
    ![][4]

### <a name="verify-registrations"></a>Verificar registros
1. **Visual Studio**
   
    Se você usar o Visual Studio para desenvolvimento, é possível se conectar ao Microsoft Azure e exibir e gerenciar vários serviços do Azure, incluindo o Hub de Notificações "Gerenciador de Servidores". Isso é útil principalmente para seu ambiente de desenvolvimento e teste. 
   
    ![][9]
   
    Você pode exibir e gerenciar todos os registros em seu hub que são muito bem categorizados para o registro de plataforma, nativo ou modelo, todas as marcas, identificador PNS, id de registro e a data de validade. Você também pode editar um registro imediatamente - que é útil se você deseja editar marcas. 
   
    ![][8]
   
   > [!NOTE]
   > A funcionalidade do Visual Studio para editar os registros só deve ser usada durante o desenvolvimento e teste com um número limitado de registros. Se surgir a necessidade de corrigir seus registros em massa, considere o uso da funcionalidade de Importação/Exportação de registros descrita aqui - [Importação/Exportação de registros](https://msdn.microsoft.com/library/dn790624.aspx)
   > 
   > 
2. **Gerenciador do Barramento de Serviço**
   
    Muitos clientes utilizam Gerenciador do Barramento de Serviço descrito aqui - [Gerenciador do Barramento de Serviço] para exibir e gerenciar seu hub de notificação. Ele é um projeto de software livre disponível em code.microsoft.com - [Código do Gerenciador do Barramento de Serviço]

### <a name="verify-message-notifications"></a>Verificar as notificações de mensagem
1. **Portal Clássico do Azure**
   
    Você pode ir para a guia “Depuração" para enviar notificações de teste para seus clientes sem a necessidade de qualquer back-end de serviço funcionando e em execução. 
   
    ![][7]
2. **Visual Studio**
   
    Você também pode enviar notificações de teste do comforts do Visual Studio:
   
    ![][10]
   
    Você pode ler mais sobre a funcionalidade do gerenciador do Hub de Notificação do Azure do Visual Studio aqui - 
   
   * [Visão Geral do Gerenciador do VS Server]
   * [Postagem do Blog do Gerenciador do VS Server- 1]
   * [Postagem do Blog do Gerenciador do VS Server- 2]

### <a name="debug-failed-notifications-review-notification-outcome"></a>Depurar notificações de falha/revisar resultado da notificação
**Propriedade EnableTestSend**

Quando você envia uma notificação através dos Hubs de Notificação, inicialmente ele apenas obtém o enfileiramento para NH fazer o processamento para descobrir todos os seus destinos e eventualmente o NH envia para o PNS. Isso significa que quando você estiver usando a API REST ou qualquer SDK cliente, um retorno bem-sucedido de sua chamada de envio significa apenas que a mensagem foi enfileirada com êxito com o Hub de Notificação. Ele não dá uma visão geral do que aconteceu quando o NH eventualmente precisou enviar a mensagem para o PNS. Se a notificação não está sendo recebida no dispositivo cliente, há uma possibilidade de que quando o NH tentou entregar a mensagem para o PNS, ocorreu um erro, por exemplo, o tamanho da carga excedeu o máximo permitido pelo PNS ou as credenciais configuradas no NH são inválidas, etc. Para obter uma visão geral dos erros de PNS, apresentamos uma propriedade chamada [EnableTestSend feature]. Essa propriedade é habilitada automaticamente quando você envia mensagens de teste do portal ou do cliente do Visual Studio e, portanto, permite que você veja informações detalhadas de depuração. Você pode usá-lo através de APIs adotando o exemplo do SDK do .NET, onde ele está disponível agora e será eventualmente adicionado a todos os SDKs de cliente. Para usar com a chamada REST, basta acrescentar um parâmetro querystring chamado "test" no final de sua chamada de envio, por exemplo, 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Exemplo (SDK do .NET)*

Suponha que você esteja usando o SDK do .NET para enviar uma notificação do sistema nativa:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);

`result.State` será simplesmente estado `Enqueued` no final da execução sem qualquer ideia do que aconteceu com o envio por push. Agora você pode usar a propriedade booliana `EnableTestSend` ao inicializar o `NotificationHubClient` e obter o status detalhado sobre os erros PNS encontrados ao enviar a notificação. A chamada de envio aqui levará mais tempo porque ela está retornando somente após o NH ter fornecido a notificação para o PNS para determinar o resultado. 

    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Saída de exemplo*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Essa mensagem indica que qualquer credenciais inválidas são configuradas no hub de notificação ou um problema com os registros no hub e o curso recomendado seria excluir esse registro e deixar que o cliente recrie-a antes de enviar a mensagem. 

> [!NOTE]
> Observe que o uso dessa propriedade é muito limitado e, portanto, você só deve usar no ambiente de desenvolvimento/teste com um conjunto limitado de registros. Estamos enviando apenas notificações de depuração para 10 dispositivos. Também temos um limite de processamento de envios de depuração de 10 por minuto. 
> 
> 

### <a name="review-telemetry"></a>Telemetria de revisão
1. **Usar o Portal Clássico do Azure**
   
    O portal permite que você obtenha uma visão geral rápida de todas as atividades em seu Hub de Notificação. 
   
    a) Na guia "Painel", você pode exibir uma exibição agregada de registros, notificações, bem como erros por plataforma. 
   
    ![][5]
   
    b) Você também pode adicionar muitas outras métricas específicas de plataforma na guia "Monitor" para uma análise mais profunda especificamente sobre erros específicos de PNS retornados quando o NH tenta enviar a notificação para o PNS. 
   
    ![][6]
   
    c) Você deve começar a analisar o **Mensagens de Entrada**, **Operações de Registro**, **Notificações com Êxito** e ir para a guia por plataforma para examinar os erros específicos do PNS. 
   
    d) Se você tiver o hub de notificação mal configurado com as configurações de autenticação, você verá o Erro de Autenticação do PNS. Isso é uma boa indicação para verificar as credenciais do PNS. 

2) **Acesso programático**

Mais detalhes aqui - 

* [Acesso Programático de Telemetria]
* [Acesso de Telemetria por meio do exemplo de APIs] 

> [!NOTE]
> Várias telemetrias relacionados a recursos como **Importação/Exportação de Registros**, **Acesso de Telemetria via APIs** etc. só estão disponíveis na camada Standard. Se você tentar usar esses recursos se estiver na camada Free ou Basic, receberá a mensagem de exceção para esse efeito ao usar o SDK e um HTTP 403 (Proibido) quando usá-los diretamente pelas APIs REST. Certifique-se de que você atualizou para a camada Standard via portal Clássico do Azure.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Visão Geral dos Hubs de Notificação]: notification-hubs-push-notification-overview.md
[Tutoriais de Introdução]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Diretrizes de modelo]: https://msdn.microsoft.com/library/dn530748.aspx 
[Diretrizes do APNS]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Diretrizes do APNS]: http://developer.android.com/google/gcm/adv.html
[Export/Import Registrations]: http://msdn.microsoft.com/library/dn790624.aspx
[Gerenciador do Barramento de Serviço]: http://msdn.microsoft.com/library/dn530751.aspx
[Código do Gerenciador do Barramento de Serviço]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Visão Geral do Gerenciador do VS Server]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Postagem do Blog do Gerenciador do VS Server- 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Postagem do Blog do Gerenciador do VS Server- 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend feature]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Acesso Programático de Telemetria]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Acesso de Telemetria por meio do exemplo de APIs]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

