---
title: Autenticação de área de trabalho virtual do Windows – Azure
description: Métodos de autenticação para área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04a4366bfee6b1d9c5f52d649910163269962684
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709250"
---
# <a name="supported-authentication-methods"></a>Métodos de autenticação com suporte

Neste artigo, daremos uma breve visão geral de quais tipos de autenticação você pode usar na área de trabalho virtual do Windows.

## <a name="session-host-authentication"></a>Autenticação de host de sessão

A área de trabalho virtual do Windows dá suporte ao NTLM (NT LAN Manager) e ao Kerberos para autenticação de host de sessão. No entanto, para usar o Kerberos, o cliente precisa obter tíquetes de segurança Kerberos de um serviço centro de distribuição de chaves (KDC) em execução em um controlador de domínio. Para obter tíquetes, o cliente precisa de uma linha de visão direta para o controlador de domínio. Você pode obter uma linha de visão direta usando sua rede corporativa. Você também pode usar uma conexão VPN para sua rede corporativa ou configurar um [servidor proxy KDC](key-distribution-center-proxy.md).

Estes são os métodos de entrada com suporte no momento:

- Cliente de Área de Trabalho do Windows
    - Nome de usuário e senha
    - Smartcard
    - Windows Hello para empresas (somente certificado confiável)
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
>O cartão inteligente e o Windows Hello para empresas só podem usar o Kerberos para entrar. Entrar com o Kerberos requer a linha de visão para o controlador de domínio ou um [servidor proxy KDC](key-distribution-center-proxy.md).

## <a name="hybrid-identity"></a>Identidade híbrida

A área de trabalho virtual do Windows dá suporte a [identidades híbridas](../active-directory/hybrid/whatis-hybrid-identity.md) por meio do Azure Active Directory (AD), incluindo aquelas que usam serviços de Federação do Active Directory (AD FS) (ADFS). Como os usuários devem ser detectáveis por meio do Azure AD, a área de trabalho virtual do Windows não dá suporte a implantações Active Directory autônomas com o ADFS.

## <a name="single-sign-on-sso"></a>SSO (logon único)

A área de trabalho virtual do Windows atualmente não dá suporte a Serviços de Federação do Active Directory (AD FS) (ADFS) para SSO.

A única maneira de evitar a solicitação de suas credenciais para o host de sessão é salvá-las no cliente. Recomendamos que você faça isso apenas com dispositivos seguros para impedir que outros usuários acessem seus recursos.

## <a name="next-steps"></a>Próximas etapas

Curioso sobre outras maneiras de manter sua implantação segura? Confira [as práticas recomendadas de segurança](security-guide.md).
