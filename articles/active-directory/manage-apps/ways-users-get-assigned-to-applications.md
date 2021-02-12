---
title: Entenda como os usuários são atribuídos a aplicativos no Azure Active Directory
description: Entenda como os usuários são atribuídos a um aplicativo que está usando Azure Active Directory para gerenciamento de identidade.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 161df0446c9478ca0f2b135c1e426f3786b164fc
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257433"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Entenda como os usuários são atribuídos a aplicativos no Azure Active Directory
Este artigo o ajudará a compreender como usuários são atribuídos a um aplicativo em seu locatário.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Como os usuários são atribuídos a um aplicativo no Azure AD?
Para acessar um aplicativo, o usuário precisa primeiro ser atribuído a ele de alguma forma. A atribuição pode ser feita por um administrador, um representante de negócios ou, às vezes, o próprio usuário. Abaixo, descrevemos as formas como os usuários podem ser atribuídos a aplicativos:

*  Um administrador [atribui um usuário](./assign-user-or-group-access-portal.md) diretamente ao aplicativo
*  Um administrador [atribui um grupo](./assign-user-or-group-access-portal.md) do qual o usuário é membro ao aplicativo, incluindo:
    * Um grupo que foi sincronizado do local
    * Um grupo de segurança estático criado na nuvem
    * Um [grupo de segurança dinâmico](../enterprise-users/groups-dynamic-membership.md) criado na nuvem
    * Um grupo de Microsoft 365 criado na nuvem
    * O grupo [Todos os Usuários](../fundamentals/active-directory-groups-create-azure-portal.md)
*  Um administrador habilita o [acesso de aplicativo de autoatendimento](./manage-self-service-access.md) para permitir que um usuário adicione um aplicativo usando [meus aplicativos](../user-help/my-apps-portal-end-user-access.md) **Adicionar** recurso de aplicativo **sem aprovação de negócios**
*  Um administrador habilita o [acesso de aplicativo de autoatendimento](./manage-self-service-access.md) para permitir que um usuário adicione um aplicativo usando [meus aplicativos](../user-help/my-apps-portal-end-user-access.md) **Adicionar** recurso de aplicativo, mas somente **com aprovação anterior de um conjunto selecionado de aprovadores de negócios**
*  Um administrador habilita o [Gerenciamento de Grupo de Autoatendimento](../enterprise-users/groups-self-service-management.md) para permitir que um usuário ingresse em um grupo a que um aplicativo está atribuído **sem aprovação de negócios**
*  Um administrador habilita o [Gerenciamento de Grupo de Autoatendimento](../enterprise-users/groups-self-service-management.md) para permitir que um usuário ingresse em um grupo a que um aplicativo está atribuído, mas apenas **com aprovação anterior de um conjunto selecionado de aprovadores de negócios**
*  Um administrador atribui uma licença a um usuário diretamente para um aplicativo de primeira parte, como [Microsoft 365](https://products.office.com/)
*  Um administrador atribui uma licença a um grupo do qual o usuário é membro para um aplicativo de primeira parte, como [Microsoft 365](https://products.office.com/)
*  Um [administrador dá consentimento para um aplicativo](../develop/howto-convert-app-to-be-multi-tenant.md) ser usado por todos os usuários e, em seguida, um usuário entra no aplicativo
* O próprio usuário [dá consentimento para um aplicativo](../develop/howto-convert-app-to-be-multi-tenant.md) entrando no aplicativo

## <a name="next-steps"></a>Próximas etapas
* [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
* [O que é gerenciamento de aplicativos?](what-is-application-management.md)
* [O que é logon único?](what-is-single-sign-on.md)