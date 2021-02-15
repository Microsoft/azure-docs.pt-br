---
title: Identificar um grupo para gerenciar no Privileged Identity Management-Azure AD | Microsoft Docs
description: Saiba como integrar grupos de funções de atribuição para gerenciar como grupos de acesso privilegiado no Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ef27475509c59b3c0caf194fd85530f3fdd6c7c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367832"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Coloque os grupos de acesso privilegiado (versão prévia) no Privileged Identity Management

No Azure Active Directory (AD do Azure), você pode atribuir funções internas do Azure AD a grupos de nuvem para simplificar a forma como você gerencia as atribuições de função. Para proteger as funções do Azure AD e proteger o acesso, agora você pode usar Privileged Identity Management (PIM) para gerenciar o acesso just-in-time para membros ou proprietários desses grupos. Para gerenciar um grupo de funções do Azure AD que pode ser atribuído como um grupo de acesso privilegiado no Privileged Identity Management, você deve colocá-lo sob gerenciamento no PIM.

## <a name="identify-groups-to-manage"></a>Identificar grupos a serem gerenciados

Você pode criar um grupo de função atribuível no Azure AD, conforme descrito em [criar um grupo de função-atribuível no Azure Active Directory](../roles/groups-create-eligible.md). Você é um proprietário do grupo para colocá-lo sob gerenciamento com Privileged Identity Management.

1. [Entre no Azure ad](https://aad.portal.azure.com) com permissões de função de administrador de função privilegiada.
1. Selecione **grupos** e, em seguida, selecione o grupo de funções que você deseja gerenciar no PIM. Você pode pesquisar e filtrar a lista.

    ![localizar um grupo de funções que pode ser atribuído para gerenciar no PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Abra o grupo e selecione **acesso privilegiado (versão prévia)**.

    ![Abrir a experiência de Privileged Identity Management](./media/groups-discover-groups/groups-discover-groups.png)

1. Comece a gerenciar atribuições no PIM.

    ![Gerenciar atribuições no Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Depois que um grupo de acesso privilegiado é gerenciado, ele não pode ser retirado do gerenciamento. Isso impede que outro administrador de recursos remova Privileged Identity Management configurações.

## <a name="next-steps"></a>Próximas etapas

- [Configurar atribuições de grupo de acesso privilegiado no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Atribuir grupos de acesso privilegiado no Privileged Identity Management](pim-resource-roles-assign-roles.md)
