---
title: Adicionar autenticação no Android com os Aplicativos Móveis| Microsoft Docs
description: Saiba como usar o recurso Aplicativos Móveis do Serviço de Aplicativo do Azure para autenticar usuários de seu aplicativo Android por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f138911510db4e6839ff96317fa6004e449e58be
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443591"
---
# <a name="add-authentication-to-your-android-app"></a>Adicionar autenticação ao aplicativo do Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-users) hoje mesmo.
>

## <a name="summary"></a>Resumo
Neste tutorial, você adiciona autenticação ao projeto de início rápido da lista de tarefas pendentes no Android usando um provedor de identidade com suporte. Este tutorial se baseia no tutorial [Introdução aos Aplicativos Móveis] , que você deve concluir primeiro.

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo do Azure
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos sempre o esquema de URL _appname_. No entanto, você pode usar o esquema de URL que quiser. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure], selecione sua conta.

2. Clique na opção de menu **Autenticação/Autorização**.

3. Em **URLs de Redirecionamento Externo Permitidas**, insira `appname://easyauth.callback`.  O _appname_ na cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir as especificações de URL normal para um protocolo (use somente letras e números e inicie com uma letra).  Você deve anotar a cadeia de caracteres escolhida, já que precisará ajustar o código do aplicativo móvel com o esquema de URL em vários lugares.

4. Clique em **OK**.

5. Clique em **Salvar**.

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Android Studio, abra o projeto concluído com o tutorial [Introdução aos Aplicativos Móveis]. No menu **Executar**, clique em **Executar aplicativo** e verifique se uma exceção sem tratamento com um código de status 401 (Não autorizado) é acionada depois que o aplicativo é iniciado.

     Essa exceção ocorre porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualiza o aplicativo para autenticar os usuários antes de solicitar recursos do back-end dos Aplicativos Móveis.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação ao aplicativo
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Armazenar em cache tokens de autenticação no cliente
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

* [Adicionar notificações por push ao aplicativo Android](app-service-mobile-android-get-started-push.md).
  Saiba como configurar o back-end dos Aplicativos Móveis para usar os hubs de notificação do Azure para enviar notificações por push.
* [Habilitar a sincronização offline para o aplicativo móvel Android](app-service-mobile-android-get-started-offline-data.md).
  Saiba como adicionar suporte offline ao aplicativo usando um back-end dos Aplicativos Móveis. Com a sincronização offline, os usuários podem interagir com um aplicativo móvel &mdash; exibindo, adicionando ou modificando dados &mdash; mesmo quando não há nenhuma conexão de rede.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introdução aos Aplicativos Móveis]: app-service-mobile-android-get-started.md
[Portal do Azure]: https://portal.azure.com/
