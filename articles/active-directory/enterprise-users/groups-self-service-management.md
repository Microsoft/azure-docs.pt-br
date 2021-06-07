---
title: Configurar o gerenciamento de grupos de autoatendimento – Azure Active Directory | Microsoft Docs
description: Criar e gerenciar grupos de segurança ou grupos de Microsoft 365 no Azure Active Directory e solicitar associações de grupo de segurança ou de grupo de Microsoft 365
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6b2b8e3374c362f937aa5cfe106e8da9f9aa39f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547994"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Configurar o gerenciamento de grupo de autoatendimento no Azure Active Directory 

Você pode permitir que os usuários criem e gerenciem seus próprios grupos de segurança ou grupos de Microsoft 365 no Azure Active Directory (AD do Azure). O proprietário do grupo pode aprovar ou negar solicitações de associação e pode delegar o controle de associação de grupo. Os recursos de gerenciamento de grupo de autoatendimento não estão disponíveis para grupos de segurança ou listas de distribuição habilitadas para email.

## <a name="self-service-group-membership-defaults"></a>Padrões de associação de grupo de autoatendimento

Quando grupos de segurança são criados no portal do Azure ou usando o PowerShell do Azure AD, somente os proprietários do grupo podem atualizar a associação. Grupos de segurança criados pelo autoatendimento no [painel de acesso](https://account.activedirectory.windowsazure.com/r#/joinGroups) e todos os grupos de Microsoft 365 estão disponíveis para ingressar para todos os usuários, seja aprovado pelo proprietário ou aprovado automaticamente. No painel de acesso, você pode alterar as opções de associação ao criar o grupo.

Grupos criados em | Comportamento padrão do grupo de segurança | Comportamento padrão do grupo de Microsoft 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | Somente proprietários podem adicionar membros<br>Visível, mas não disponível para junção no painel de acesso | Abrir para ingressar para todos os usuários
[Azure portal](https://portal.azure.com) | Somente proprietários podem adicionar membros<br>Visível, mas não disponível para junção no painel de acesso<br>O proprietário não é atribuído automaticamente na criação do grupo | Abrir para ingressar para todos os usuários
[Painel de acesso](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Abrir para ingressar para todos os usuários<br>As opções de associação podem ser alteradas quando o grupo é criado | Abrir para ingressar para todos os usuários<br>As opções de associação podem ser alteradas quando o grupo é criado

## <a name="self-service-group-management-scenarios"></a>Cenários de gerenciamento de grupo de autoatendimento

* **Gerenciamento de grupo delegado** Um exemplo é um administrador que está gerenciando o acesso a um aplicativo SaaS que a empresa está usando. O gerenciamento desses direitos de acesso está se tornando inconveniente, portanto esse administrador solicita ao proprietário de negócios para criar um novo grupo. O administrador atribui o acesso para o aplicativo ao novo grupo e adiciona ao grupo todas as pessoas que já acessam o aplicativo. O proprietário da empresa pode então adicionar mais usuários, e os usuários são automaticamente provisionados para o aplicativo. O proprietário da empresa não precisa esperar que o administrador gerencie o acesso para usuários. Se o administrador conceder a mesma permissão a um gerente em um grupo de negócios diferente, essa pessoa também poderá gerenciar o acesso para seus próprios membros do grupo. Nem o proprietário da empresa nem o gerente podem exibir ou gerenciar as associações de grupo uns dos outros. O administrador ainda pode ver todos os usuários que têm acesso ao aplicativo e direitos de acesso de bloco, se necessário.
* **Gerenciamento de grupo de autoatendimento** Um exemplo desse cenário são dois usuários que têm sites do SharePoint Online que eles configuram de forma independente. Eles desejam dar acesso às equipes uns aos seus sites. Para isso, eles podem criar um grupo no Azure AD e, no SharePoint Online, cada um seleciona esse grupo para fornecer acesso a seus sites. Quando alguém deseja acesso, solicita do painel de acesso, e depois da aprovação obtêm acesso a ambos os sites do SharePoint Online automaticamente. Posteriormente, um deles decide que todas as pessoas que acessam o site também devem obter acesso a determinado aplicativo SaaS. O administrador do aplicativo SaaS pode adicionar direitos de acesso ao aplicativo para o site do SharePoint Online. Depois, as solicitações que forem aprovadas darão acesso aos dois sites do SharePoint Online e também ao aplicativo SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Disponibilização de um grupo para autoatendimento do usuário

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **grupos** e, em seguida, selecione configurações **gerais** .
1. Definir **proprietários pode gerenciar solicitações de associação de grupo no painel de acesso** como **Sim**.
1. Defina **acesso restrito a grupos no painel de acesso** como **não**.
1. Se você definir **os usuários podem criar grupos de segurança em portais do Azure** ou **os usuários podem criar grupos de Microsoft 365 nos portais do Azure** para

    - **Sim**: todos os usuários em sua organização do Azure ad têm permissão para criar novos grupos de segurança e adicionar membros a esses grupos. Esses novos grupos também aparecem no Painel de Acesso para todos os outros usuários. Se a configuração de política no grupo permitir, outros usuários poderão criar solicitações para unir esses grupos
    - **Não**: os usuários não podem criar grupos e não podem alterar grupos existentes para os quais eles são proprietários. No entanto, ainda podem gerenciar as associações desses grupos e aprovar solicitações de outros usuários para ingressar em seus grupos.

Você também pode usar **proprietários que podem atribuir Membros como proprietários de grupo no portal do Azure** para obter controle de acesso mais granular sobre o gerenciamento de grupo de autoatendimento para seus usuários.

Quando os usuários podem criar grupos, todos os usuários em sua organização têm permissão para criar novos grupos e, em seguida, podem, como o proprietário padrão, adicionar membros a esses grupos. Você não pode especificar indivíduos que podem criar seus próprios grupos. Você pode especificar indivíduos somente para tornar outro membro de grupo um proprietário de grupo.

> [!NOTE]
> Uma licença Azure Active Directory Premium (P1 ou P2) é necessária para que os usuários solicitem um grupo de segurança ou grupo de Microsoft 365 e que os proprietários aprovem ou neguem solicitações de associação. Sem uma licença de Azure Active Directory Premium, os usuários ainda podem gerenciar seus grupos no painel de acesso, mas não podem criar um grupo que exija aprovação de proprietário no painel de acesso e não podem solicitar a União de um grupo.

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciar o acesso a recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para definir configurações de grupo](../enterprise-users/groups-settings-cmdlets.md)
* [Gerenciamento de aplicativos no Microsoft Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar suas identidades locais com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
