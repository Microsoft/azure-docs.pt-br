---
title: "Adicionar notificações por push ao aplicativo Android com os Aplicativos Móveis | Microsoft Docs"
description: "Saiba como usar os Aplicativos Móveis para enviar notificações por push ao seu aplicativo Android."
services: app-service\mobile
documentationcenter: android
manager: syntaxc4
editor: 
author: ggailey777
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/12/2016
ms.author: glenga
ms.openlocfilehash: b89e9af55342d5d7473d848956996f846250b4b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-android-app"></a>Adicionar notificações por push ao aplicativo Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Visão geral
Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Android] para que, sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto baixado do início rápido do servidor, deverá adicionar o pacote de extensão de notificação por push ao seu projeto. Para saber mais, veja [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
Você precisará do seguinte:

* Uma IDE, dependendo do back-end do projeto:

  * [Android Studio](https://developer.android.com/sdk/index.html), se esse aplicativo tiver um back-end do Node.js.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou posterior, se esse aplicativo tiver um back-end do Microsoft .NET.
* Android 2.3 ou posterior, Google Repository revisão 27 ou posterior e Google Play Services 9.0.2 ou posterior para o Firebase Cloud Messaging.
* Conclua o [início rápido do Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Criar um projeto que ofereça suporte ao Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar um hub de notificação
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Em seguida, habilite as notificações por push para o projeto de servidor
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações de push para seu aplicativo
Nesta seção, você atualizará seu aplicativo de cliente Android para manipular notificações por push.

### <a name="verify-android-sdk-version"></a>Verificar a versão do SDK do Android
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

A próxima etapa é instalar os serviços do Google Play. O Google Cloud Messaging tem alguns requisitos mínimos de nível de API para desenvolvimento e teste, com os quais a propriedade **minSdkVersion** no manifesto deve estar em conformidade.

Se estiver testando com um dispositivo mais antigo, consulte [Set Up Google Play Services SDK] (Configurar a SDK do Google Play Services) para determinar o mínimo que esse valor pode ser definido e defina-o de maneira apropriada.

### <a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play ao projeto
[!INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Incluir código
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testar o aplicativo no serviço móvel publicado
Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu este tutorial, considere continuar com um dos seguintes tutoriais:

* [Adicionar autenticação ao aplicativo Android](app-service-mobile-android-get-started-users.md).
  Saiba como adicionar autenticação ao projeto de início rápido da lista de tarefas pendentes no Android usando um provedor de identidade com suporte.
* [Habilitar a sincronização offline para o aplicativo móvel Android](app-service-mobile-android-get-started-offline-data.md).
  Saiba como adicionar suporte offline ao aplicativo usando um back-end dos Aplicativos Móveis. Com a sincronização offline, os usuários podem interagir com um aplicativo móvel &mdash; exibindo, adicionando ou modificando dados &mdash; mesmo quando não há nenhuma conexão de rede.

<!-- URLs -->
[início rápido do Android]: app-service-mobile-android-get-started.md

[Set Up Google Play Services SDK]:https://developers.google.com/android/guides/setup
