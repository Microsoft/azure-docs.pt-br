---
title: O que é o gerenciamento de aplicativos no Azure Active Directory
description: Uma visão geral do uso do Azure AD (Active Directory) como um sistema de IAM (Gerenciamento de Identidades e Acesso) para seus aplicativos locais e de nuvem.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: iangithinji
ms.reviewer: ''
ms.openlocfilehash: 8bdb8eb9cf176f8e190769e38c81d02ad2985196
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376385"
---
# <a name="what-is-application-management"></a>O que é gerenciamento de aplicativos?

O Azure AD é um sistema de IAM (Gerenciamento de Identidades e Acesso). Ele fornece um só local para armazenar informações sobre identidades digitais. Você pode configurar seus aplicativos de software para usar o Azure AD como o local em que as informações do usuário são armazenadas. 

O Azure AD precisa ser configurado para se integrar a um aplicativo. Em outras palavras, ele precisa saber quais aplicativos o estão usando para identidades. Deixar o Azure AD ciente desses aplicativos e informar como ele deve tratá-los é conhecido como gerenciamento de aplicativos.

Gerencie os aplicativos na página **Aplicativos empresariais** localizada na seção Gerenciar do portal do Azure Active Directory.

![A opção Aplicativos empresariais na seção Gerenciar do portal do Azure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>O que é um sistema de IAM (Gerenciamento de Identidades e Acesso)?
Um aplicativo é um software usado para alguma finalidade. A maioria dos aplicativos exige que os usuários se conectem.

Um sistema de identidade centralizado fornece um só local para armazenar informações do usuário que possam ser usadas posteriormente por todos os aplicativos. Esses sistemas ficaram conhecidos como sistemas de IAM (Gerenciamento de Identidades e Acesso). O Azure Active Directory é o sistema de IAM para a nuvem da Microsoft.

>[!TIP]
>Um sistema de IAM fornece um só local para controlar as identidades dos usuários. O Azure AD é o sistema de IAM para a nuvem da Microsoft.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Por que gerenciar aplicativos com uma solução de nuvem?

Geralmente, as organizações têm centenas de aplicativos que os usuários precisam para trabalhar. Os usuários acessam esses aplicativos em vários dispositivos e locais. Novos aplicativos são adicionados, desenvolvidos e publicados regularmente. Com muitos aplicativos e pontos de acesso, é importante usar uma solução de identidade que funcione com todos eles.

>[!TIP]
>A galeria de aplicativos do Azure AD contém muitos aplicativos populares que já estão pré-configurados para funcionar com o Azure AD como um provedor de identidade.

## <a name="how-does-azure-ad-work-with-apps"></a>Como o Azure AD funciona com aplicativos?

O Azure AD reside no meio e fornece gerenciamento de identidades para aplicativos de nuvem e locais. 

![Diagrama que mostra aplicativos federados por meio do Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Reduza os custos administrativos [automatizando o provisionamento de usuário](../app-provisioning/user-provisioning.md) para que os usuários sejam adicionados automaticamente ao Azure AD quando você os adicionar ao sistema de RH da sua empresa. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Quais tipos de aplicativos posso integrar ao Azure AD?

Você pode usar o Azure AD como seu sistema de identidade para praticamente qualquer aplicativo. Muitos aplicativos já estão pré-configurados e podem ser configurados com esforço mínimo. Esses aplicativos pré-configurados são publicados na [Galeria de Aplicativos do Azure AD](/azure/active-directory/saas-apps/). 

Você poderá configurar manualmente a maioria dos aplicativos para logon único se eles ainda não estiverem na galeria. O Azure AD fornece várias opções de SSO. Algumas das mais populares são o SSO baseado em SAML e o SSO baseado em OIDC. Para saber mais sobre como integrar aplicativos para habilitar o SSO, confira [opções de logon único](sso-options.md). 

Sua organização usa aplicativos locais? Você pode integrá-los usando o Proxy de Aplicativo. Para saber mais, confira [Fornecer acesso remoto a aplicativos locais por meio do Proxy de Aplicativo do Azure AD](application-proxy.md).

>[!TIP]
>Ao criar os próprios aplicativos de linha de negócios, você pode integrá-los ao Azure AD para dar suporte ao logon único. Para saber mais sobre como desenvolver aplicativos para o Azure AD, confira [Plataforma de identidade da Microsoft](..//develop/v2-overview.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Gerenciar os riscos com as políticas de Acesso Condicional

Combinar o SSO (logon único) do Azure AD com [Acesso Condicional](../conditional-access/concept-conditional-access-cloud-apps.md) aumenta a segurança no acesso aos aplicativos. As políticas de Acesso Condicional fornecem controle granular a aplicativos com base nas condições definidas. 

## <a name="improve-productivity-with-single-sign-on"></a>Aumentar a produtividade com o logon único

O SSO (logon único) fornece uma experiência de usuário unificada entre o Microsoft 365 e todos os outros aplicativos que você usa. Você não vai mais precisar inserir constantemente seu nome de usuário e sua senha!

Para saber mais sobre o logon único, confira [o que é logon único](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Abordar a governança e a conformidade

Monitore aplicativos por meio de relatórios que usam as ferramentas de SIEM (Gerenciamento de Eventos e Informações de Segurança). É possível acessar os relatórios no portal ou nas APIs. Programaticamente, é possível auditar quem tem acesso aos aplicativos e remover o acesso de usuários inativos nas revisões de acesso.

## <a name="manage-costs"></a>Gerenciar os custos

Ao migrar para o Azure AD, você economiza custos e elimina a dificuldade de gerenciar uma infraestrutura local. O Azure AD também oferece acesso de autoatendimento para aplicativos, o que economiza tempo para administradores e usuários. O logon único elimina as senhas específicas de aplicativo. Essa capacidade de iniciar sessão uma vez economiza custos relacionados com a redefinição de senha para aplicativos e acaba com a perda de produtividade ao recuperar senhas.

Para aplicativos focados em Recursos Humanos ou outros aplicativos com um grande conjunto de usuários, você pode usar o provisionamento de aplicativos para facilitar sua vida. O provisionamento de aplicativos automatiza o processo de adição e remoção de usuários. Para saber mais, confira [O que é o provisionamento de aplicativos?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Próximas etapas

- [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
- [Introdução à integração de aplicativos](plan-an-application-integration.md)
- [Saiba como automatizar o provisionamento](../app-provisioning/user-provisioning.md)