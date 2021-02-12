---
title: Autenticação baseada em senha com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de autenticação baseada em senha com o Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd6a5c8af117bf6cb39969a5f1b1f17ff08681c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172815"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Autenticação baseada em senha com Azure Active Directory

O SSO (Sign-On único) baseado em senha usa o processo de autenticação existente para o aplicativo. Quando você habilita o SSO baseado em senha, o Azure Active Directory (Azure AD) coleta, criptografa e armazena com segurança as credenciais do usuário no diretório. O Azure AD fornece o nome de usuário e a senha para o aplicativo quando o usuário tenta entrar.

Escolha SSO baseado em senha quando um aplicativo for autenticado com um nome de usuário e senha em vez de tokens de acesso e cabeçalhos. O SSO baseado em senha dá suporte a qualquer aplicativo baseado em nuvem que tenha uma página de entrada baseada em HTML. 

## <a name="use-when"></a>Usar quando

Você precisa proteger com pré-autenticação e fornecer SSO por meio de compartimentação de senha para aplicativos Web.

![diagrama arquitetônico](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>Componentes do sistema

* **Usuário**: acessa o aplicativo com base em forma de meus aplicativos ou visitando diretamente o site. 

* **Navegador da Web**: o componente com o qual o usuário interage para acessar a URL externa do aplicativo. O usuário acessa o aplicativo baseado em formulário por meio da extensão myapps. 

* **Extensão myapps**: identifica o aplicativo SSO baseado em senha configurado e fornece as credenciais para o formulário de entrada. A extensão myapps é instalada no navegador da Web. 

* **AD do Azure**: autentica o usuário.

## <a name="implement-password-based-sso-with-azure-ad"></a>Implementar SSO baseado em senha com o Azure AD

* [O que é SSO baseado em senha](../manage-apps/what-is-single-sign-on.md) 

* [Configurar SSO baseado em senha para aplicativos de nuvem ](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [Configurar o SSO baseado em senha para aplicativos locais com o proxy de aplicativo](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)

