---
title: Acesso condicional para usuários de colaboração B2B-Azure Active Directory | Microsoft Docs
description: A colaboração B2B do Azure Active Directory dá suporte à autenticação multifator (MF) para acesso seletivo aos seus aplicativos corporativos
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a54e5006a268347148945fbe7fc5f18cfa41036
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357125"
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

1. Conectar ao Azure AD

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

* Dia 1: Um usuário tem o MFA na Contoso e está acessando o App1; portanto, nenhum prompt adicional do MFA é mostrado na Fabrikam.

* Dia 2: O usuário acessou o Aplicativo 2 na Contoso; portanto, agora ao acessar a Fabrikam, ele precisa se registrar no MFA nele.

Esse processo pode ser confuso e pode levar a causar preenchimentos incompletos na entrada.

Além disso, mesmo se a Contoso tiver o recurso MFA, nem sempre aconteceria da Fabrikam confiar na política do MFA.

Finalmente, a MFA de locatários de recursos também funciona para IDs sociais e MSAs e para organizações parceiras que não tenham a MFA configurada.

Portanto, a recomendação para MFA para usuários B2B é sempre exigir MFA no locatário que convida. Esse requisito pode levar a um MFA duplo em alguns casos, mas sempre que se acessa o locatário de convite a experiência dos usuários finais é previsível: Sara precisará se registrar no MFA com o locatário de convite.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Acesso condicional baseado no dispositivo, baseado no local e em risco para usuários B2B

Quando a contoso habilita políticas de acesso condicional com base no dispositivo para seus dados corporativos, o acesso é impedido de dispositivos que não são gerenciados pela contoso e não são compatíveis com as políticas de dispositivo da contoso.

Se o dispositivo do usuário B2B não for gerenciado pela Contoso, o acesso de usuários B2B das organizações parceiras será bloqueado em quaisquer contextos em que essas políticas são impostas. No entanto, a Contoso pode criar listas de exclusão que contenham usuários de parceiros específicos para excluí-las da política de acesso condicional com base no dispositivo.

#### <a name="mobile-application-management-policies-for-b2b"></a>Políticas de gerenciamento de aplicativo móvel para B2B

As políticas de proteção de aplicativo de acesso condicional não podem ser aplicadas a usuários B2B porque a organização que está convidando não tem visibilidade da organização inicial do usuário B2B.

#### <a name="location-based-conditional-access-for-b2b"></a>Acesso condicional baseado em local para B2B

As políticas de acesso condicional com base no local podem ser impostas para usuários B2B se a organização que convida for capaz de criar um intervalo de endereços IP confiável que define suas organizações parceiras.

#### <a name="risk-based-conditional-access-for-b2b"></a>Acesso condicional com base em risco para B2B

Atualmente, as políticas de entrada baseadas em risco não podem ser aplicadas a usuários B2B, pois a avaliação de risco é realizada na organização inicial do usuário B2B.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

* [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
* [Licenciamento da colaboração B2B do Azure AD](licensing-guidance.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](faq.md)
