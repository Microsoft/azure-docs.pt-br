---
title: "Hubs de Notificação do Azure: perguntas frequentes | Microsoft Docs"
description: "Perguntas frequentes sobre como projetar/implementar soluções em Hubs de Notificação"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: "notificação por push, notificações por push, notificações por push do iOS, notificações por push do android, push do ios, push do android"
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
ms.openlocfilehash: f3bfda250b7f8ed679d05057682b95683d402535
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Notificações por push com os Hubs de Notificação do Azure: perguntas frequentes
## <a name="general"></a>Geral
### <a name="what-is-the-resource-structure-of-notification-hubs"></a>O que é a estrutura de recursos dos Hubs de Notificação?

Os Hubs de Notificação do Azure têm dois níveis de recursos: hubs e namespaces. Um hub é um recurso de envio por push único que pode conter as informações de push de plataforma cruzada de um aplicativo. Um namespace é uma coleção de hubs em uma região.

Mapeamento recomendado corresponde a um namespace com um único aplicativo. Dentro do namespace, você pode ter um hub de produção que funciona com o seu aplicativo de produção, um hub de teste que funciona com o seu aplicativo de teste e assim por diante.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Qual é o modelo de preço para os Hubs de Notificação?
Os detalhes de preços mais recentes podem ser encontrados na página [Preços dos Hubs de Notificação]. Os Hubs de Notificação são cobrados no nível de namespace. (Para a definição de um namespace, veja "Qual é a estrutura de recursos dos Hubs de Notificação?") Os Hubs de Notificação oferecem três camadas:

* **Livre**: esta camada é um bom ponto de partida para explorar os recursos de envio. Não é recomendável para aplicativos de produção. Obter 500 dispositivos e 1 milhão envia incluído por namespace por mês, com nenhuma garantia (SLA) contrato de nível de serviço.
* **Básico**: essa camada (ou a Standard) é recomendável para aplicativos de produção menores. Obter 200.000 dispositivos e 10 milhões envia incluído por namespace por mês como uma linha de base. Opções de aumento de cota são incluídas.
* **Padrão**:essa camada é recomendada para aplicativos de produção médios a grandes. Obter 10 milhões de dispositivos e 10 milhões envia incluído por namespace por mês como uma linha de base. Recursos de telemetria cota aumento opções e recursos estão incluídos.

Recursos de camada padrão:
* **Telemetria avançada**: você pode usar os Hubs de Notificação por Telemetria de Mensagem para rastrear quaisquer solicitações de envio por push e Comentários do Sistema de Notificação de Plataforma para depuração.
* **Multilocação**: você pode trabalhar com as credenciais do Sistema de Notificação de Plataforma em nível de namespace. Esta opção permite que você dividida locatários facilmente em hubs no mesmo namespace.
* **Push agendado**: você pode agendar que notificações sejam enviadas a qualquer momento.

### <a name="what-is-the-notification-hubs-sla"></a>O que é o SLA dos Hubs de Notificação?
Para camadas Basic e Standard dos Hubs de Notificação, os aplicativos configurados corretamente podem enviar notificações por push ou executar operações de gerenciamento de registro pelo menos 99,9% do tempo. Para saber mais sobre o SLA, vá para a página [SLA de Hubs de Notificação](https://azure.microsoft.com/support/legal/sla/notification-hubs/).

> [!NOTE]
> Como notificações por push dependem de Sistemas de Notificação de Plataforma de terceiros (APNs da Apple e FCM do Google), não há nenhuma garantia de SLA para a entrega dessas mensagens. Depois que os Hubs de Notificação enviam os lotes para os Sistemas de Notificação de Plataforma (garantido por SLA), será responsabilidade dos Sistemas de Notificação de Plataforma entregar os envios (sem garantia de SLA).

### <a name="which-customers-are-using-notification-hubs"></a>Quais clientes estão usando os Hubs de Notificação?
Muitos clientes utilizam os Hubs de Notificação. Alguns dos notáveis são listados aqui:

* Sochi 2014: Centenas de 150 milhões notificações enviadas em duas semanas, 3 milhões de dispositivos e grupos de interesse. [Estudo de caso: Sochi]
* Skanska: [Estudo de caso: Skanska]
* Seattle Times: [Estudo de caso: Seattle Times]
* Mural.ly: [Estudo de caso: Mural.ly]
* 7Digital: [Estudo de caso: 7Digital]
* Aplicativos Bing: Dezenas de milhões de dispositivos de enviar notificações de 3 milhões por dia.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Como atualizar ou fazer downgrade do meu hub ou namespace para uma camada diferente?
Vá para o  **[portal do Azure]** > **Namespaces de Hubs de Notificação** ou **Hubs de Notificação**. Selecione o recurso que você deseja atualizar e vá para **Tipo de Preço**. Observe os seguintes requisitos:

* O tipo de preço atualizado se aplica a *todos* os hubs no namespace com o qual você está trabalhando.
* Se a contagem de dispositivos exceder o limite da camada que você estiver fazendo downgrade para, você precisa excluir dispositivos antes de você fazer o downgrade.


## <a name="design-and-development"></a>Design e desenvolvimento
### <a name="which-server-side-platforms-do-you-support"></a>A quais plataformas do lado do servidor você oferece suporte?
Os SDKs do servidor estão disponíveis para .NET, Java, Node. js, PHP e Python. As APIs de Hubs de Notificação baseiam-se em interfaces REST, portanto, você pode trabalhar diretamente com as APIs REST se você estiver usando diferentes plataformas ou não desejar dependência extra. Para saber mais, vá para a página [APIs REST de Hubs de Notificação].

### <a name="which-client-platforms-do-you-support"></a>A quais plataformas de cliente você oferece suporte?
As notificações por push têm suporte para plataformas [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Universal do Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) e [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Para saber mais, vá para a página [tutoriais de Introdução aos Hubs de Notificação].

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Há suporte para notificações de Web, email ou mensagem de texto?
Os Hubs de Notificação são projetados principalmente para enviar notificações aos aplicativos móveis. Ele não fornece email ou texto recursos de mensagem. No entanto, plataformas de terceiros que fornecem esses recursos podem ser integradas com Hubs de Notificação para enviar notificações de push nativo usando os [Aplicativos Móveis].

Os Hubs de Notificação também não oferecem um serviço de entrega de notificações por push no navegador pronto para uso. Os clientes podem implementar esse recurso usando o SignalR sobre as plataformas com suporte do lado do servidor. Se você quiser enviar notificações aos aplicativos de navegador na área restrita do Chrome, confira o [tutorial de Aplicativos do Chrome].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Como os Aplicativos Móveis e os Hubs de Notificação do Azure estão relacionados e quando usá-los?
Se você tiver um back-end do aplicativo móvel existente e desejar apenas adicionar o recurso para enviar notificações por push, poderá usar os Hubs de Notificação do Azure. Se você quiser configurar seu aplicativo móvel volta final do zero, considere usar o recurso de aplicativos móveis do serviço de aplicativo do Azure. Um aplicativo móvel provisiona automaticamente um hub de notificação para que você pode enviar facilmente notificações por push do back-end do aplicativo móvel. Preços de aplicativos móveis incluem os encargos de base para um hub de notificação. Você paga apenas quando ultrapassar os envios incluídos. Para obter mais detalhes sobre os custos, vá para o [preços do serviço de aplicativo] página.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Para quantos dispositivos posso dar suporte se eu enviar notificações por push por meio dos Hubs de Notificação?
Veja a página [Preços dos Hubs de Notificação] para obter detalhes sobre o número de dispositivos com suporte.

Se você precisar de suporte para mais de 10 milhões de dispositivos registrados, [entre em contato conosco](https://azure.microsoft.com/overview/contact-us/) diretamente e ajudaremos você a dimensionar sua solução.

### <a name="how-many-push-notifications-can-i-send-out"></a>Quantas notificações por push posso enviar?
Dependendo da camada selecionada, o Hubs de Notificação do Azure escalará verticalmente de forma automática, com base no número de notificações fluindo pelo sistema.

> [!NOTE]
> O custo de uso geral pode subir com base no número de notificações por push servidas. Esteja ciente dos limites do tipo indicados na página [Preços dos Hubs de Notificação] .
> 
> 

Nossos clientes usam os Hubs de Notificação para enviar milhões de notificações por push por dia. Você não precisa fazer nada especial para dimensionar o alcance de suas notificações por push, desde que esteja usando os Hubs de Notificação do Azure.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Quanto tempo leva para as notificações chegarem ao meu dispositivo?
Em um cenário de uso normal, onde a carga de entrada é consistente e uniforme, os Hubs de Notificação do Azure conseguem processar pelo menos *um milhão envios por push em um minuto*. Essa taxa pode variar dependendo do número de marcas, da natureza dos envios de entrada e outros fatores externos.

Durante o tempo de entrega estimado, o serviço calcula os destinos por plataforma e encaminhar mensagens para o PNS (Serviço de Notificação por Push) com base em expressões de marca/marcas registradas. É responsabilidade do PNS para enviar notificações para o dispositivo.

O PNS não garante qualquer SLA para entregar notificações. No entanto, a maioria das notificações de push é entregue aos dispositivos de destino em poucos minutos (normalmente em 10 minutos) desde o momento em que eles são enviados para os Hubs de Notificação. Algumas notificações podem levar mais tempo.

> [!NOTE]
> Os Hubs de Notificação do Azure têm uma política em vigor para descartar todas as notificações por push que não são entregues ao PNS em 30 minutos. Esse atraso pode acontecer por várias razões, mais comumente porque o PNS está limitando seu aplicativo.
> 
> 

### <a name="is-there-any-latency-guarantee"></a>Há qualquer garantia de latência?
Devido à natureza das notificações por push (entregues por uma plataforma PNS externa específica), não há nenhuma garantia de latência. Normalmente, a maioria das notificações por push é entregue em alguns minutos.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>O que é necessário considerar ao criar uma solução com hubs de notificação e namespaces?
#### <a name="mobile-appenvironment"></a>Ambiente/aplicativo móvel
* Use um hub de notificação por aplicativo móvel, por ambiente.
* Em um cenário multilocatário, cada locatário deve ter um hub separado.
* Nunca compartilham o mesmo hub de notificação para produção e ambientes de teste. Essa prática pode causar problemas ao enviar notificações. (A Apple oferece pontos de extremidade de área restrita e push de produção, cada qual com credenciais separadas).
* Por padrão, você pode enviar notificações de teste para seus dispositivos registrados por meio do portal do Azure ou do componente integrado do Azure no Visual Studio. O limite é definido como 10 dispositivos selecionados aleatoriamente do pool de registro.

> [!NOTE]
> Se o hub foi originalmente configurado com um certificado de área restrita da Apple e, em seguida, foi reconfigurado para usar um certificado de produção da Apple, os tokens de dispositivo original são inválidos. Tokens inválidos causariam envios falhe. Separe a produção e ambientes de teste e usar os hubs diferentes para ambientes diferentes.
> 
> 

#### <a name="pns-credentials"></a>Credenciais PNS
Quando um aplicativo móvel é registrado com o portal do desenvolvedor da plataforma (por exemplo, Apple ou Google), um aplicativo identificador e tokens de segurança são enviadas. O back-end do aplicativo fornece esses tokens para o PNS da plataforma para que as notificações por push podem ser enviadas aos dispositivos. Tokens de segurança podem ser na forma de certificados (por exemplo, Apple iOS ou Windows Phone) ou chaves de segurança (por exemplo, Google Android ou Windows). Eles devem ser configurados em hubs de notificação. A configuração normalmente é feita no nível do hub de notificação, mas também pode ser feito no nível de namespace em um cenário de multilocatário.

#### <a name="namespaces"></a>Namespaces
Os namespaces podem ser usados para o agrupamento de implantação. Eles também podem ser usados para representar todos os hubs de notificação para todos os locatários do mesmo aplicativo em um cenário de multilocatário.

#### <a name="geo-distribution"></a>Distribuição geográfica
A distribuição geográfica nem sempre é crítica em cenários de notificações por push. Vários PNSes (por exemplo, APNS ou GCM) que fornecem notificações por push para dispositivos não são distribuídos uniformemente.

Se você tiver um aplicativo que é usado globalmente, poderá criar hubs em namespaces diferentes usando o serviço de Hubs de Notificação em diferentes regiões do Azure em todo o mundo.

> [!NOTE]
> Não recomendamos essa organização porque aumenta o custo de gerenciamento, especialmente para os registros. Deve ser feito somente se houver uma necessidade de explícita.
> 
> 

### <a name="should-i-do-registrations-from-the-app-back-end-or-directly-through-client-devices"></a>Devo fazer registros do back-end do aplicativo ou diretamente por meio de dispositivos cliente?
Registros de back-end do aplicativo são úteis quando você precisa autenticar clientes antes de criar o registro. Eles também são úteis quando há marcas que devem ser criadas ou modificadas pelo aplicativo back-end com base na lógica do aplicativo. Para saber mais, vá até as páginas [Diretrizes de registro de back-end] e [Diretrizes de registro de back-end 2].

### <a name="what-is-the-push-notification-delivery-security-model"></a>O que é o modelo de segurança de entrega de notificação por push?
Os Hubs de Notificação do Azure usam um modelo de segurança baseado em [assinatura de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Você pode usar os tokens de assinatura de acesso compartilhado no nível do namespace raiz ou no nível granular de hubs de notificação. Os tokens de Assinatura de Acesso Compartilhado podem ser definidos a seguir diferentes regras de autorização, por exemplo, para enviar as permissões de mensagens ou para escutar permissões de notificação de assinatura de acesso. Para saber mais, veja o documento [modelo de segurança de Hubs de Notificação].

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Como lidar com a carga confidencial nas notificações por push?
Todas as notificações são entregues aos dispositivos de destino pelos PNS da plataforma. Quando uma notificação é enviada para Hubs de Notificação do Azure, é processada e passada para o respectivo PNS.

Todas as conexões do remetente para os Hubs de Notificações do Azure e para o PNS usam HTTPS.

> [!NOTE]
> Os Hubs de Notificações do Azure não registram a carga da mensagem de forma alguma.
> 
> 

Para enviar cargas confidenciais, é recomendável usar um padrão Push seguro. O remetente fornece uma notificação de ping com um identificador de mensagem para o dispositivo sem a carga confidencial. Quando o aplicativo no dispositivo recebe a carga, o aplicativo chama uma API segura diretamente para buscar os detalhes da mensagem. Para obter um guia sobre como implementar esse padrão, vá para a página [tutorial de Push Seguro de Hubs de Notificação].

## <a name="operations"></a>Operações
### <a name="what-support-is-provided-for-disaster-recovery"></a>Qual suporte é fornecido para a recuperação de desastre?
Fornecemos uma cobertura de recuperação de desastre de metadados em nosso lado (o nome de Hubs de Notificação, a cadeia de conexão e outras informações fundamentais). Quando um cenário de recuperação de desastre é disparado, os dados de registro são o *único segmento* da infraestrutura de Hubs de Notificação for perdida. Você precisará implementar uma solução para preencher esses dados novamente em seu novo hub após a recuperação:

1. Crie um hub de notificações secundário em um datacenter diferente. É recomendável criar um desde o início para proteger você de um evento de recuperação de desastre que possa afetar sua capacidade de gerenciamento. Você também pode criar um no momento do evento de recuperação de desastre.

2. Popule o hub de notificação secundário com os registros do hub de notificação primário. Não é recomendável tentar manter os registros em ambos os hubs e mantê-los em sincronia, à medida que os registros. Essa prática não funciona bem devido a tendência inerente de registros para expirar no lado do PNS. Os Hubs de Notificação limpa-os enquanto recebe comentários dos PNS sobre registros expirados ou inválidos.  

Temos dois recomendações para back-ends de aplicativo:

* Use um back-end de aplicativo que mantém um determinado conjunto de registros no final. Ele pode realizar uma inserção em massa no hub de notificação secundário.

* Use um back-end de aplicativo que obtém um despejo regular de registros do hub de notificação principal como um backup. Ele pode realizar uma inserção em massa no hub de notificação secundário.

> [!NOTE]
> A funcionalidade Exportação/Importação de Registros disponível na camada Standard é descrita no documento [Importação/Exportação de Registros] .
> 
> 

Se você não tiver um back-end, quando o aplicativo é iniciado nos dispositivos de destino, eles executam um novo registro no hub de notificação secundário. Eventualmente, o hub de notificação secundário terá todos os dispositivos ativos registrados.

Haverá um período de tempo quando os dispositivos com aplicativos não abertos não recebem notificações.

### <a name="is-there-audit-log-capability"></a>Há recursos de log de auditoria?
Todas as operações de Gerenciamento de Hubs de Notificação vão para Logs de Operação expostos no [portal clássico do Azure].

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas
### <a name="what-troubleshooting-capabilities-are-available"></a>Quais recursos de solução de problemas estão disponíveis?
Os Hubs de Notificação do Azure fornecem vários recursos para a solução de problemas, principalmente no cenário de notificações removidas. Para obter detalhes, veja o white paper [Solução de problemas de Hubs de Notificação].

### <a name="what-telemetry-features-are-available"></a>Quais recursos de telemetria estão disponíveis?
Os Hubs de Notificação do Azure permitem a exibição de dados de telemetria no [portal clássico do Azure]. Os detalhes das métricas estão disponíveis na página [Métricas de Hubs de Notificação].

> [!NOTE]
> Notificações de sucesso significam simplesmente que notificações de push foram entregues ao PNS externo (por exemplo, APNS para Apple) ou GCM para Google. É responsabilidade do PNS para entregar as notificações para dispositivos de destino. Normalmente, o PNS não expõe as métricas de entrega para terceiros.  
> 
> 

Também fornecemos a capacidade de exportar a telemetria programaticamente (na camada Standard). Para obter detalhes, veja o [exemplo de métricas de Hubs de Notificação].

[portal clássico do Azure]: https://manage.windowsazure.com
[Preços dos Hubs de Notificação]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Estudo de caso: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Estudo de caso: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Estudo de caso: Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Estudo de caso: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Estudo de caso: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[APIs REST de Hubs de Notificação]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[tutoriais de Introdução aos Hubs de Notificação]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[tutorial de Aplicativos do Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Diretrizes de registro de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Diretrizes de registro de back-end 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[modelo de segurança de Hubs de Notificação]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[tutorial de Push Seguro de Hubs de Notificação]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Solução de problemas de Hubs de Notificação]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Métricas de Hubs de Notificação]: https://msdn.microsoft.com/library/dn458822.aspx
[exemplo de métricas de Hubs de Notificação]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Importação/Exportação de Registros]: https://msdn.microsoft.com/library/dn790624.aspx
[portal do Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Aplicativos Móveis]: https://azure.microsoft.com/services/app-service/mobile/
[preços do serviço de aplicativo]: https://azure.microsoft.com/pricing/details/app-service/
