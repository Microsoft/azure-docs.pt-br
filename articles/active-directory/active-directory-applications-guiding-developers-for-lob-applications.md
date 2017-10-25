---
title: Desenvolver aplicativos para o Azure AD| Microsoft Docs
description: "Escrito para profissionais de TI, este artigo fornece diretrizes para a integração de aplicativos do Azure com o Active Directory."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: dd69f2bc-37c5-457c-857d-27acb84267fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b119be9c06d8c1ccc8e747168429e6c2d2e7a8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Desenvolver aplicativos de linha de negócios para o Azure Active Directory
Este guia fornece uma visão geral do desenvolvimento de aplicativos LoB (de linha de negócios) do Azure AD (Active Directory). O público-alvo são os administradores globais do Active Directory/Office 365.

## <a name="overview"></a>Visão geral
A criação de aplicativos integrados ao AD do Azure oferece aos usuários em sua organização logon único com o Office 365. Ter o aplicativo no Azure AD dá controle sobre a política de autenticação definida para o aplicativo. Para saber mais sobre acesso condicional e como proteger aplicativos com MFA (Multi-Factor Authentication), consulte [Configurando regras de acesso](active-directory-conditional-access-azuread-connected-apps.md).

Registre seu aplicativo com o Azure Active Directory. Registrar o aplicativo significa que seus desenvolvedores podem usar o Azure AD para autenticar usuários e solicitar acesso a recursos de usuário como email, calendário e documentos.

Qualquer membro do diretório (não convidados) pode registrar um aplicativo, também conhecido como *criação de um objeto de aplicativo*.

Registrar um aplicativo permite que qualquer usuário faça o seguinte:

* Obter uma identidade para o aplicativo que reconhece o AD do Azure
* Obter um ou mais segredos/chaves que o aplicativo pode usar para se autenticar no AD
* Marcar o aplicativo no portal do Azure com um nome, logotipo personalizado etc.
* Aproveitar os recursos de autorização do Azure AD para seu aplicativo, incluindo:

  * RBAC (Controle de Acesso Baseado em Função)
  * Active Directory do Azure como servidor de autorização oAuth (proteger uma API exposta pelo aplicativo)
* Declarar as permissões exigidas necessárias para que o aplicativo funcione conforme o esperado, incluindo:

      - Permissões de aplicativo (somente administradores globais). Por exemplo: associação de função em outro aplicativo do Azure AD ou associação de grupo relativa a um recurso, grupo de recursos ou assinatura do Azure
      - Permissões (qualquer usuário). Por exemplo: Azure AD, Entrar e Ler o Perfil

> [!NOTE]
> Por padrão, qualquer membro pode registrar um aplicativo. Para saber como restringir as permissões para o registro de aplicativos para membros específicos, consulte [Como os aplicativos são adicionados ao Azure AD](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Aqui está o que você, o administrador global, precisa fazer para ajudar os desenvolvedores a preparar o aplicativo para produção:

* Configurar regras de acesso (política de acesso/MFA)
* Configurar o aplicativo para exigir a atribuição de usuário e atribuir usuários
* Suprimir a experiência de consentimento do usuário padrão

## <a name="configure-access-rules"></a>Configurar regras de acesso
Configurar regras de acesso por aplicativo para seus aplicativos de SaaS. Por exemplo você pode exigir MFA ou somente permitir o acesso a usuários em redes confiáveis. Os detalhes para isso estão disponíveis no documento [Configurando regras de acesso](active-directory-conditional-access-azuread-connected-apps.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurar o aplicativo para exigir a atribuição de usuário e atribuir usuários
Por padrão, os usuários podem acessar aplicativos sem ser atribuídos. No entanto, se o aplicativo expõe funções ou se você deseja que o aplicativo seja exibido no painel de acesso do usuário, você deve exigir a atribuição de usuário.

[Exigindo atribuição do usuário](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Caso você seja um assinante do Azure AD Premium ou EMS (Enterprise Mobility Suite), é altamente recomendável usar grupos. A atribuição de grupos ao aplicativo permite delegar o gerenciamento de acesso contínuo ao proprietário do grupo. Você pode criar o grupo ou, se preferir, pedir à parte responsável da sua organização para criar o grupo usando o recurso de gerenciamento de grupos.

[Atribuindo usuários a um aplicativo](active-directory-applications-guiding-developers-assigning-users.md)  
[Atribuindo grupos a um aplicativo](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Suprimir o consentimento do usuário
Por padrão, cada usuário passa por uma experiência de consentimento para entrar. A experiência de consentimento, de ser solicitado a conceder permissões para um aplicativo, pode ser desconcertante para usuários que não estão familiarizados com essa decisão.

Para aplicativos em que você confia, você pode simplificar a experiência do usuário consentindo aplicativo em nome de sua organização.

Para saber mais sobre o consentimento do usuário e sobre a experiência de consentimento no Azure, confira [Integrando aplicativos com o Azure Active Directory](active-directory-integrating-applications.md).

## <a name="related-articles"></a>Artigos relacionados
* [Habilitar acesso remoto seguro a aplicativos locais com o Proxy de Aplicativo do Azure AD](active-directory-application-proxy-get-started.md)
* [Visualização de acesso condicional do Azure para aplicativos SaaS](active-directory-conditional-access-azuread-connected-apps.md)
* [Gerenciando o acesso a aplicativos usando o Azure AD](active-directory-managing-access-to-apps.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
