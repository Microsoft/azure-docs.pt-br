---
title: "Configuração do gerenciamento de acesso de aplicativos de autoatendimento no Azure Active Directory | Microsoft Docs"
description: "Criar e gerenciar grupos de segurança ou grupos do Office 365 no Azure Active Directory e o grupo de segurança de solicitação ou associações de grupo do Office 365"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 904d5c70-c34a-46c4-a9a7-d1efecf4821c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 7a7370eb076ba8602a58a260a14bb863c55bc803
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>Configurar o Azure Active Directory para gerenciamento de grupo de autoatendimento
Os usuários podem criar e gerenciar seus próprios grupos de segurança ou grupos do Office 365 no Azure Active Directory (Azure AD). Os usuários também podem solicitar associações ao grupo de segurança ou ao grupo do Office 365, e o proprietário do grupo pode aprovar ou recusar a associação. O controle diário da associação de grupo pode ser delegado a pessoas que compreendem o contexto de negócios da associação. Os recursos de gerenciamento de grupo de autoatendimento só estão disponíveis para grupos de segurança e para grupos do Office 365, mas não para grupos de segurança habilitados para email ou listas de distribuição.

Atualmente, o gerenciamento de grupo de autoatendimento serve dois cenários essenciais: gerenciamento de grupo delegado e gerenciamento de grupo de autoatendimento.

* **Gerenciamento de grupo delegado** Um exemplo é um administrador que está gerenciando o acesso a um aplicativo SaaS que a empresa está usando. O gerenciamento desses direitos de acesso está se tornando inconveniente, portanto esse administrador solicita ao proprietário de negócios para criar um novo grupo. O administrador atribui acesso ao aplicativo para o novo grupo e adiciona ao grupo todas as pessoas que já acessam o aplicativo. O proprietário da empresa pode então adicionar mais usuários, e os usuários são automaticamente provisionados para o aplicativo. O proprietário da empresa não precisa esperar que o administrador gerencie o acesso para usuários. Se o administrador conceder a mesma permissão a um gerente de um grupo de negócios diferente, essa pessoa também poderá gerenciar o acesso para seus próprios usuários. O proprietário da empresa ou o gerente não podem exibir nem gerenciar os usuários uns dos outros. O administrador ainda pode ver todos os usuários que têm acesso ao aplicativo e direitos de acesso de bloco, se necessário.
* **Gerenciamento de grupo de autoatendimento** Um exemplo deste cenário consiste em dois usuários que têm sites do SharePoint Online que eles configuram de forma independente. Eles desejam fornecer às equipes um do outro acesso a seus sites. Para isso, eles podem criar um grupo no Azure AD e, no SharePoint Online, cada um seleciona esse grupo para fornecer acesso a seus sites. Quando alguém deseja acesso, solicita do painel de acesso, e depois da aprovação obtêm acesso a ambos os sites do SharePoint Online automaticamente. Posteriormente, um deles decide que todas as pessoas que acessam o site também devem obter acesso a determinado aplicativo SaaS. O administrador do aplicativo SaaS pode adicionar direitos de acesso ao aplicativo para o site do SharePoint Online. Depois, as solicitações que forem aprovadas darão acesso aos dois sites do SharePoint Online e também ao aplicativo SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Disponibilização de um grupo para autoatendimento do usuário
1. Entre no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global para o diretório.
2. Selecione **Usuários e grupos** e depois **Configurações de grupo**.
3. Defina **Gerenciamento de grupo de autoatendimento habilitado** como **Sim**.
4. Defina **Os usuários podem criar grupos de segurança** ou **Os usuários podem criar grupos do Office 365** como **Sim**.
  * Quando essas configurações forem habilitadas, todos os usuários em seu diretório terão permissão para criar novos grupos de segurança e adicionar membros a esses grupos. Esses novos grupos também aparecem no Painel de Acesso para todos os outros usuários. Se a configuração de política de grupo permitir isso, outros usuários poderão criar solicitações para ingressar nesses grupos. 
  * Quando essas configurações estiverem desabilitadas, os usuários não poderão criar grupos nem alterar grupos existentes dos quais são proprietários. No entanto, ainda podem gerenciar as associações desses grupos e aprovar solicitações de outros usuários para ingressar em seus grupos.

Você também pode usar **Usuários que podem gerenciar grupos de segurança** e **Usuários que podem gerenciar grupos do Office 365** para alcançar um controle de acesso mais granular sobre o gerenciamento de grupo de autoatendimento para seus usuários. Quando **Usuários podem criar grupos** estiver habilitada, todos os usuários em seu locatário terão permissão para criar novos grupos e adicionar membros a esses grupos. Ao defini-los como **Alguns**, você está restringindo grupo de gerenciamento a apenas um grupo limitado de usuários. Quando essa opção é definida como **Alguns**, você deve adicionar usuários ao grupo SSGMSecurityGroupsUsers para que eles possam criar novos grupos e adicionar membros a eles. A definição de **Usuários que podem usar autoatendimento para grupos de segurança** e de **Usuários que podem gerenciar grupos do Office 365** como **Todos**, você habilita todos os usuários em seu diretório a criar novos grupos.

Você também pode usar **Grupo que pode gerenciar grupos de segurança** ou **Grupo que pode gerenciar os grupos do Office 365** para especificar um único grupo cujos membros podem usar o autoatendimento.

## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciar o acesso a recursos com Grupos do Azure Active Directory](active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para definir configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [O que é o Active Directory do Azure?](active-directory-whatis.md)
* [Integrar suas identidades locais com o Azure Active Directory](active-directory-aadconnect.md)
