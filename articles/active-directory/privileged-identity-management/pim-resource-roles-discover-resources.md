---
title: Descobrir recursos do Azure para gerenciar no PIM – Azure AD | Microsoft Docs
description: Saiba como descobrir recursos do Azure para gerenciar no Azure AD PIM (Privileged Identity Management).
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
ms.date: 09/29/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d38990289169412f06b0c7e4bcbdf67f688da7
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539002"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Descubra os recursos do Azure para gerenciar no Privileged Identity Management

Usando o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode melhorar a proteção dos recursos do Azure. Isso é útil para:

- Organizações que já usam Privileged Identity Management para proteger as funções do Azure AD
- Os proprietários do grupo de gerenciamento e da assinatura que estão tentando proteger os recursos de produção

Ao configurar o Privileged Identity Management para recursos do Azure pela primeira vez, você precisa descobrir e selecionar os recursos a serem protegidos com Privileged Identity Management. Não há limite para o número de recursos que você pode gerenciar com Privileged Identity Management. No entanto, é recomendável começar com seus recursos de produção mais críticos.

## <a name="discover-resources"></a>Descobrir recursos

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **recursos do Azure**.

    Se esta for a primeira vez que você usa Privileged Identity Management para recursos do Azure, você verá uma página **descobrir recursos** .

    ![Descobrir o painel de recursos sem recursos listados para a primeira experiência](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se outro administrador em sua organização já estiver gerenciando recursos do Azure no Privileged Identity Management, você verá uma lista dos recursos que estão sendo gerenciados no momento.

    ![Painel descobrir recursos listando recursos que estão sendo gerenciados no momento](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Selecione **descobrir recursos** para iniciar a experiência de descoberta.

    ![O painel de descoberta lista os recursos que podem ser gerenciados, como assinaturas e grupos de gerenciamento](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Na página **descoberta** , use **filtro de estado do recurso** e **selecione tipo de recurso** para filtrar os grupos de gerenciamento ou assinaturas para os quais você tem permissão de gravação. Ele provavelmente é mais fácil para começar **todos** os inicialmente.

   Você pode pesquisar e selecionar recursos de assinatura ou grupo de gerenciamento para gerenciar no Privileged Identity Management. Ao gerenciar um grupo de gerenciamento ou uma assinatura no Privileged Identity Management, você também pode gerenciar seus recursos filho.

   > [!Note]
   > Ao adicionar um novo recurso filho do Azure a um grupo de gerenciamento gerenciado por PIM, você pode colocar o recurso filho em gerenciamento pesquisando-o em PIM.

1. Selecione os recursos não gerenciados que você deseja gerenciar.

1. Selecione **gerenciar recurso** para começar a gerenciar os recursos selecionados.

    > [!NOTE]
    > Depois que um grupo de gerenciamento ou assinatura é gerenciado, ele não pode ser não gerenciado. Isso impede que outro administrador de recursos remova Privileged Identity Management configurações.

    ![Painel de descoberta com um recurso selecionado e a opção de gerenciar recurso realçada](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Se você vir uma mensagem para confirmar a integração do recurso selecionado para gerenciamento, selecione **Sim**. O PIM será então configurado para gerenciar todos os objetos filho novos e existentes sob os recursos.

    ![Mensagem confirmando a integração dos recursos selecionados para gerenciamento](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)
