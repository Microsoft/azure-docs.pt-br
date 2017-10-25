---
title: "Integrar o logon único do Azure Active Directory com aplicativos SaaS | Microsoft Docs"
description: "Habilitar o gerenciamento de acesso centralizado da autenticação de logon único e do provisionamento de usuário dos aplicativos SaaS no Active Directory do Azure. Uma visão geral de como integrar o Active Directory do Azure a aplicativos SaaS."
services: active-directory
keywords: integrar o AD do Azure a aplicativos SaaS
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 53b9d341-d1fc-4bbb-ac7c-3f4c68fcf00a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: aaronsm
ms.openlocfilehash: fc0d297598c334ca8f6f8a2bd3ae948c87956342
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Integrar o logon único do Azure AD com aplicativos de SaaS
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-enterprise-apps-manage-sso.md)
> * [Portal clássico do Azure](active-directory-sso-integrate-saas-apps.md)
>
>

[!INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Para começar a configurar o logon único para um aplicativo que está trazendo para sua organização, você usará um diretório existente no Active Directory do Azure (Azure AD). Você pode usar um diretório do Azure AD que você obtém por meio do Microsoft Azure, Office 365 ou Windows Intune. Se você tiver dois ou mais deles, consulte [Administrar seu diretório do AD do Azure](active-directory-administer.md) para determinar qual deles usar.

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo. Para saber com atribuir funções de administrador no centro de administração do Azure AD, consulte [Atribuindo funções de administrador no Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).

## <a name="authentication"></a>Autenticação
Para aplicativos que dão suporte aos protocolos SAML 2.0, WS-Federation ou OpenID Connect, o Active Directory do Azure usa certificados de assinatura para estabelecer relações de confiança. Para obter mais informações sobre isso, consulte [Gerenciando certificados para logon único federado](active-directory-sso-certs.md).

Para aplicativos que dão suporte apenas ao logon baseado em formulários HTML, o Active Directory do Azure usa a “compartimentação de senha” para estabelecer relações de confiança. Isso permite que os usuários em sua organização entrem automaticamente em um aplicativo de SaaS pelo Azure AD usando as informações de conta do aplicativo de SaaS. O Azure AD coleta e armazena com segurança as informações de conta de usuário e a senha relacionada. Para obter mais informações, consulte [Logon único baseado em senha](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## <a name="authorization"></a>Autorização
Uma conta provisionada permite que um usuário autorizado use um aplicativo após ser autenticado por meio de logon único. O provisionamento do usuário pode ser feito manualmente ou, em alguns casos, você pode adicionar e remover informações do usuário do aplicativo de SaaS com base nas alterações feitas no Active Directory do Azure. Para saber mais sobre o uso dos conectores existentes do Azure AD para o provisionamento automatizado, consulte [Provisionamento e desprovisionamento do usuário automatizados para os aplicativos de SaaS](active-directory-saas-app-provisioning.md).

Caso contrário, você pode adicionar manualmente informações do usuário a um aplicativo ou usar outras soluções de provisionamento que estão disponíveis no marketplace.

## <a name="access"></a>Access
O Azure AD fornece várias maneiras personalizáveis para implantar aplicativos para os usuários finais em sua organização. Você não está limitado a nenhuma implantação ou solução de acesso específica. Você pode usar [a solução que melhor atender às suas necessidades](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Considerações adicionais sobre os aplicativos já em uso
Configurar o logon único para um aplicativo já utilizado pela sua organização é um processo diferente de criar novas contas para um novo aplicativo. Há algumas etapas preliminares incluindo: mapeamento das identidades do usuário no aplicativo para as identidades do Azure AD e compreender como os usuários farão logon em um aplicativo depois dele ser integrado.

> [!NOTE]
> Para configurar o SSO para um aplicativo existente, você precisa ter direitos de administrador globais no AD do Azure e no aplicativo de SaaS.
>
>

### <a name="mapping-user-accounts"></a>Mapeamento das contas de usuário
A identidade de um usuário geralmente tem um identificador exclusivo que pode ser um endereço de email ou um nome UPN. Você precisará vincular (mapear) a identidade do aplicativo de cada usuário com sua respectiva identidade do AD do Azure. Existem algumas maneiras de fazer isso, dependendo do requisito de autenticação de seu aplicativo.

Para obter mais informações sobre o mapeamento das identidades do aplicativo com as identidades do Azure AD, consulte [Personalizando as declarações emitidas no token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) e [Personalizando os mapeamentos de atributo para o provisionamento](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Compreensão da experiência de logon do usuário
Ao integrar o SSO para um aplicativo que já está em uso, é importante perceber que a experiência do usuário será afetada. Para todos os aplicativos, os usuários começarão usando suas credenciais do AD do Azure para entrar. Eles podem precisar usar um portal diferente para acessar os aplicativos.

O SSO para alguns aplicativos pode ser feito na interface de entrada do aplicativo, mas para outros, o usuário terá que passar por um portal central, como ([Meus Aplicativos](http://myapps.microsoft.com) ou [Office365](http://portal.office.com/myapps)) para entrar. Saiba mais sobre os diferentes tipos de SSO e suas experiências de usuário em [O que é o acesso a aplicativos e o logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

Outro recurso valioso é *Suprimir o consentimento do usuário* no artigo [Orientando desenvolvedores](active-directory-applications-guiding-developers-for-lob-applications.md) .

## <a name="next-steps"></a>Próximas etapas
Para aplicativos SaaS que você encontrar na Galeria de Aplicativos, o Azure AD fornece diversos [tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md).

Se o aplicativo não estiver na Galeria de Aplicativos, você poderá [adicioná-lo à Galeria de Aplicativos do Azure AD como um aplicativo personalizado](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Há muito mais detalhes sobre todos esses temas na biblioteca do Azure.com, começando com [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

Além disso, não perca o [Índice de artigos para Gerenciamento de Aplicativos no Azure Active Directory](active-directory-apps-index.md).
