---
title: "Acesso condicional para usuários de colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "A colaboração B2B do Azure Active Directory dá suporte à autenticação multifator (MF) para acesso seletivo aos seus aplicativos corporativos"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 09/11/2017
ms.author: sasubram
ms.openlocfilehash: d3917754069ad961ffd9233d852aab6fa172eaef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Acesso condicional para usuários de colaboração B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Autenticação multifator para usuários B2B
Com a colaboração B2B do Azure AD, as organizações podem aplicar políticas de MFA (autenticação multifator) para usuários B2B. Essas políticas podem ser impostas no nível do locatário, aplicativo ou usuário individual, da mesma maneira que podem ser habilitadas para membros e funcionários em tempo integral da organização. Politicas de MFA são impostas na organização do recurso.

Exemplo:
1. O Administrador ou operador de informações na Empresa A convida o usuário da Empresa B para um aplicativo *Foo* na empresa A.
2. O aplicativo *Foo* na empresa A está configurado para exigir MFA no acesso.
3. Quando o usuário da empresa B tenta acessar o aplicativo *Foo* no locatário da empresa A, recebe uma solicitação para concluir um desafio de MFA.
4. O usuário pode configurar sua MFA com a empresa A, e escolher a opção de MFA.
5. Esse cenário funciona para qualquer identidade (Azure AD ou MSA, por exemplo, se os usuários na Empresa B autenticarem usando a ID social)
6. A empresa A deve ter licenças suficientes do Azure AD Premium que oferecem suporte a MFA. O usuário da empresa B consumirá essa licença da empresa A.

O locatário que convida sempre é responsável pela MFA dos usuários da organização parceira, mesmo se a organização parceira tiver recursos de MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Definindo a MFA para usuários de colaboração B2B
Para descobrir como é fácil configurar a MFA para usuários de colaboração B2B, confira o vídeo a seguir:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Experiência de MFA de usuários B2B para resgate de oferta
Confira a animação abaixo para ver a experiência de resgate:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Redefinição de MFA para usuários de colaboração B2B
Atualmente, o administrador pode exigir que os usuários de colaboração B2B façam uma verificação novamente usando somente os seguintes cmdlets do PowerShell:

1. Conecte-se ao AD do Azure

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Obter todos os usuários com métodos de verificação

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Veja um exemplo:

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Redefina o método MFA para um usuário específico a fim de exigir que o usuário de colaboração B2B defina métodos de prova novamente. Exemplo:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Por que realizamos MFA na locação de recursos?

Na versão atual, a MFA está sempre na locação de recursos, por motivos de previsibilidade. Por exemplo, digamos que um usuário da Contoso (Sally) é convidado para a Fabrikam e a Fabrikam habilitou MFA para usuários B2B.

Se a Contoso tiver a política de MFA habilitada para App1, mas não para App2, se olharmos para a declaração de MFA da Contoso no token, veremos o seguinte problema:

* Dia 1: um usuário tem MFA na Contoso e está acessando o App1, então nenhuma solicitação de MFA adicional é mostrada na Fabrikam.

* Dia 2: o usuário acessou o App 2 na Contoso, então agora ao acessar a Fabrikam, ele deve se registrar na MFA.

Esse processo pode ser confuso e pode levar a causar preenchimentos incompletos na entrada.

Além disso, mesmo se a Contoso tiver o recurso MFA, nem sempre aconteceria da Fabrikam confiar na política do MFA.

Finalmente, a MFA de locatários de recursos também funciona para IDs sociais e MSAs e para organizações parceiras que não tenham a MFA configurada.

Portanto, a recomendação para MFA para usuários B2B é sempre exigir MFA no locatário que convida. Esse requisito poderia conduzir a uma MFA dupla em alguns casos, mas sempre que acessa o locatário que convida, a experiência dos usuários finais será previsível: Sally deve se registrar na MFA com o locatário que convida.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Acesso condicional com base em risco, no local e no dispositivo para usuários B2B

Quando a Contoso habilita políticas de acesso condicional com base em dispositivo para seus dados corporativos, o acesso é impedido em dispositivos não gerenciados pela Contoso e incompatíveis com as políticas de dispositivo da Contoso.

Se o dispositivo do usuário B2B não for gerenciado pela Contoso, o acesso de usuários B2B das organizações parceiras será bloqueado em quaisquer contextos em que essas políticas são impostas. Entretanto, a Contoso pode criar listas de exclusão contendo usuários parceiros específicos para excluí-los da política de acesso condicional com base em dispositivo.

#### <a name="location-based-conditional-access-for-b2b"></a>Acesso condicional com base em local para B2B

Políticas de acesso condicional com base em local podem ser impostas para usuários B2B se a organização que convida for capaz de criar um intervalo de endereços IP confiável que define suas organizações parceiras.

#### <a name="risk-based-conditional-access-for-b2b"></a>Acesso condicional com base em risco para B2B

Atualmente, as políticas de entrada baseadas em risco não podem ser aplicadas a usuários B2B, pois a avaliação de risco é realizada na organização inicial do usuário B2B.

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalização da colaboração B2B do Azure Active Directory](active-directory-b2b-api.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos para gerenciamento de aplicativos no Azure Active Directory](active-directory-apps-index.md)
