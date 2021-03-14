---
title: Considerações sobre UWP (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as considerações para usar o Plataforma Universal do Windows (UWP) com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8a8aab447007eb574a7a4bc532d8177bd0d8b345
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122469"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Considerações sobre o uso de Plataforma Universal do Windows com MSAL.NET
Os desenvolvedores de aplicativos que usam o Plataforma Universal do Windows (UWP) com MSAL.NET devem considerar os conceitos apresentados neste artigo.

## <a name="the-usecorporatenetwork-property"></a>A propriedade UseCorporateNetwork
Na plataforma Windows Runtime (WinRT), `PublicClientApplication` tem a propriedade booliana `UseCorporateNetwork` . Essa propriedade permite que aplicativos do Windows 10 e aplicativos UWP se beneficiem da autenticação integrada do Windows (IWA) se o usuário estiver conectado a uma conta que tenha um locatário Federated Azure Active Directory (AD do Azure). Os usuários que entraram no sistema operacional também podem usar o SSO (logon único). Quando você define a `UseCorporateNetwork` propriedade, MSAL.NET usa um wab (agente de autenticação da Web).

> [!IMPORTANT]
> Definir a `UseCorporateNetwork` propriedade como true pressupõe que o desenvolvedor do aplicativo habilitou o IWA no aplicativo. Para habilitar o IWA:
> - Nos seus aplicativos UWP `Package.appxmanifest` , na guia **recursos** , habilite os seguintes recursos:
>   - **Autenticação de Empresa**
>   - **Redes Privadas (Cliente e Servidor)**
>   - **Certificado de usuário compartilhado**

O IWA não está habilitado por padrão porque Microsoft Store requer um alto nível de verificação antes de aceitar aplicativos que solicitam os recursos de autenticação corporativa ou certificados de usuário compartilhados. Nem todos os desenvolvedores querem fazer esse nível de verificação.

Na plataforma UWP, a implementação do WAB subjacente não funciona corretamente em cenários empresariais em que o acesso condicional está habilitado. Os usuários veem os sintomas desse problema quando tentam entrar usando o Windows Hello. Quando o usuário for solicitado a escolher um certificado:

- O certificado para o PIN não foi encontrado.
- Depois que o usuário escolher um certificado, ele não será solicitado a fornecer o PIN.

Você pode tentar evitar esse problema usando um método alternativo, como senha de nome de usuário e autenticação por telefone, mas a experiência não é boa.

## <a name="troubleshooting"></a>Solução de problemas

Alguns clientes relataram o seguinte erro de entrada em ambientes corporativos específicos nos quais sabem que eles têm uma conexão com a Internet e que a conexão funciona com uma rede pública.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Você pode evitar esse problema verificando se o WAB (o componente subjacente do Windows) permite uma rede privada. Você pode fazer isso definindo uma chave do registro:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Para obter mais informações, consulte [agente de autenticação da Web-Fiddler](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Próximas etapas
Os exemplos a seguir fornecem mais informações.

Amostra | Plataforma | Descrição 
|------ | -------- | -----------|
|[Active-Directory-dotnet-Native-UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Um aplicativo cliente UWP que usa MSAL.NET. Ele acessa Microsoft Graph para um usuário que se autentica usando um ponto de extremidade 2,0 do Azure AD. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-xamarin-Native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Um aplicativo Xamarin Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e o Azure AD por meio da plataforma Microsoft Identity. Ele também mostra como acessar Microsoft Graph e mostra o token resultante. <br>![Diagrama que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e o Azure AD por meio da plataforma Microsoft Identity.](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
