---
title: Autenticação de área de trabalho virtual do Windows – Azure
description: Métodos de autenticação para área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5681228e5da2708912d69f16a4b09a4a93d8bb04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500293"
---
# <a name="supported-authentication-methods"></a>Métodos de autenticação com suporte

Neste artigo, daremos uma breve visão geral de quais tipos de autenticação você pode usar na área de trabalho virtual do Windows.

## <a name="session-host-authentication"></a>Autenticação de host de sessão

A área de trabalho virtual do Windows dá suporte ao NTLM (NT LAN Manager) e ao Kerberos para autenticação de host de sessão. No entanto, para usar o Kerberos, o cliente precisa obter tíquetes de segurança Kerberos de um serviço centro de distribuição de chaves (KDC) em execução em um controlador de domínio. Para obter tíquetes, o cliente precisa de uma linha de visão direta para o controlador de domínio. Você pode obter uma linha de visão direta usando sua rede corporativa. Você também pode usar uma conexão VPN para sua rede corporativa.

Estes são os métodos de entrada com suporte no momento:

- Cliente de Área de Trabalho do Windows
    - Nome de usuário e senha
    - Smartcard
    - Windows Hello
- Cliente para Microsoft Store
    - Nome de usuário e senha
- Cliente Web
    - Nome de usuário e senha
- Android
    - Nome de usuário e senha
- iOS
    - Nome de usuário e senha
- macOS
    - Nome de usuário e senha

>[!NOTE]
>O cartão inteligente e o Windows Hello só podem usar o Kerberos para entrar. Entrar com o Kerberos requer a linha de visão para o controlador de domínio.

## <a name="hybrid-identity"></a>Identidade híbrida

A área de trabalho virtual do Windows dá suporte a [identidades híbridas](../active-directory/hybrid/whatis-hybrid-identity.md) por meio do Azure Active Directory (AD), incluindo aquelas que usam serviços de Federação do Active Directory (AD FS) (ADFS). Como os usuários devem ser detectáveis por meio do Azure AD, a área de trabalho virtual do Windows não dá suporte a implantações Active Directory autônomas com o ADFS.

## <a name="single-sign-on-sso"></a>SSO (logon único)

A área de trabalho virtual do Windows atualmente não dá suporte a Serviços de Federação do Active Directory (AD FS) (ADFS) para SSO.

A única maneira de evitar a solicitação de suas credenciais para o host de sessão é salvá-las no cliente. Recomendamos que você faça isso apenas com dispositivos seguros para impedir que outros usuários acessem seus recursos.

## <a name="next-steps"></a>Próximas etapas

Curioso sobre outras maneiras de manter sua implantação segura? Confira [as práticas recomendadas de segurança](security-guide.md).
