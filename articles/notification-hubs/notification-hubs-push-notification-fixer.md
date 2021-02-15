---
title: Diagnosticar notificações eliminadas nos hubs de notificação do Azure
description: Saiba como diagnosticar problemas comuns com notificações removidas nos Hubs de Notificações do Microsoft Azure.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: b5139f75084eb0646db2fc8b05b04aaf3ddb2a12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89010776"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnosticar notificações eliminadas nos hubs de notificação do Azure

Uma pergunta comum sobre os hubs de notificação do Azure é como solucionar problemas quando as notificações de um aplicativo não aparecem em dispositivos cliente. Os clientes desejam saber onde e por que as notificações foram removidas e como corrigir o problema. Este artigo identifica o motivo pelo qual as notificações podem ser removidas ou não recebidas pelos dispositivos. Ele também explica como determinar a causa raiz.

É fundamental compreender primeiro como os Hubs de Notificação efetua push das notificações para um dispositivo.

![Arquitetura dos Hubs de Notificação][0]

Em um fluxo de envio de notificação típico, a mensagem é enviada do *back-end do aplicativo* para os Hubs de Notificação. Os hubs de notificação processam todos os registros. Leva em consideração as marcas configuradas e as expressões de marca para determinar os destinos. Destinos são os registros que precisam receber a notificação por push. Esses registros podem abranger qualquer uma das plataformas com suporte: Android, Baidu (dispositivos Android na China), Amazon (sistema operacional de incêndio) iOS, Windows e Windows Phone.

Com os destinos estabelecidos, os Hubs de Notificação efetuam push nas notificações para o *serviço de notificação por push* para a plataforma do dispositivo. Os exemplos incluem o serviço de notificação por push da Apple (APNs) para iOS e macOS e o firebase Cloud Messaging (FCM) para dispositivos Android. Os Hubs de Notificação efetuam push de notificações divididas em diversos lotes de registros. Ele é autenticado com o respectivo serviço de notificação por push, com base nas credenciais definidas na portal do Azure, em **Configurar Hub de notificação**. O serviço de notificação por push encaminha as notificações para os respectivos *dispositivos cliente*.

O segmento final da entrega de notificação é entre o serviço de notificação por push da plataforma e o dispositivo. A entrega de notificação pode falhar em qualquer um dos quatro estágios do processo de notificação por push (cliente, back-end de aplicativo, hubs de notificação e serviço de notificação por push da plataforma). Para obter mais informações sobre a arquitetura dos Hubs de Notificação, consulte [Visão geral dos Hubs de Notificação].

Uma falha ao entregar notificações pode ocorrer durante a fase inicial de teste/preparo. Notificações removidas neste estágio podem indicar um problema de configuração. Se uma falha ao entregar notificações ocorrer em produção, algumas ou todas as notificações poderão ser descartadas. Um problema de aplicativo ou padrão de mensagens mais profundo é indicado nesse caso.

A próxima seção analisa os cenários em que as notificações podem ser removidas, variando de comum a raro.

## <a name="notification-hubs-misconfiguration"></a>Configuração incorreta dos Hubs de Notificação

Para enviar notificações para o respectivo serviço de notificação por push, os hubs de notificação devem se autenticar no contexto do seu aplicativo. Você deve criar uma conta de desenvolvedor com o serviço de notificação da plataforma de destino (Microsoft, Apple, Google, etc.). Em seguida, você deve registrar seu aplicativo com o sistema operacional em que você obtém um token ou chave que você usa para trabalhar com o PNS de destino.

Você precisa adicionar as credenciais da plataforma para o Portal do Azure. Se nenhuma notificação estiver atingindo o dispositivo, a primeira etapa será garantir que as credenciais corretas sejam configuradas nos hubs de notificação. As credenciais devem corresponder ao aplicativo criado em uma conta de desenvolvedor específica da plataforma.

Para ver instruções passo a passo para concluir esse processo, consulte [Introdução aos Hubs de Notificações do Microsoft Azure].

Aqui estão algumas configurações incorretas comuns para observar:

### <a name="notification-hub-name-location"></a>Local do nome do hub de notificação

Verifique se o nome do seu hub de notificação (sem erros de grafia) é o mesmo em todos esses locais:

* Onde você se registra no cliente
* Onde você envia notificações do back-end
* Onde você configurou as credenciais do serviço de notificação por push

Certifique-se de usar as cadeias de caracteres de configuração de assinatura de acesso compartilhado corretas no cliente e no back-end do aplicativo. Em geral, você deve usar **DefaultListenSharedAccessSignature** no cliente e **DefaultFullSharedAccessSignature** no back-end do aplicativo. Isso concede permissões para enviar notificações aos hubs de notificação.

### <a name="apn-configuration"></a>Configuração de APN

Você deve manter dois hubs diferentes: um para produção e outro para teste. Você deve carregar o certificado usado em um ambiente de área restrita para um hub separado do certificado/Hub que será usado na produção. Não tente carregar tipos diferentes de certificados para o mesmo hub. Isso causará falhas de notificação.

Se você carregar inadvertidamente diferentes tipos de certificados para o mesmo Hub, deverá excluir o Hub e começar a ser atualizado com um novo hub. Se, por alguma razão, você não puder excluir o Hub, deverá, pelo menos, excluir todos os registros existentes do Hub.

### <a name="fcm-configuration"></a>Configuração do FCM

1. Verifique se a *chave do servidor* obtida do firebase corresponde à chave do servidor que você registrou na portal do Azure.

   ![Chave do servidor Firebase][3]

2. Verifique se você configurou a **ID do projeto** no cliente. Você pode obter o valor da **ID do projeto** do painel do Firebase.

   ![ID do projeto Firebase][1]

## <a name="application-issues"></a>Problemas de aplicativos

### <a name="tags-and-tag-expressions"></a>Marcas e expressões de marca

Se você usar marcas ou expressões de marca para segmentar seu público-alvo, é possível que, ao enviar a notificação, nenhum destino seja encontrado. Esse erro se baseia nas marcas especificadas ou nas expressões de marca em sua chamada de envio.

Examine seus registros para garantir que as marcas correspondam quando você envia uma notificação. Em seguida, verifique a confirmação da notificação apenas dos clientes que têm esses registros.

Por exemplo, suponha que todos os seus registros com hubs de notificação usem a marca "política". Se você enviar uma notificação com a marca "esportes", a notificação não será enviada para nenhum dispositivo. Um caso complexo pode envolver expressões de marca em que você se registrou usando "Tag A" *ou* "tag b", mas foi direcionado para "marcar uma && marca B". A seção Dicas de autodiagnóstico posteriormente neste artigo mostra como revisar seus registros e suas marcas.

### <a name="template-issues"></a>Problemas do modelo

Se você usar modelos, siga as diretrizes descritas em [Modelos].

### <a name="invalid-registrations"></a>Registros inválidos

Se o Hub de notificação foi configurado corretamente e as marcas ou expressões de marca foram usadas corretamente, os destinos válidos serão encontrados. As notificações devem ser enviadas para esses destinos. Os Hubs de Notificação, em seguida, disparam vários lotes de processamento em paralelo. Cada lote envia mensagens para um conjunto de registros.

> [!NOTE]
> Como os hubs de notificação processam lotes em paralelo, a ordem na qual as notificações são entregues não é garantida.

Os hubs de notificação são otimizados para um modelo de entrega de mensagens "no máximo uma vez". Tentamos realizar uma eliminação de duplicação para que nenhuma notificação seja entregue mais de uma vez para um dispositivo. Os registros são verificados para garantir que apenas uma mensagem seja enviada por identificador de dispositivo antes de ser enviada para o serviço de notificação por push.

Cada lote é enviado para o serviço de notificação por push, que, por sua vez, aceita e valida os registros. Durante esse processo, é possível que o serviço de notificação por push detecte um erro com um ou mais registros em um lote. O serviço de notificação por push retorna um erro para os hubs de notificação e o processo é interrompido. O serviço de notificação por push remove esse lote completamente. Isso é especialmente verdadeiro com o APNs, que usa um protocolo de fluxo TCP.

Nesse caso, o registro com falha é removido do banco de dados. Em seguida, tentamos entregar a notificação novamente para o restante dos dispositivos nesse lote.

Para obter mais informações de erro sobre a tentativa de entrega com falha em relação a um registro, você pode usar as APIs REST dos hubs de notificação [por telemetria de mensagem: obter telemetria de mensagem de notificação](/rest/api/notificationhubs/get-notification-message-telemetry) e [comentários de PNS](/previous-versions/azure/reference/mt705560(v=azure.100)). Para obter o código de exemplo, consulte o [Exemplo de envio de REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemas do serviço de notificação por push

Depois que o serviço de notificação por push recebe a notificação, ele entrega a notificação para o dispositivo. Neste ponto, os hubs de notificação não têm controle sobre a entrega da notificação para o dispositivo.

Como os serviços de notificação de plataforma são robustos, as notificações tendem a alcançar dispositivos em alguns segundos. Se o serviço de notificação por push estiver sendo acelerado, os hubs de notificação aplicarão uma estratégia de retirada exponencial. Se o serviço de notificação por push permanecer inacessível por 30 minutos, há uma política em vigor para expirar e descartar as mensagens permanentemente.

Se um serviço de notificação por push tentar entregar uma notificação, mas o dispositivo estiver offline, a notificação será armazenada pelo serviço de notificação por push. Ele é armazenado por apenas um período de tempo limitado. A notificação é entregue ao dispositivo quando ele estiver disponível.

Cada aplicativo armazena apenas uma notificação recente. Se várias notificações forem enviadas enquanto um dispositivo estiver offline, cada nova notificação fará com que a última seja descartada. Manter apenas a notificação mais recente é chamada de *União* no APNs e *recolher* no FCM. (FCM usa uma chave de recolhimento.) Quando o dispositivo permanece offline por um longo tempo, as notificações que foram armazenadas para o dispositivo são descartadas. Para obter mais informações, consulte [visão geral de APNs] e [sobre mensagens FCM].

Com os hubs de notificação, você pode passar uma chave de União por meio de um cabeçalho HTTP usando a API SendNotification genérica. Por exemplo, para o SDK do .NET, você usaria `SendNotificationAsync`. A API SendNotification também usa cabeçalhos HTTP que são passados como estão para o respectivo serviço de notificação por push.

## <a name="self-diagnosis-tips"></a>Dicas de autodiagnóstico

Aqui estão os caminhos para diagnosticar a causa raiz das notificações descartadas nos hubs de notificação.

### <a name="verify-credentials"></a>Verifique as credenciais

#### <a name="push-notification-service-developer-portal"></a>Portal do desenvolvedor do serviço de notificação por push

Verifique as credenciais no respectivo portal do desenvolvedor do serviço de notificação por push (APNs, FCM, Serviço de Notificação do Windows e assim por diante). Para obter mais informações, consulte [tutorial: enviar notificações para plataforma universal do Windows aplicativos usando os hubs de notificação do Azure](./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

#### <a name="azure-portal"></a>Portal do Azure

Para revisar e corresponder as credenciais que você obteve no portal do desenvolvedor do serviço de notificação por push, vá para a guia **políticas de acesso** na portal do Azure.

![Políticas de acesso do Portal do Azure][4]

### <a name="verify-registrations"></a>Verificar registros

#### <a name="visual-studio"></a>Visual Studio

No Visual Studio, você pode se conectar ao Azure por meio de Gerenciador de Servidores para exibir e gerenciar vários serviços do Azure, incluindo hubs de notificação. Esse atalho é útil principalmente para seu ambiente de desenvolvimento/teste.

![Gerenciador de Servidores do Visual Studio][9]

![Gerenciador de Servidores](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Você pode exibir e gerenciar todos os registros em seu hub. Os registros podem ser categorizados por plataforma, registro nativo ou de modelo, marca, identificador de serviço de notificação por push, ID de registro e data de validade. Também é possível editar um registro nesta página. Ele é especialmente útil para editar marcas.

Clique com o botão direito do mouse no Hub de notificação em **Gerenciador de servidores**e selecione **diagnosticar**. 

![Gerenciador de Servidores do Visual Studio: menu diagnosticar](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Você verá a seguinte página:

![Visual Studio: página de diagnóstico](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Alterne para a página de **registros do dispositivo** :

![Visual Studio: registros de dispositivo](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

Você pode usar a página de **envio de teste** para enviar uma mensagem de notificação de teste:

![Visual Studio: enviar teste](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Use o Visual Studio para editar os registros somente durante o desenvolvimento/teste e com um número limitado de registros. Se você precisar editar seus registros em massa, considere o uso da funcionalidade exportar e importar Registro descrita em [como: exportar e modificar registros em massa](/previous-versions/azure/azure-services/dn790624(v=azure.100)).

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Muitos clientes usam o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer) para exibir e gerenciar seus hubs de notificação. O Gerenciador de Barramento de Serviço é um projeto de software livre. 

### <a name="verify-message-notifications"></a>Verificar as notificações de mensagem

#### <a name="azure-portal"></a>Portal do Azure

Para enviar uma notificação de teste para seus clientes sem precisar de um serviço de back-end em funcionamento, em **SUPORTE + SOLUÇÃO DE PROBLEMAS**, selecione **Envio de Teste**.

![Funcionalidade de Envio de Teste no Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Você também pode enviar notificações de teste do Visual Studio.

![Funcionalidade de Envio de Teste no Visual Studio][10]

Para obter mais informações sobre como usar os Hubs de Notificação com o Gerenciador de Servidores do Visual Studio, consulte estes artigos:

* [Como exibir registros de dispositivo para hubs de notificação](/previous-versions/windows/apps/dn792122(v=win.10))
* [Análise aprofundada: Visual Studio 2013 Atualização 2 RC e SDK do Azure 2.3]
* [Anunciando o lançamento do Visual Studio 2013 Atualização 3 e SDK do Azure 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Depurar notificações com falha e examinar o resultado da notificação

#### <a name="enabletestsend-property"></a>Propriedade EnableTestSend

Quando você envia uma notificação por meio de hubs de notificação, a notificação é inicialmente enfileirada. Os Hubs de Notificação determinam os destinos corretos e, em seguida, enviam a notificação para o serviço de notificação por push. Se você estiver usando a API REST ou qualquer um dos SDKs do cliente, o retorno de sua chamada de envio significa apenas que a mensagem é enfileirada com hubs de notificação. Ele não fornece informações sobre o que aconteceu quando os hubs de notificação eventualmente enviaram a notificação para o serviço de notificação por push.

Se a notificação não chegar ao dispositivo cliente, poderá ocorrer um erro quando os hubs de notificação tentarem entregá-la ao serviço de notificação por push. Por exemplo, o tamanho do conteúdo poderá exceder o máximo permitido pelo serviço de notificação por push, ou as credenciais configuradas em Hubs de Notificação podem ser inválidas.

Para obter informações sobre erros do serviço de notificação por push, você pode usar a propriedade [EnableTestSend]. Essa propriedade é habilitada automaticamente quando você envia mensagens de teste do portal ou do cliente do Visual Studio. Você pode usar essa propriedade para ver informações detalhadas de depuração e também por meio de APIs. No momento, é possível usá-la no SDK do .NET. Ele será adicionado a todos os SDKs do cliente eventualmente.

Para usar a propriedade `EnableTestSend` com a chamada REST, acrescente um parâmetro de cadeia de consulta chamado *test* ao final da sua chamada de envio. Por exemplo:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Exemplo do SDK do .NET

Veja este exemplo de como usar o SDK do .NET para enviar uma notificação de pop-up nativo (notificação do sistema):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

No final da execução, `result.State` simplesmente declara `Enqueued`. Os resultados não fornecem informações sobre o que aconteceu com sua notificação por push.

Em seguida, você pode usar a propriedade booliana `EnableTestSend`. Use a propriedade `EnableTestSend` ao iniciar `NotificationHubClient` para obter um status detalhado sobre os erros do serviço de notificação por push que ocorrem quando a notificação é enviada. A chamada de envio leva tempo adicional para retornar porque primeiro ele precisa de hubs de notificação para entregar a notificação ao serviço de notificação por push.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Saída de exemplo

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Essa mensagem indica que as credenciais configuradas nos hubs de notificação são inválidas ou que há um problema com os registros no Hub. Exclua esse registro e deixe o cliente recriar o registro antes de enviar a mensagem.

> [!NOTE]
> O uso da propriedade `EnableTestSend` é extremamente limitado. Use essa opção somente em um ambiente de desenvolvimento/teste e com um conjunto limitado de registros. As notificações de depuração são enviadas para apenas 10 dispositivos. Também há um limite de processamento de envios de depuração, a 10 por minuto.

### <a name="review-telemetry"></a>Telemetria de revisão

#### <a name="azure-portal"></a>Portal do Azure

No portal, é possível obter uma visão geral rápida de todas as atividades no seu hub de notificação.

1. Na guia **Visão geral**, é possível ver uma exibição agregada de registros, notificações e erros por plataforma.

   ![Painel Visão geral dos Hubs de Notificação][5]

2. Na guia **Monitor**, você pode adicionar várias outras métricas específicas da plataforma para uma análise mais profunda. Você pode olhar especificamente os erros que são retornados quando os hubs de notificação tentam enviar a notificação para o serviço de notificação por push.

   ![Log de atividades do Portal do Azure][6]

3. Comece revisando as **Mensagens de entrada**, **Operações de registro** e **Notificações com êxito**. Em seguida, acesse a guia por plataforma para examinar os erros que são específicos para o serviço de notificação por push.

4. Se as configurações de autenticação para o hub de notificação estiverem incorretas, a mensagem **Erro de autenticação do PNS** será exibida. É uma boa indicação para verificar as credenciais do serviço de notificação por push.

#### <a name="programmatic-access"></a>Acesso Programático

Para obter mais informações sobre o acesso programático, consulte [acesso programático](/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Vários recursos relacionados à telemetria, como exportar e importar os registros e acesso à telemetria por meio de APIs, estão disponíveis apenas na camada de serviço Standard. Se você tentar usar esses recursos da camada de serviço gratuita ou básica, receberá uma mensagem de exceção se usar o SDK. Você obterá um erro HTTP 403 (proibido) se usar os recursos diretamente das APIs REST.
>
> Para usar os recursos relacionados à telemetria, primeiro verifique no portal do Azure que você está usando a camada de serviço Standard.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Visão geral dos hubs de notificação]: notification-hubs-push-notification-overview.md
[Introdução aos Hubs de Notificações do Microsoft Azure]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Modelos]: /previous-versions/azure/azure-services/dn530748(v=azure.100)
[Visão geral de APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Sobre mensagens de FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: /previous-versions/azure/azure-services/dn790624(v=azure.100)
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: /previous-versions/windows/apps/dn792122(v=win.10)
[Análise aprofundada: Visual Studio 2013 Atualização 2 RC e SDK do Azure 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Anunciando o lançamento do Visual Studio 2013 Atualização 3 e SDK do Azure 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: /dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: /previous-versions/azure/azure-services/dn458823(v=azure.100)
