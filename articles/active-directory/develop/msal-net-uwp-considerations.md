---
title: Considerações sobre Plataforma Universal do Windows (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba mais sobre considerações específicas ao usar Plataforma Universal do Windows com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d78a64ee41e37fe53eba20eab6753c0b6eb8389
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277913"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Considerações específicas de Plataforma Universal do Windows com MSAL.NET
No UWP, há várias considerações que você deve levar em conta ao usar o MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>A propriedade UseCorporateNetwork
Na plataforma WinRT, `PublicClientApplication` tem a seguinte propriedade ``UseCorporateNetwork``booliana. Essa propriedade permite que os aplicativos win 8.1 e UWP se beneficiem da autenticação integrada do Windows (e, portanto, SSO com o usuário conectado com o sistema operacional) se o usuário estiver conectado com uma conta em um locatário federado do Azure AD. Quando você define essa propriedade, o MSAL.NET aproveita o WAB (agente de autenticação da Web).

> [!IMPORTANT]
> Definir essa propriedade como true pressupõe que o desenvolvedor do aplicativo habilitou a autenticação integrada do Windows (IWA) no aplicativo. Para isso:
> - No para seu aplicativo UWP, na guia recursos, habilite os seguintes recursos:  ``Package.appxmanifest``
>   - Autenticação de Empresa
>   - Redes Privadas (Cliente e Servidor)
>   - Certificado de usuário compartilhado

O IWA não está habilitado por padrão porque os aplicativos que solicitam os recursos de autenticação corporativa ou certificados de usuário compartilhados exigem um nível mais alto de verificação para serem aceitos na Windows Store, e nem todos os desenvolvedores podem querer executar o mais alto nível de verificação.

A implementação subjacente na plataforma UWP (WAB) não funciona corretamente em cenários empresariais em que o acesso condicional foi habilitado. O sintoma é que o usuário tenta entrar com o Windows Hello e é proposto para escolher um certificado, mas:

- o certificado para o PIN não foi encontrado,
- ou o usuário o escolhe, mas nunca receberá uma solicitação para o PIN.

Uma solução alternativa é usar um método alternativo (nome de usuário/senha + autenticação por telefone), mas a experiência não é boa.

## <a name="troubleshooting"></a>Solução de problemas

Alguns clientes relataram que, em alguns ambientes corporativos específicos, houve o seguinte erro de entrada:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

enquanto eles sabem que têm uma conexão com a Internet, e isso funciona com uma rede pública.

Uma solução alternativa é verificar se o WAB (o componente subjacente do Windows) permite a rede privada. Você pode fazer isso definindo uma chave do registro:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Para obter detalhes, consulte [agente de autenticação da Web-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Próximas etapas
Mais detalhes são fornecidos nos seguintes exemplos:

Amostra | Plataforma | DESCRIÇÃO 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Um aplicativo cliente Plataforma Universal do Windows usando o msal.net, acessando o Microsoft Graph para uma autenticação de usuário com o ponto de extremidade v 2.0 do Azure AD. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Um aplicativo Xamarin Forms simples mostrando como usar o MSAL para autenticar o MSA e o Azure AD por meio do ponto de extremidade do AAD v 2.0 e acessar o Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
