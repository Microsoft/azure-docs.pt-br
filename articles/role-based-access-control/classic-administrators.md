---
title: Administradores da assinatura clássica do Azure | Microsoft Docs
description: Descreve como adicionar ou alterar as funções Administrador de Serviços e Coadministrador do Azure e como exibir o Administrador da Conta.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c2096b6c5ddb72c8ab5c5d3203a05c94db51f6c5
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444344"
---
# <a name="azure-classic-subscription-administrators"></a>Administradores da assinatura clássica do Azure

A Microsoft recomenda que você gerencie o acesso aos recursos do Azure usando o RBAC (controle de acesso baseado em função). No entanto, se você ainda estiver usando o modelo de implantação clássico, será necessário usar uma função de administrador da assinatura clássica: Administrador de Serviços e Coadministrador. Para obter mais informações, consulte [Azure Resource Manager versus implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md).

Este artigo descreve como adicionar ou alterar as funções Administrador de Serviços e Coadministrador e como exibir o Administrador da Conta.

## <a name="add-a-co-administrator"></a>Adicionar um Coadministrador

> [!TIP]
> É necessário adicionar um Coadministrador somente se o usuário precisar gerenciar implantações clássicas do Azure usando o [Módulo do PowerShell para Gerenciamento de Serviços do Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Se o usuário usa apenas o portal do Azure para gerenciar os recursos clássicos, não é preciso adicionar o administrador clássico para ele.

1. Entre no [portal do Azure](https://portal.azure.com) como um Administrador de Serviços.

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

    Coadministradores somente podem ser atribuídos no escopo da assinatura.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **Administradores clássicos**.

    ![Captura de tela que abre Administradores clássicos](./media/classic-administrators/classic-administrators.png)

1. Clique em **Adicionar** > **Adicionar coadministrador** para abrir o painel Adicionar coadministradores.

    Se a opção Adicionar coadministrador estiver desabilitada, você não terá permissões.

1. Selecione o usuário que você quer adicionar e clique em **Adicionar**.

    ![Captura de tela que adiciona o coadministrador](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Adicionar um usuário convidado como um Coadministrador

[Usuários convidados](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) aos quais foram atribuídas a função Coadministrador poderão ver algumas diferenças em comparação com os usuários membros com a função Coadministrador. Considere este cenário:

- O usuário A com uma conta corporativa ou de estudante do Azure AD é um Administrador de Serviços de uma assinatura do Azure.
- O usuário B tem uma conta da Microsoft.
- O usuário A atribui a função Coadministrador ao usuário B.
- O usuário B pode fazer quase tudo, mas não pode registrar aplicativos ou pesquisar usuários no diretório do Azure AD.

Você esperava que o usuário B pudesse gerenciar tudo. O motivo para essa diferença é que a conta da Microsoft é adicionada à assinatura como um usuário convidado, em vez de um usuário membro. Os usuários convidados têm permissões padrão diferentes no Azure AD em comparação com os usuários membros. Por exemplo, usuários membros podem ler outros usuários no Azure AD, e os usuários convidados não podem. Os usuários membros podem registrar novas entidades de serviço no Azure AD, e os usuários convidados não podem. Se houver a necessidade de um usuário convidado executar essas tarefas, uma solução possível é atribuir as funções de administrador específicas do Azure AD que o usuário convidado precisa. Por exemplo, no cenário anterior, você pode atribuir a função [Leitores de Diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) para ler outros usuários e atribuir a função [Desenvolvedor de Aplicativos](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) para criar entidades de serviço. Para saber mais sobre os usuários membros e convidados e suas permissões, consulte [Quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

Observe que as [funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md) são diferentes das [funções de administrador do Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). As funções internas não concedem acesso ao Azure AD. Para saber mais, consulte [Compreender as diferentes funções](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Remover um Coadministrador

1. Entre no [portal do Azure](https://portal.azure.com) como um Administrador de Serviços.

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **Administradores clássicos**.

1. Adicione uma marca de seleção ao lado do Coadministrador que você quer remover.

1. Clique em **Remover**.

1. Na caixa de mensagem que é exibida, clique em **Sim**.

    ![Captura de tela que remove o coadministrador](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Alterar o administrador do serviço

Somente o Administrador da Conta pode alterar o Administrador de Serviços de uma assinatura. Por padrão, ao inscrever-se em uma assinatura do Azure, o Administrador de Serviços é o mesmo que o Administrador da Conta. O usuário com a função Administrador da Conta não tem acesso ao portal do Azure. O usuário com a função Administrador de Serviços tem acesso total ao portal do Azure. Se o Administrador da Conta e o Administrador de Serviços forem o mesmo usuário e você alterar o Administrador de Serviços para um usuário diferente, o Administrador da Conta perderá o acesso ao portal do Azure. No entanto, o Administrador da Conta sempre poderá usar o Centro de Contas para alterar o Administrador de Serviços de volta para si mesmo.

Há duas maneiras de alterar o Administrador de Serviços. É possível alterar no **portal do Azure** ou **Centro de Contas**.

### <a name="azure-portal"></a>Portal do Azure

1. Verifique se o cenário tem suporte verificando as limitações para alterar os administradores de serviço.

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

1. Clique em **Propriedades**.

    ![Captura de tela mostrando o Administrador da Conta](./media/classic-administrators/account-admin.png)

1. Na parte superior, clique em **Administrador de Serviços** para abrir o painel do Administrador de Serviços.

    Se o botão de Administrador de Serviços estiver desabilitado, você não terá permissões. Somente o usuário que é o Administrador da Conta poderá alterar o Administrador de Serviços.

1. Selecione um novo Administrador de Serviços e clique em **Salvar**.

### <a name="account-center"></a>Centro de contas

1. Verifique se o cenário tem suporte verificando as limitações para alterar os administradores de serviço.

1. Entre no [Centro de Contas](https://account.windowsazure.com/subscriptions) como o Administrador da Conta.

1. Clique em uma assinatura.

1. No lado direito, clique em **Editar detalhes da assinatura**.

    ![Captura de tela mostrando o botão Editar assinatura no Centro de Contas](./media/classic-administrators/editsub.png)

1. Na caixa **ADMINISTRADOR DE SERVIÇOS** , insira o endereço de email do novo administrador de serviços.

    ![Captura de tela mostrando a caixa para alterar o email do Administrador de Serviços](./media/classic-administrators/change-service-admin.png)

1. Clique na marca de seleção para salvar a alteração.

### <a name="limitations-for-changing-the-service-administrator"></a>Limitações para alterar o Administrador de Serviços

Cada assinatura está associada a um diretório do Azure AD. Para localizar o diretório ao qual a assinatura está associada, abra **Assinaturas** no portal do Azure e selecione uma assinatura para visualizar o diretório.

Se você estiver conectado com uma conta corporativa ou de estudante, poderá adicionar outras contas na organização como Administrador de Serviços. Por exemplo, abby@contoso.com pode adicionar bob@contoso.com como Administrador de Serviços, mas não pode adicionar john@notcontoso.com, a menos que john@notcontoso.com esteja no diretório contoso.com. Usuários conectados com contas corporativas ou de estudante podem continuar adicionando usuários da conta da Microsoft como Administrador de Serviços. Só pode haver um administrador de serviços por assinatura do Azure.

  | Método de entrada | Adicionar usuário da conta Microsoft como Administrador de Serviços? | Adicionar conta corporativa ou de estudante na mesma organização que um Administrador de Serviços? | Adicionar conta corporativa ou de estudante em organização diferente como Administrador de Serviços? |
  | --- | --- | --- | --- |
  |  Conta da Microsoft |Sim |Não |Não |
  |  Conta corporativa ou de estudante |Sim |sim |Não |

## <a name="view-the-account-administrator"></a>Exibir o Administrador da Conta

O Administrador da Conta é o usuário que inscreveu-se inicialmente para a assinatura do Azure e é responsável como o proprietário de cobrança da assinatura. Para alterar o Administrador de Conta de uma assinatura, consulte [Transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md).

Siga estas etapas para exibir o Administrador da Conta.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

1. Clique em **Propriedades**.

    O Administrador da Conta da assinatura é exibido na caixa **Administrador da Conta**.

    ![Captura de tela mostrando o Administrador da Conta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Próximas etapas

* [Entender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md)
* [Adicionar ou alterar administradores de assinatura do Azure](../billing/billing-add-change-azure-subscription-administrator.md)
