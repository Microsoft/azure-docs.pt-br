---
title: Suporte a políticas de logon único e proteção de aplicativo em aplicativos móveis que você desenvolve | Azure
titleSuffix: Microsoft identity platform
description: Explicação e visão geral da criação de aplicativos móveis que dão suporte a políticas de logon único e de proteção de aplicativo usando a plataforma de identidade da Microsoft e a integração com o Azure Active Directory.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 4f0588667df6acb11a43e8c3469c67f65ed3cdd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165171"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>Suporte a políticas de logon único e proteção de aplicativo em aplicativos móveis que você desenvolve

O SSO (logon único) é uma importante oferta da plataforma de identidade da Microsoft e Azure Active Directory, fornecendo logons fáceis e seguros para usuários do seu aplicativo. Além disso, as políticas de proteção de aplicativo (aplicativo) permitem o suporte das principais políticas de segurança que mantêm os dados do usuário seguros. Juntos, esses recursos permitem proteger logons de usuário e gerenciamento dos dados do seu aplicativo.

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

Este artigo explica por que o SSO e o aplicativo são importantes e fornece as diretrizes de alto nível para a criação de aplicativos móveis que dão suporte a esses recursos. Isso se aplica a aplicativos de telefone e Tablet. Se você for um administrador de ti que deseja implantar o SSO no locatário de Azure Active Directory da sua organização, confira nossas [diretrizes para planejar uma implantação de logon único](../manage-apps/plan-sso-deployment.md)

## <a name="about-single-sign-on-and-app-protection-policies"></a>Sobre as políticas de logon único e de proteção de aplicativo

O [SSO (logon único)](../manage-apps/plan-sso-deployment.md) permite que um usuário entre uma vez e obtenha acesso a outros aplicativos sem digitar as credenciais novamente. Isso facilita o acesso a aplicativos e elimina a necessidade de os usuários lembrarem listas longas de nomes de usuário e senhas. Implementá-lo em seu aplicativo facilita o acesso e o uso do seu aplicativo.

Além disso, habilitar o logon único em seu aplicativo desbloqueia novos mecanismos de autenticação que acompanham a autenticação moderna, como [logons sem senha](../authentication/concept-authentication-passwordless.md). Nomes de usuários e senhas são um dos vetores de ataque mais populares em relação aos aplicativos, e habilitar o SSO permite que você atenue esse risco impondo o acesso condicional ou logons sem senha que adicionam segurança adicional ou dependem de mecanismos de autenticação mais seguros. Por fim, habilitar o logon único também habilita o [logout único](v2-protocols-oidc.md#single-sign-out). Isso é útil em situações como aplicativos de trabalho que serão usados em dispositivos compartilhados.

[As políticas de proteção de aplicativo (aplicativo)](/mem/intune/apps/app-protection-policy) garantem que os dados de uma organização permaneçam seguros e contidos. Eles permitem que as empresas gerenciem e protejam seus dados em um aplicativo e permitem o controle sobre quem pode acessar o aplicativo e seus dados. A implementação de políticas de proteção de aplicativo permite que seu aplicativo Conecte usuários a recursos protegidos por políticas de acesso condicional e transfira dados com segurança para e de outros aplicativos protegidos. Cenários desbloqueados pelas políticas de proteção de aplicativo incluem exigir um PIN para abrir um aplicativo, controlar o compartilhamento de dados entre aplicativos e impedir que os dados de aplicativo da empresa sejam salvos em locais de armazenamento pessoal.

## <a name="implementing-single-sign-on"></a>Implementando o logon único

Recomendamos o seguinte para permitir que seu aplicativo aproveite o logon único.

### <a name="use-the-microsoft-authentication-library-msal"></a>Usar a MSAL (biblioteca de autenticação da Microsoft)

A melhor opção para implementar o logon único em seu aplicativo é usar [a MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md). Usando o MSAL, você pode adicionar autenticação ao seu aplicativo com código mínimo e chamadas à API, obter os recursos completos da [plataforma de identidade da Microsoft](./index.yml)e deixar que a Microsoft manipule a manutenção de uma solução de autenticação segura. Por padrão, o MSAL adiciona suporte de SSO para seu aplicativo. Além disso, o uso de MSAL é um requisito se você também planeja implementar políticas de proteção de aplicativo.

> [!NOTE]
> É possível configurar o MSAL para usar uma exibição da Web inserida. Isso impedirá o logon único. Use o comportamento padrão (ou seja, o navegador da Web do sistema) para garantir que o SSO funcionará.

Se você estiver usando atualmente a [biblioteca Adal](../azuread-dev/active-directory-authentication-libraries.md) em seu aplicativo, é altamente recomendável [migrá-la para MSAL](msal-migration.md), pois a [Adal está sendo preterida](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363).

Para aplicativos iOS, temos um guia de [início rápido](quickstart-v2-ios.md) que mostra como configurar entradas usando o MSAL, bem como [diretrizes para configurar o MSAL para vários cenários de SSO](single-sign-on-macos-ios.md).

Para aplicativos Android, temos um guia de [início rápido](quickstart-v2-android.md) que mostra como configurar entradas usando o MSAL e orientação sobre [como habilitar o SSO entre aplicativos no Android usando o MSAL](msal-android-single-sign-on.md).

### <a name="use-the-system-web-browser"></a>Usar o navegador da Web do sistema

Um navegador da Web é necessário para a autenticação interativa. Para aplicativos móveis que usam bibliotecas de autenticação modernas diferentes de MSAL (ou seja, outras bibliotecas de OpenID Connect ou SAML) ou, se você implementar seu próprio código de autenticação, deverá usar o navegador do sistema como sua superfície de autenticação para habilitar o SSO.

O Google tem diretrizes para fazer isso em aplicativos Android: [guias personalizadas do Chrome – Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

A Apple tem orientação para fazer isso em aplicativos iOS: [autenticar um usuário por meio de um serviço Web | Documentação do desenvolvedor da Apple](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service).

> [!TIP]
> O [plug-in de SSO para dispositivos Apple](apple-sso-plugin.md) permite o SSO para aplicativos Ios que usam exibições da Web inseridas em dispositivos gerenciados usando o Intune. Recomendamos o MSAL e o navegador do sistema como a melhor opção para o desenvolvimento de aplicativos que habilitam o SSO para todos os usuários, mas isso permitirá o SSO em alguns cenários em que caso contrário não seja possível.

## <a name="enable-app-protection-policies"></a>Habilitar políticas de proteção de aplicativo

Para habilitar as políticas de proteção de aplicativo, use a [MSAL (biblioteca de autenticação da Microsoft)](msal-overview.md). MSAL é a biblioteca de autenticação e autorização da plataforma de identidade da Microsoft e o SDK do Intune é desenvolvido para funcionar em conjunto com ele.

Além disso, você deve usar um aplicativo agente para autenticação. O agente requer que o aplicativo forneça informações de aplicativo e dispositivo para garantir a conformidade do aplicativo. os usuários do iOS usarão o [Microsoft Authenticator aplicativo](../user-help/user-help-auth-app-sign-in.md) e os usuários do Android usarão o aplicativo Microsoft Authenticator ou o [aplicativo portal da empresa](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) para [autenticação orientada](./msal-android-single-sign-on.md). Por padrão, o MSAL usa um agente como sua primeira opção para atender a uma solicitação de autenticação, portanto, usar o agente para autenticar será habilitado para seu aplicativo automaticamente ao usar o MSAL pronto para uso.

Por fim, [adicione o SDK do Intune](/mem/intune/developer/app-sdk-get-started) ao seu aplicativo para habilitar as políticas de proteção do aplicativo. O SDK da maior parte segue um modelo de interceptação e aplicará automaticamente as políticas de proteção de aplicativo para determinar se as ações que o aplicativo está assumindo são permitidas ou não. Também há APIs que você pode chamar manualmente para informar ao aplicativo se há restrições em determinadas ações.

## <a name="additional-resources"></a>Recursos adicionais

- [Planejar uma implantação de logon único do Azure Active Directory](../manage-apps/plan-sso-deployment.md)
- [Como: configurar o SSO no macOS e no iOS](single-sign-on-macos-ios.md)
- [Plug-in do SSO corporativo da Microsoft para dispositivos Apple (versão prévia)](apple-sso-plugin.md)
- [Autenticação orientada no Android](./msal-android-single-sign-on.md)
- [Agentes de autorização e como habilitá-los](./msal-android-single-sign-on.md)
- [Introdução ao SDK do Aplicativo do Microsoft Intune](/mem/intune/developer/app-sdk-get-started)
- [Definir as configurações para o SDK de Aplicativos do Intune](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Aplicativos protegidos do Microsoft Intune](/mem/intune/apps/apps-supported-intune-apps)
