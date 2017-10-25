---
title: Usar grupos para gerenciar o acesso a recursos no Azure Active Directory | Microsoft Docs
description: "Como usar grupos no Azure Active Directory para gerenciar o acesso de usuário a aplicativos e recursos locais e na nuvem."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 714120d0-cdf9-465d-afee-39bef591c6b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 3a4764ddf2dea68bf25bd46b9691429ad3341678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-resources-with-azure-active-directory-groups"></a>Gerenciar o acesso a recursos com grupos do Azure Active Directory
O AD do Azure (Active Directory do Azure) é uma solução abrangente de gerenciamento de acesso e identidades que oferece um conjunto robusto de recursos para gerenciar o acesso aos recursos e aplicativos na nuvem e locais, incluindo serviços online da Microsoft como o Office 365 e uma infinidade de aplicativos SaaS que não são da Microsoft. Este artigo fornece uma visão geral, mas se você deseja começar a usar os grupos do Azure AD agora, siga as instruções em [Gerenciamento de grupos de segurança no Azure AD](active-directory-accessmanagement-manage-groups.md). Para ver como você pode usar o PowerShell para gerenciar grupos no Azure Active Directory, você pode ler mais em [Cmdlets do Azure Active Directory para gerenciamento de grupo](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

> [!NOTE]
> Para usar o Active Directory do Azure, você precisa de uma conta do Azure. Se você ainda não tem uma conta, você pode [inscrever-se para uma conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
>
>

No Azure AD, um dos principais recursos é a capacidade de gerenciar o acesso aos recursos. Esses recursos podem ser parte do diretório, como no caso de permissões para gerenciar objetos por meio de funções no diretório ou podem ser externos ao diretório, como aplicativos SaaS, serviços do Azure e sites do SharePoint ou recursos locais. Há quatro maneiras de um usuário receber direitos de acesso a um recurso:

1. Atribuição direta

    Os usuários podem ser atribuídos diretamente a um recurso pelo proprietário do recurso.
2. Associação de grupo

    Um grupo pode ser atribuído a um recurso pelo proprietário do recurso e, dessa forma, os membros desse grupo recebem o acesso ao recurso. A associação do grupo pode então ser gerenciada pelo proprietário do grupo. O proprietário do recurso delega efetivamente ao proprietário do grupo a permissão para atribuir usuários aos seus recursos.
3. Com base em regra

    O proprietário do recurso pode usar uma regra para expressar quais usuários devem receber acesso a um recurso. O resultado da regra depende dos atributos usados na regra e seus valores para usuários específicos e, dessa forma, o proprietário do recurso delega efetivamente o direito de gerenciar o acesso aos seus recursos à fonte autoritativa para os atributos que são usados na regra. O proprietário do recurso ainda gerencia a regra e determina quais atributos e valores fornecem acesso aos seus recursos.
4. Autoridade externa

    O acesso a um recurso é derivado de uma fonte externa; por exemplo, um grupo que é sincronizado por meio de uma fonte autoritativa, como um diretório local, ou um aplicativo SaaS, como o WorkDay. O proprietário do recurso atribui o grupo para fornecer acesso ao recurso, e a fonte externa gerencia os membros do grupo.

   ![Visão geral do diagrama de gerenciamento de acesso](./media/active-directory-access-management-groups/access-management-overview.png)

## <a name="watch-a-video-that-explains-access-management"></a>Assista a um vídeo que explica o gerenciamento de acesso
Você pode assistir a um breve vídeo que explica mais sobre isso:

**Azure AD: introdução a associações dinâmicas para grupos**

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]
>
>

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Como funciona o gerenciamento de acesso no Azure Active Directory?
No centro da solução de gerenciamento de acesso do Azure AD está o grupo de segurança. Usar um grupo de segurança para gerenciar o acesso aos recursos é um paradigma conhecido, que possibilita uma maneira fácil de entender e flexível de fornecer acesso a um recurso para o grupo de usuários em questão. O proprietário do recurso (ou o administrador do diretório) pode atribuir um grupo para fornecer um determinado acesso aos recursos que possui. Os membros do grupo receberão o acesso, e o proprietário do recurso pode delegar o direito de gerenciar a lista de membros de um grupo a outra pessoa, como um gerente de departamento ou um administrador de assistência técnica.

![Diagrama de gerenciamento de acesso do Active Directory do Azure](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

O proprietário de um grupo também pode disponibilizar esse grupo para solicitações de autoatendimento. Dessa forma, um usuário final pode pesquisar e localizar o grupo e fazer uma solicitação para ingressar nele, buscando efetivamente permissão para acessar os recursos que são gerenciados por meio do grupo. O proprietário do grupo pode configurar o grupo para que as solicitações de ingresso sejam aprovadas automaticamente ou exijam a aprovação do proprietário do grupo. Quando um usuário faz uma solicitação para ingressar em um grupo, ela é encaminhada aos proprietários do grupo. Se um dos proprietários aprovar a solicitação, o usuário solicitante será notificado e o usuário será ingressado ao grupo. Se um dos proprietários recusar a solicitação, o usuário solicitante será notificado, mas não será ingressado ao grupo.

## <a name="getting-started-with-access-management"></a>Introdução ao gerenciamento de acesso
Pronto para começar? Você deve experimentar algumas das tarefas básicas que pode fazer com grupos do AD do Azure. Use esses recursos para fornecer acesso especializado a diferentes grupos de pessoas para recursos diferentes em sua organização. Uma lista das primeiras etapas básicas é exibida abaixo.

* [Criação de uma regra simples para configurar associações dinâmicas para um grupo](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)
* [Uso de um grupo para gerenciar o acesso a aplicativos SaaS](active-directory-accessmanagement-group-saasapps.md)
* [Disponibilização de um grupo para autoatendimento do usuário final](active-directory-accessmanagement-self-service-group-management.md)
* [Sincronização de um grupo local para o Azure usando o Azure AD Connect](active-directory-aadconnect.md)
* [Gerenciando proprietários de um grupo](active-directory-accessmanagement-managing-group-owners.md)

## <a name="next-steps"></a>Próximas etapas
Agora que você compreendeu as noções básicas do gerenciamento de acesso, aqui estão alguns recursos avançados adicionais disponíveis no Active Directory do Azure para gerenciar o acesso aos seus aplicativos e recursos.

* [Usar atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md)
* [Gerenciamento de grupos de segurança no Azure AD](active-directory-accessmanagement-manage-groups.md)
* [Configuração de grupos dedicados no Azure AD](active-directory-accessmanagement-dedicated-groups.md)
* [Referência de API do Graph para grupos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)
* [Cmdlets do Azure Active Directory para definir configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
