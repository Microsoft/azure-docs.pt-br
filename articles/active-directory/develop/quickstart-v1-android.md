---
title: Entrada de usuários e chamada à API do Microsoft Graph em um aplicativo Android | Microsoft Docs
description: Saiba como conectar usuários e chamar a API do Microsoft Graph de um aplicativo Android.
services: active-directory
documentationcenter: android
author: rwike77
manager: CelesteDG
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1ebfce2b96da51b9c33646c1626495d280d7b8a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592255"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início Rápido: Entrada de usuários e chamada à API do Microsoft Graph em um aplicativo Android

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Caso esteja desenvolvendo um aplicativo do Android, a Microsoft torna simples e direta a entrada de usuários do Azure Active Directory (Azure AD). O Azure AD permite que seu aplicativo acesse dados de usuário por meio do Microsoft Graph ou sua própria API da Web protegida.

A biblioteca do Android para ADAL (Biblioteca de Autenticação do Azure AD) dá a seu aplicativo a capacidade de começar a usar a [Nuvem do Microsoft Azure](https://azure.microsoft.com/free/cloud-services/) e a [API do Microsoft Graph](https://developer.microsoft.com/graph) dando suporte a [Contas do Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/) usando o padrão do setor OAuth 2.0 e o OpenID Connect.

Neste início rápido, você aprenderá a:

* Obter um token para o Microsoft Graph
* Atualizar um token
* Chamar o Microsoft Graph
* Sair do usuário

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de um locatário do Azure AD no qual possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](quickstart-create-new-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Cenário: Conectar usuários e chamar o Microsoft Graph

![Mostra a topologia do Azure AD e do Android](./media/quickstart-v1-android/active-directory-android-topology.png)

Você pode usar esse aplicativo para todas as contas do Azure AD. Ele dá suporte a cenários de locatário único e multilocatários (discutidos em etapas). Ele demonstra como você pode criar aplicativos para se conectar com os usuários corporativos e acessar seus dados do Azure + O365 por meio do Microsoft Graph. Durante o fluxo de autenticação, os usuários finais precisarão entrar e dar o consentimento para as permissões do aplicativo e, em alguns casos, pode ser preciso que um administrador dê consentimento ao aplicativo. A maioria da lógica neste exemplo mostra como autenticar um usuário final e fazer uma chamada básica para o Microsoft Graph.

## <a name="sample-code"></a>Exemplo de código

Você pode encontrar o código de exemplo completo [no GitHub](https://github.com/Azure-Samples/active-directory-android).

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this,
        AUTHORITY,
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(),
    RESOURCE_ID,
    CLIENT_ID,
    REDIRECT_URI,
    PromptBehavior.Auto,
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="step-1-register-and-configure-your-app"></a>Etapa 1: Registrar e configurar seu aplicativo

Você precisará ter um aplicativo cliente nativo registrado na Microsoft usando o [portal do Azure](https://portal.azure.com).

1. Obtendo o registro do aplicativo
    - Navegue até o [Portal do Azure](https://aad.portal.azure.com).
    - Selecione ***Azure Active Directory*** > ***Registros de Aplicativo***.

2. Crie o aplicativo
    - Selecione **Novo registro**.
    - Insira um nome de aplicativo no campo **Nome**.
    - Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
    - Em **URI de Redirecionamento**, escolha **Cliente público (dispositivo móvel e desktop)** no menu suspenso e insira `http://localhost`.
    - Clique em **Registrar**.

3. Configurar o Microsoft Graph
    - Selecione **Permissões de API**.
    - Escolha **Adicionar uma permissão** e, em **Selecionar uma API**, escolha ***Microsoft Graph***.
    - Em **Permissões delegadas**, escolha a permissão **User.Read** e pressione **Adicionar** para salvar.        
    
4. Parabéns! Seu aplicativo foi configurado com êxito. Na próxima seção, você precisará:
    - `Application ID`
    - `Redirect URI`

## <a name="step-2-get-the-sample-code"></a>Etapa 2: Obter o código de amostra

1. Clonar o código.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Abrir o exemplo no Android Studio.
    - Selecionar **Abrir um projeto existente do Android Studio**.

## <a name="step-3-configure-your-code"></a>Etapa 3: Configurar seu código

Você pode encontrar todas as configurações deste exemplo de código no arquivo ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java***.

1. Substitua a constante `CLIENT_ID` por `ApplicationID`.
2. Substitua a constante `REDIRECT URI` pelo `Redirect URI` configurado anteriormente (`http://localhost`).

## <a name="step-4-run-the-sample"></a>Etapa 4: Execute o exemplo

1. Selecione **Compilar > Limpar Projeto**.
2. Selecione **Executar > Executar aplicativo**.
3. O aplicativo deve criar e mostrar alguns UX básicos. Quando você clica no botão `Call Graph API`, ele solicitará a entrada e chamará silenciosamente a API do Microsoft Graph com o novo token.

## <a name="next-steps"></a>Próximas etapas

1. Confira a [Wiki do Android para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) para obter mais informações sobre a mecânica da biblioteca e como configurar novos cenários e recursos.
2. Em cenários nativos, o aplicativo usará um Webview integrado e não sairá do aplicativo. O `Redirect URI` pode ser arbitrário.
3. Encontrou problemas ou tem solicitações? Você pode abrir um tíquete ou postar no Stack Overflow com a marca `azure-active-directory`.

### <a name="cross-app-sso"></a>SSO entre aplicativos

Saiba [como habilitar o SSO entre aplicativos no Android usando a ADAL](howto-v1-enable-sso-android.md).

### <a name="auth-telemetry"></a>Telemetria de autenticação

A biblioteca ADAL expõe a telemetria de autenticação para ajudar os desenvolvedores de aplicativo a entenderem como seus aplicativos estão se comportando para criar melhores experiências. Isso permite que você capture entradas com êxito, usuários ativos e vários outros insights interessantes. O uso da telemetria de autenticação requer que os desenvolvedores de aplicativo estabeleçam um serviço de telemetria para agregar e armazenar eventos.

Para saber mais sobre a telemetria de autenticação, confira [telemetria de autenticação do Android para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry).
