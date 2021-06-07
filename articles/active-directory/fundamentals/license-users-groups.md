---
title: Atribuir ou remover licenças - Azure Active Directory | Microsoft Docs
description: Instruções sobre como atribuir ou remover licenças do Azure Active Directory de seus usuários ou grupos.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6900647acf7182529f34c8cc065dbb039de38be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504399"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Atribuir ou remover licenças no portal de Azure Active Directory

Muitos serviços do Azure Active Directory (AD do Azure) exigem que você licenciasse cada um de seus usuários ou grupos (e membros associados) para esse serviço. Somente os usuários com licenças ativas poderão acessar e usar os serviços do Azure AD licenciados para os quais isso é verdade. As licenças são aplicadas por locatário e não são transferidas para outros locatários. 

## <a name="available-license-plans"></a>Planos de licença disponíveis

Há vários planos de licença disponíveis para o serviço do Azure AD, incluindo:

- AD do Azure Gratuito

- Azure AD Premium P1

- Azure AD Premium P2

Para obter informações específicas sobre cada plano de licença e os detalhes de licenciamento associados, consulte [qual licença eu preciso?](https://azure.microsoft.com/pricing/details/active-directory/). Para se inscrever nos planos de licença do Azure AD Premium, veja [aqui](./active-directory-get-started-premium.md).

Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes que uma licença possa ser atribuída a um grupo, você deve especificar o **Local de uso** para todos os membros. Você pode definir esse valor **Azure Active Directory &gt; os usuários &gt; perfil &gt; configurações** área no Azure AD. Qualquer usuário cujo local de uso não seja especificado herda o local da organização do Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Exibir planos de licença e detalhes do plano

Você pode exibir os planos de serviço disponíveis, incluindo as licenças individuais, verificar datas de vencimento pendentes e exibir o número de atribuições disponíveis.

### <a name="to-find-your-service-plan-and-plan-details"></a>Para localizar o plano de serviço e os detalhes do plano

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de licenças na sua organização do Azure AD.

1. Selecione **Active Directory do Azure** e, em seguida, selecione **Licenças**.

    :::image type="content" source="media/license-users-groups/license-details-blade.png" alt-text="Página licenças, com número de serviços comprados e licenças atribuídas":::

1. Selecione **todos os produtos** para exibir a página todos os produtos e ver os números **totais**, **atribuídos**, **disponíveis** e **expirando em breve** para seus planos de licença.

    :::image type="content" source="media/license-users-groups/license-products-blade-with-products.png" alt-text="página de serviços-com planos de licença de serviço – informações de licença associadas":::

    > [!NOTE]
    > Os números são definidos como: 
    > - Total: número total de licenças adquiridas
    > - Atribuído: número de licenças atribuídas aos usuários
    > - Disponível: número de licenças disponíveis para atribuição, incluindo expirando em breve
    > - Expirando em breve: número de licenças expirando em breve

1. Selecione um nome de plano para ver seus usuários e grupos licenciados.

## <a name="assign-licenses-to-users-or-groups"></a>Atribuir licenças a usuários ou grupos

Certifique-se de que qualquer pessoa que precise usar um serviço licenciado do Azure AD tenha a licença apropriada. Você pode adicionar os direitos de licenciamento a usuários ou a um grupo inteiro.

### <a name="to-assign-a-license-to-a-user"></a>Para atribuir uma licença a um usuário

1. Na página **produtos** , selecione o nome do plano de licença que você deseja atribuir ao usuário.

    ![página de serviços, com plano de licença de serviço realçado](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na página Visão geral do plano de licença, selecione **atribuir**.

    ![página serviços, com a opção atribuir realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na página **Atribuir**, selecione **Usuários e grupos** e pesquise e selecione o usuário que você está atribuindo a licença.

    ![Atribuir página de licença, com pesquisa realçada e opções de seleção](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Selecione **Opções de atribuição**, verifique se você tem as opções de licença apropriadas ativadas e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença](media/license-users-groups/license-option-blade-assignments.png)

    A página **Atribuir licença** é atualizada para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

    > [!NOTE]
    > Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes que uma licença possa ser atribuída a um usuário, você deve especificar o **Local de uso**. Você pode definir esse valor **Azure Active Directory &gt; os usuários &gt; perfil &gt; configurações** área no Azure AD. Qualquer usuário cujo local de uso não seja especificado herda o local da organização do Azure AD.

1. Selecione **Atribuir**.

    O usuário é adicionado à lista de usuários licenciados e tem acesso aos serviços incluídos do Azure AD.
    > [!NOTE]
    > As licenças também podem ser atribuídas diretamente a um usuário na página **licenças** do usuário. Se um usuário tiver uma licença atribuída por meio de uma associação de grupo e você quiser atribuir a mesma licença diretamente ao usuário, ele só poderá ser feito na página **produtos** mencionada na etapa 1.

### <a name="to-assign-a-license-to-a-group"></a>Para atribuir uma licença a um grupo

1. Na página **produtos** , selecione o nome do plano de licença que você deseja atribuir ao usuário.

    ![Folha produtos, com plano de licença do produto realçado](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na página **Plano 2 do Active Directory do Azure 2**, selecione **Atribuir**.

    ![Página de produtos, com a opção de atribuir realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na página **Atribuir**, selecione **Usuários e grupos** e pesquise e selecione o grupo que você está atribuindo a licença.

    ![Página atribuir licença, com a pesquisa realçada e selecione as opções 2](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Selecione **Opções de atribuição**, verifique se você tem as opções de licença apropriadas ativadas e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença 2](media/license-users-groups/license-option-blade-group-assignments.png)

    A página **Atribuir licença** é atualizada para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

1. Selecione **Atribuir**.

    O grupo é adicionado à lista de grupos licenciados e todos os membros têm acesso aos serviços incluídos do Azure AD.

## <a name="remove-a-license"></a>Remover uma licença

Você pode remover uma licença da página de usuário do Azure AD de um usuário, na página Visão geral do grupo de uma atribuição de grupo ou a partir da página **licenças** do Azure ad para ver os usuários e grupos de uma licença.

### <a name="to-remove-a-license-from-a-user"></a>Para remover uma licença de um usuário

1. Na página **usuários licenciados** para o plano de serviço, selecione o usuário que não deve mais ter a licença. Por exemplo, _Alain Charon_.

1. Selecione **remover licença**.

    ![Página de usuários licenciados com a opção de licença de remover realçada](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> As licenças que um usuário herda de um grupo não podem ser removidas diretamente. Em vez disso, você precisa remover o usuário do grupo do qual ele está herdando a licença.

### <a name="to-remove-a-license-from-a-group"></a>Para remover uma licença de um grupo

1. Na página **grupos licenciados** do plano de licença, selecione o grupo que não deve mais ter a licença.

1. Selecione **remover licença**.

    ![Página grupos licenciados com a opção remover licença realçada 2](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Quando uma conta de usuário local sincronizada com o Azure AD fica fora do escopo da sincronização ou quando a sincronização é removida, o usuário é excluído de maneira reversível no Azure AD. Quando isso ocorrer, as licenças atribuídas ao usuário diretamente ou por meio do licenciamento baseado em grupo serão marcadas como **suspensas** em vez de **excluídas**.

## <a name="next-steps"></a>Próximas etapas

Depois de atribuir suas licenças, você pode executar os seguintes processos:

- [Identifique e resolva problemas de atribuição de licenças](../enterprise-users/licensing-groups-resolve-problems.md)

- [Adicione usuários licenciados a um grupo para licenciamento](../enterprise-users/licensing-groups-migrate-users.md)

- [Cenários, limitações e problemas conhecidos usando grupos para gerenciar o licenciamento no Azure Active Directory](../enterprise-users/licensing-group-advanced.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)
