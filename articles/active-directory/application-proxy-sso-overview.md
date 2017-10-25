---
title: "Gerenciar logon único para o Proxy de Aplicativo do Azure AD | Microsoft Docs"
description: "Saiba mais sobre os conceitos básicos do logon único com o Proxy de Aplicativo"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1deb3d91049d45fe26791783e13bd23e0a7d9f95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Como o Proxy de Aplicativo do Azure AD fornece logon único?

O logon único é um elemento fundamental do Proxy de Aplicativo Azure AD.  Ele fornece a melhor experiência de usuário porque os usuários só precisam entrar no Azure Active Directory na nuvem. Depois que se autenticarem no Azure Active Directory, o conector do Proxy de Aplicativo lida com a autenticação do aplicativo local. O aplicativo de back-end não pode determinar a diferença entre um usuário remoto entrando por meio do Proxy de Aplicativo e o uso comum em um dispositivo associado ao domínio. 

Para usar o Azure Active Directory para logon único a seus aplicativos, você precisa selecionar **Azure Active Directory** como método de pré-autenticação. Se você selecionar **Passagem**, seus os usuários não se autenticam no Azure Active Directory, mas são direcionados diretamente para o aplicativo. Você pode configurar essa configuração quando publica um aplicativo pela primeira vez ou navega até o aplicativo no portal do Azure e edita as configurações de Proxy de Aplicativo. 

Para ver as opções de logon único, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Navegue até **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os Aplicativos**.
3. Selecione o aplicativo cujas opções de logon único você quer gerenciar.
4. Selecione **Logon único**.

   ![Menu suspenso de logon único](./media/application-proxy-sso-overview/single-sign-on-mode.png)

No menu suspenso mostra cinco opções de logon único para seu aplicativo:

* Logon único do Azure AD desabilitado
* Logon baseado em senha
* Logon vinculado
* Autenticação Integrada do Windows
* Logon baseado em cabeçalho

## <a name="azure-ad-single-sign-on-disabled"></a>Logon único do Azure AD desabilitado

Se você não quiser usar a integração do Azure Active Directory para logon único para o seu aplicativo, escolha **Logon único do Azure AD desabilitado**. Com essa opção selecionada, os usuários podem ter de se autenticar duas vezes. Primeiro, eles se autenticam para o Azure Active Directory e, então, entram no aplicativo em si. 

Essa opção é uma boa opção se seu aplicativo local não exige que os usuários se autentiquem, mas você deseja adicionar o Azure Active Directory como camada de segurança de acesso remoto. 

## <a name="password-based-sign-on"></a>Logon baseado em senha

Se você quiser usar o Azure Active Directory como cofre de senhas para seus aplicativos locais, escolha **Logon baseado em senha**. Essa opção é uma boa escolha se o aplicativo autentica com uma combinação de nome de usuário e senha em vez de tokens de acesso ou cabeçalhos. Com logon baseado em senha, os usuários precisam entrar no aplicativo ao acessarem pela primeira vez. Depois disso, o Azure Active Directory fornece o nome de usuário e a senha em nome do usuário. 

Para obter informações sobre a configuração de logon baseado em senha, consulte [Compartimentação de senhas para logon único com Proxy de Aplicativo](application-proxy-sso-azure-portal.md).

## <a name="linked-sign-on"></a>Logon vinculado

Se você já tiver uma solução de logon único configurada para as identidades locais, escolha **Logon vinculado**. Essa opção permite que o Azure Active Directory aproveite as soluções de SSO existentes, mas também oferece aos usuários acesso remoto ao aplicativo. 

Para obter informações sobre logon vinculado (antigamente conhecido como logon único existente), confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows

Se seus aplicativos locais usam autenticação integrada do Windows (IWA) ou se você quiser usar delegação restrita de Kerberos (KCD) para logon único, escolha **Autenticação integrada do Windows**. Com essa opção, os usuários só precisam se autenticar no Azure Active Directory e, em seguida, o conector de Proxy de Aplicativo representa o usuário para obter um token Kerberos e entrar no aplicativo. 

Para obter informações sobre como configurar a autenticação integrada do Windows, consulte [Delegação restrita de Kerberos para logon único com o Proxy de Aplicativo](active-directory-application-proxy-sso-using-kcd.md).

## <a name="header-based-sign-on"></a>Logon baseado em cabeçalho 

Se seus aplicativos usam cabeçalhos para autenticação, escolha **logon baseado em cabeçalho**. Com essa opção, os usuários só precisam se autenticar no Azure Active Directory. Parceiros da Microsoft com o serviço de autenticação de terceiros chamado PingAccess, que converte o token de acesso do Azure Active Directory em formato de cabeçalho para o aplicativo. 

Para obter informações sobre como configurar a autenticação baseada em cabeçalho, consulte [ Autenticação baseada em cabeçalho para logon único com o Proxy de Aplicativo](application-proxy-ping-access.md).

## <a name="next-steps"></a>Próximas etapas

- [Compartimentação de senhas para logon único com o Proxy de Aplicativo](application-proxy-sso-azure-portal.md)
- [Delegação restrita de Kerberos para logon único com o Proxy de Aplicativo](active-directory-application-proxy-sso-using-kcd.md)
- [Autenticação baseada em cabeçalho para logon único com o Proxy de Aplicativo](application-proxy-ping-access.md) 
