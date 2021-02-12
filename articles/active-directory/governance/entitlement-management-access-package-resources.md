---
title: Alterar funções de recurso para um pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como alterar as funções de recurso para um pacote do Access existente no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82247ae9817e5ff2e26d0862230654021876406b
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223249"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar funções de recurso para um pacote de acesso no gerenciamento de direitos do Azure AD

Como um Gerenciador de pacotes do Access, você pode alterar os recursos em um pacote do Access a qualquer momento sem se preocupar com o provisionamento do acesso do usuário aos novos recursos ou a remoção de seu acesso dos recursos anteriores. Este artigo descreve como alterar as funções de recurso para um pacote do Access existente.

Este vídeo fornece uma visão geral de como alterar um pacote de acesso.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Verificar o catálogo em busca de recursos

Se você precisar adicionar recursos a um pacote do Access, verifique se os recursos necessários estão disponíveis no catálogo. Se você for um Gerenciador de pacotes do Access, não poderá adicionar recursos a um catálogo, mesmo que você os tenha. Você está restrito a usar os recursos disponíveis no catálogo.

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Catálogo** e, em seguida, abra o catálogo.

1. No menu à esquerda, clique em **recursos** para ver a lista de recursos neste catálogo.

    ![Lista de recursos em um catálogo](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Se você for um Gerenciador de pacotes do Access e precisar adicionar recursos ao catálogo, poderá pedir ao proprietário do catálogo para adicioná-los.

## <a name="add-resource-roles"></a>Adicionar funções de recurso

Uma função de recurso é uma coleção de permissões associadas a um recurso. A maneira de disponibilizar os recursos para os usuários solicitarem é adicionando funções de recurso ao seu pacote de acesso. Você pode adicionar funções de recursos para grupos, equipes, aplicativos e sites do SharePoint.

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **funções de recurso**.

1. Clique em **adicionar funções de recurso** para abrir a página Adicionar funções de recurso ao pacote de acesso.

    ![Pacote de acesso – adicionar funções de recurso](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Dependendo se você deseja adicionar um grupo, uma equipe, um aplicativo ou um site do SharePoint, execute as etapas em uma das seções de função de recurso a seguir.

## <a name="add-a-group-or-team-resource-role"></a>Adicionar uma função de recurso de equipe ou grupo

Você pode ter o gerenciamento de direitos para adicionar automaticamente usuários a um grupo ou a uma equipe do Microsoft Teams quando eles forem atribuídos a um pacote de acesso. 

- Quando um grupo ou equipe faz parte de um pacote do Access e um usuário é atribuído a esse pacote do Access, o usuário é adicionado a esse grupo ou equipe, se ainda não estiver presente.
- Quando a atribuição de pacote de acesso de um usuário expira, elas são removidas do grupo ou da equipe, a menos que tenham atualmente uma atribuição a outro pacote de acesso que inclua o mesmo grupo ou equipe.

Você pode selecionar qualquer [grupo de segurança do Azure ad ou grupo de Microsoft 365](../fundamentals/active-directory-groups-create-azure-portal.md). Os administradores podem adicionar qualquer grupo a um catálogo; os proprietários do catálogo podem adicionar qualquer grupo ao catálogo se eles forem proprietários do grupo. Tenha em mente as seguintes restrições do Azure AD ao selecionar um grupo:

- Quando um usuário, incluindo um convidado, é adicionado como um membro a um grupo ou equipe, ele pode ver todos os outros membros desse grupo ou equipe.
- O Azure AD não pode alterar a associação de um grupo que foi sincronizado do Windows Server Active Directory usando Azure AD Connect ou que foi criado no Exchange Online como um grupo de distribuição.  
- A associação de grupos dinâmicos não pode ser atualizada com a adição ou remoção de um membro, portanto, as associações de grupo dinâmicos não são adequadas para uso com o gerenciamento de direitos.

Para obter mais informações, consulte [comparar grupos](/office365/admin/create-groups/compare-groups) e [grupos de Microsoft 365 e Microsoft Teams](/microsoftteams/office-365-groups).

1. Na página **adicionar funções de recurso ao pacote de acesso** , clique em **grupos e equipes** para abrir o painel Selecionar grupos.

1. Selecione os grupos e as equipes que você deseja incluir no pacote de acesso.

    ![Pacote de acesso – adicionar funções de recurso-selecionar grupos](./media/entitlement-management-access-package-resources/group-select.png)

1. Clique em **Selecionar**.

    Depois de selecionar o grupo ou a equipe, a coluna de **subtipo** listará um dos seguintes subtipos:

    | Subtipo | Descrição |
    | --- | --- |
    | Segurança | Usado para conceder acesso aos recursos. |
    | Distribuição | Usado para enviar notificações para um grupo de pessoas. |
    | Microsoft 365 | Microsoft 365 grupo que não está habilitado para equipes. Usado para colaboração entre usuários, dentro e fora da sua empresa. |
    | Equipe | Microsoft 365 grupo que é habilitado para equipes. Usado para colaboração entre usuários, dentro e fora da sua empresa. |

1. Na lista **função** , selecione **proprietário** ou **membro**.

    Normalmente, você seleciona a função de membro. Se você selecionar a função proprietário, isso permitirá que os usuários adicionem ou removam outros membros ou proprietários.

    ![Pacote de acesso-Adicionar função de recurso para um grupo ou equipe](./media/entitlement-management-access-package-resources/group-role.png)

1. Clique em **Adicionar**.

    Qualquer usuário com atribuições existentes ao pacote de acesso se tornará automaticamente membros deste grupo ou equipe quando ele for adicionado.

## <a name="add-an-application-resource-role"></a>Adicionar uma função de recurso de aplicativo

Você pode fazer com que o Azure AD atribua automaticamente aos usuários o acesso a um aplicativo empresarial do Azure AD, incluindo aplicativos SaaS e aplicativos de sua organização federados ao Azure AD, quando um usuário recebe um pacote de acesso. Para aplicativos que se integram ao Azure AD por meio de logon único federado, o Azure AD emitirá tokens de Federação para usuários atribuídos ao aplicativo.

Os aplicativos podem ter várias funções. Ao adicionar um aplicativo a um pacote do Access, se esse aplicativo tiver mais de uma função, será necessário especificar a função apropriada para esses usuários. Se você estiver desenvolvendo aplicativos, poderá ler mais sobre como essas funções são adicionadas aos seus aplicativos em [como configurar a declaração de função emitida no token SAML para aplicativos empresariais](../develop/active-directory-enterprise-app-role-management.md).

Quando uma função de aplicativo faz parte de um pacote de acesso:

- Quando um usuário recebe esse pacote de acesso, o usuário é adicionado a essa função de aplicativo, se ainda não estiver presente.
- Quando a atribuição de pacote de acesso de um usuário expirar, seu acesso será removido do aplicativo, a menos que eles tenham uma atribuição a outro pacote de acesso que inclua essa função de aplicativo.

Aqui estão algumas considerações ao selecionar um aplicativo:

- Os aplicativos também podem ter grupos atribuídos às suas funções.  Você pode optar por adicionar um grupo no lugar de uma função de aplicativo em um pacote do Access, no entanto, o aplicativo não ficará visível para o usuário como parte do pacote de acesso no meu portal de acesso.

1. Na página **adicionar funções de recurso ao pacote de acesso** , clique em **aplicativos** para abrir o painel Selecionar aplicativos.

1. Selecione os aplicativos que você deseja incluir no pacote de acesso.

    ![Pacote de acesso – adicionar funções de recurso-selecionar aplicativos](./media/entitlement-management-access-package-resources/application-select.png)

1. Clique em **Selecionar**.

1. Na lista **função** , selecione uma função de aplicativo.

    ![Pacote de acesso-Adicionar função de recurso para um aplicativo](./media/entitlement-management-access-package-resources/application-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com atribuições existentes ao pacote de acesso receberão automaticamente acesso a esse aplicativo quando ele for adicionado.

## <a name="add-a-sharepoint-site-resource-role"></a>Adicionar uma função de recurso de site do SharePoint

O Azure AD pode atribuir automaticamente acesso de usuários a um site do SharePoint Online ou a um conjunto de sites do SharePoint Online quando eles recebem um pacote de acesso.

1. Na página **adicionar funções de recurso ao pacote de acesso** , clique em **sites do SharePoint** para abrir o painel selecionar sites do SharePoint Online.

    :::image type="content" source="media/entitlement-management-access-package-resources/sharepoint-multigeo-portal.png" alt-text="Pacote de acesso-adicionar funções de recurso-selecionar sites do SharePoint-exibição do portal":::

1. Se você tiver [várias geografias](https://docs.microsoft.com/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365?view=o365-worldwide) habilitadas para o SharePoint, selecione o ambiente do qual deseja selecionar sites.
    
    :::image type="content" source="media/entitlement-management-access-package-resources/sharepoint-multigeo-select.png" alt-text="Pacote de acesso-adicionar funções de recurso-selecionar sites de várias regiões do SharePoint":::

1. Se várias regiões geográficas não estiverem habilitadas, você não precisará selecionar um ambiente. Selecione os sites do SharePoint Online que você deseja incluir no pacote de acesso.

    ![Pacote de acesso-adicionar funções de recurso-selecionar sites do SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Clique em **Selecionar**.

1. Na lista **função** , selecione uma função de site do SharePoint Online.

    ![Pacote de acesso-Adicionar função de recurso para um site do SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com atribuições existentes ao pacote de acesso receberão automaticamente acesso a esse site do SharePoint Online quando ele for adicionado.

## <a name="remove-resource-roles"></a>Remover funções de recurso

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **funções de recurso**.

1. Na lista de funções de recurso, localize a função de recurso que você deseja remover.

1. Clique nas reticências (**...**) e, em seguida, clique em **remover função de recurso**.

    Todos os usuários com atribuições existentes ao pacote de acesso terão automaticamente seu acesso revogado para essa função de recurso quando ele for removido.

## <a name="when-changes-are-applied"></a>Quando as alterações são aplicadas

No gerenciamento de direitos, o Azure AD processará alterações em massa para atribuição e recursos em seus pacotes de acesso várias vezes por dia. Portanto, se você fizer uma atribuição ou alterar as funções de recurso do seu pacote de acesso, poderá levar até 24 horas para que essa alteração seja feita no Azure AD, além da quantidade de tempo que leva para propagar essas alterações para outros serviços online da Microsoft ou aplicativos SaaS conectados. Se a alteração afetar apenas alguns objetos, a alteração provavelmente levará apenas alguns minutos para ser aplicada ao Azure AD, após o qual outros componentes do Azure AD detectarão essa alteração e atualizarão os aplicativos SaaS. Se a alteração afetar milhares de objetos, a alteração levará mais tempo. Por exemplo, se você tiver um pacote de acesso com 2 aplicativos e 100 atribuições de usuário e decidir adicionar uma função de site do SharePoint ao pacote de acesso, pode haver um atraso até que todos os usuários façam parte dessa função de site do SharePoint. Você pode monitorar o progresso por meio do log de auditoria do Azure AD, do log de provisionamento do Azure AD e dos logs de auditoria do site do SharePoint.

Quando você remove um membro de uma equipe, eles são removidos do grupo de Microsoft 365 também. A remoção da funcionalidade de chat da equipe pode ser atrasada. Para obter mais informações, consulte [Associação de grupo](/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Próximas etapas

- [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Como configurar a declaração de função emitida no token SAML para aplicativos empresariais](../develop/active-directory-enterprise-app-role-management.md)
- [Introdução ao SharePoint Online](/sharepoint/introduction)
