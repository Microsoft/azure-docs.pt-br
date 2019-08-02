---
title: Sobre Aplicativos Móveis no Serviço de Aplicativo do Azure
description: Saiba mais sobre as vantagens que o Serviço de Aplicativo traz para seus aplicativos móveis corporativos.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 55f33821a748789fb11b44dc08c367961b4258f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440285"
---
# <a name="getting-started"> </a>Sobre Aplicativos Móveis no Serviço de Aplicativo do Azure

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-value-prop) hoje mesmo.
>

O Serviço de Aplicativo do Azure é uma oferta de [PaaS](https://azure.microsoft.com/overview/what-is-paas/) (plataforma como um serviço) para desenvolvedores profissionais. O serviço oferece um conjunto avançado de recursos para cenários Web, móveis e de integração. 

O recurso Aplicativos Móveis do Serviço de Aplicativo do Azure dá aos desenvolvedores empresariais e integradores de sistema uma plataforma de desenvolvimento de aplicativos móveis que é altamente dimensionável e globalmente disponível.

![Visão geral dos recursos dos Aplicativos Móveis](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Por que os aplicativos móveis?
Com o recurso Aplicativos Móveis, você pode:

* **Criar aplicativos nativos e multiplataforma**: Se você estiver criando aplicativos nativos do iOS, do Android e do Windows ou aplicativos multiplataforma do Xamarin ou do Cordova (PhoneGap), poderá aproveitar o Serviço de Aplicativo usando SDKs nativos.
* **Conectar-se a seus sistemas empresariais**: Com o recurso Aplicativos Móveis, você pode adicionar credenciais corporativas em minutos e se conectar aos recursos locais ou de nuvem de sua empresa.
* **Criar aplicativos prontos para uso offline com a sincronização de dados**: Torne sua força de trabalho móvel mais produtiva por meio da criação de aplicativos que trabalham offline e use Aplicativos Móveis para sincronizar dados em segundo plano quando houver conectividade com uma de suas fontes de dados empresariais ou APIs SaaS (software como serviço).
* **Enviar notificações por push para milhões em segundos**: Atraia seus clientes com notificações por push instantâneas em qualquer dispositivo, personalizadas de acordo com as necessidades deles e enviadas na hora certa.

## <a name="mobile-apps-features"></a>Recursos dos Aplicativos Móveis
Os recursos a seguir são importantes para o desenvolvimento móvel habilitado para nuvem:

* **Autenticação e autorização**: Suporte para provedores de identidade, incluindo Azure Active Directory para autenticação corporativa, além de provedores sociais, como Facebook, Google, Twitter e contas Microsoft. Os Aplicativos Móveis oferecem um serviço OAuth 2.0 para cada provedor. Você também pode integrar o SDK do provedor de identidade à funcionalidade específica do provedor.

    Descubra mais sobre os [recursos de autenticação].

* **Acesso a dados**: Os Aplicativos Móveis fornecem uma fonte de dados OData v3 compatível com dispositivos móveis que é vinculada ao Banco de Dados SQL do Azure ou a um SQL Server local. Como esse serviço pode ser baseado no Entity Framework, você pode integrar facilmente com outros provedores de dados NoSQL e SQL, incluindo o [Armazenamento de Tabelas do Azure], o MongoDB, o [Azure Cosmos DB] e os provedores de API SaaS, como o Office 365 e o Salesforce.com.

* **Sincronização offline**: Os SDKs de cliente facilitam a criação de aplicativos móveis robustos e dinâmicos que funcionam com um conjunto de dados offline. Você pode sincronizar esse conjunto de dados automaticamente com os dados de back-end, incluindo o suporte a resolução de conflitos.

  Descubra mais sobre os [recursos de dados].

* **Notificações por push**: Os SDKs de cliente são perfeitamente integrados às funcionalidades de registro dos Hubs de Notificação do Azure, permitindo que você envie notificações por push para milhões de usuários simultaneamente.

  Descubra mais sobre os [recursos de notificação por push].

* **SDKs de cliente**: Há um conjunto completo de SDKs de cliente que abrangem o desenvolvimento nativo ([iOS], [Android] e [Windows]), o desenvolvimento multiplataforma ([Xamarin.iOS ou Xamarin.Android], [Xamarin.Forms]) e o desenvolvimento de aplicativos híbridos ([Apache Cordova]). Cada SDK de cliente está disponível com uma licença MIT e é software livre.

## <a name="azure-app-service-features"></a>Recursos do Serviço de Aplicativo do Azure
Os recursos de plataforma abaixo são úteis para sites de produção móvel:

* **Dimensionamento automático**: Com o Serviço de Aplicativo, você pode escalar verticalmente ou expandir rapidamente para lidar com todas as cargas de clientes de entrada. Selecione o número e o tamanho das VMs manualmente ou configure o dimensionamento automático para dimensionar seu back-end de aplicativo móvel com base na carga ou a agenda.

  Descubra mais sobre o [dimensionamento automático].

* **Ambientes de preparo**: O Serviço de Aplicativo pode executar várias versões de seu site, permitindo que você execute um teste A/B, um teste em produção como parte de um plano de DevOps maior e o preparo in-loco de um novo back-end.

  Descubra mais sobre [ambientes de preparo].

* **Implantação contínua**: O Serviço de Aplicativo pode ser integrado a sistemas comuns de _SCM_ (gerenciamento de controle do código-fonte), permitindo que você implante com facilidade uma nova versão do back-end.

  Descubra mais sobre [opções de implantação](../app-service/deploy-local-git.md).

* **Rede virtual**: O Serviço de Aplicativo pode se conectar a recursos locais usando a rede virtual, o Azure ExpressRoute ou conexões híbridas.

  Descubra mais sobre as [conexões híbridas], as [redes virtuais] e o [ExpressRoute].

* **Ambientes isolados e dedicados**: Para executar aplicativos do Serviço de Aplicativo do Azure com segurança, execute o Serviço de Aplicativo em um ambiente totalmente isolado e dedicado. Esse ambiente é ideal para cargas de trabalho de aplicativos que exigem escala alta, isolamento ou acesso seguro à rede.

  Descubra mais sobre os [Ambientes do Serviço de Aplicativo].

## <a name="next-steps"></a>Próximas etapas

Para começar a usar os Aplicativos Móveis no Serviço de Aplicativo do Azure, conclua o tutorial de [introdução]. O tutorial abrange os fundamentos da produção de um back-end móvel e um cliente de sua escolha. Ele também aborda a integração de autenticação, sincronização offline e notificações por push. Você pode concluir o tutorial várias vezes, uma vez para cada aplicativo cliente.

Para saber mais sobre Aplicativos Móveis, reveja nosso [mapa de aprendizado].
Para saber mais sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Introdução]: app-service-mobile-ios-get-started.md
[Armazenamento de Tabelas do Azure]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[recursos de autenticação]: ./app-service-mobile-auth.md
[recursos de dados]: ./app-service-mobile-offline-data-sync.md
[recursos de notificação por push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS ou Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[Dimensionamento automático]: ../app-service/web-sites-scale.md
[ambientes de preparo]: ../app-service/deploy-staging-slots.md
[conexões híbridas]: ../biztalk-services/integration-hybrid-connection-overview.md
[redes virtuais]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[Ambientes do Serviço de Aplicativo]: ../app-service/environment/intro.md
[mapa de aprendizado]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
