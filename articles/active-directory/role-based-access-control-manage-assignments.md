---
title: "Exibir atribuições de acesso a recursos do Azure | Microsoft Docs"
description: "Exibir e gerenciar todas as atribuições de Controle de Acesso Baseado em Função para um usuário ou grupo no portal do Azure"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: jeffsta
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: andredm
ms.openlocfilehash: 72c695d08bdf5de003d51ffb0768184e1e4d00ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal"></a>Exibir as atribuições de acesso para usuários e grupos no Portal do Azure
> [!div class="op_single_selector"]
> * [Gerenciar o acesso por usuário ou grupo](role-based-access-control-manage-assignments.md)
> * [Gerenciar o acesso por recurso](role-based-access-control-configure.md)

Com RBAC (controle de acesso baseado em função) na visualização do Azure AD (Azure Active Directory), você pode gerenciar o acesso aos recursos do Azure. 

O acesso atribuído com o RBAC é refinado porque há duas maneiras de restringir as permissões:

* **Escopo:** as atribuições de função RBAC passam para uma assinatura, um grupo de recursos ou um recurso específico. Um usuário que recebe acesso a um recurso único não pode acessar os outros recursos na mesma assinatura.
* **Função:** dentro do escopo da atribuição, o acesso é reduzido ainda mais com a atribuição de função. Funções podem ser de alto nível, como proprietário, ou específicas, como leitor de máquina virtual.

Funções só podem ser atribuídas de uma assinatura, grupo de recursos ou recurso que seja o escopo da atribuição. Mas você pode exibir todas as atribuições de acesso para determinado usuário ou grupo em um mesmo lugar. Em de cada assinatura, você pode ter até 2.000 atribuições de função. 

Saiba mais sobre como [Usar atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](role-based-access-control-configure.md).

## <a name="view-access-assignments"></a>Exibir atribuições de acesso
Para pesquisar as atribuições de acesso para um único usuário ou grupo, comece em Azure Active Directory no [portal do Azure](http://portal.azure.com).

1. Selecione **Azure Active Directory**. Se essa opção não estiver visível na sua lista de navegação, selecione **Mais Serviços** e role para baixo até encontrar **Azure Active Directory**.
2. Selecione **Usuários e Grupos** e **Todos os usuários** ou **Todos os grupos**. Para este exemplo, vamos focar em usuários individuais.
    ![Gerenciar usuários e grupos no Azure Active Directory – captura de tela](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Pesquise o usuário por nome ou nome de usuário.
4. Selecione **Recursos do Azure** na folha do usuário. Todas as atribuições de acesso para esse usuário são exibidas.

### <a name="read-permissions-to-view-assignments"></a>Permissões de leitura para exibir atribuições
Essa página mostra apenas as atribuições de acesso que você tem permissão para ler. Por exemplo, você tem acesso de leitura para a assinatura A e vai para a folha de recursos do Azure a fim de verificar as atribuições do usuário. É possível ver as atribuições de acesso dele para a assinatura A, mas não é possível ver que ele também tem acesso a atribuições na assinatura B.

## <a name="delete-access-assignments"></a>Excluir atribuições de acesso
Nessa folha, você pode excluir atribuições de acesso que foram atribuídas diretamente a um usuário ou grupo. Se a atribuição de acesso foi herdada de um grupo pai, você precisará ir até o recurso ou a assinatura e gerenciar a atribuição lá.

1. Na lista de todas as atribuições de acesso para um usuário ou grupo, selecione aquela que deseja excluir.
2. Selecione **Remover** e **Sim** para confirmar.
    ![Remover atribuição de acesso – captura de tela](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="next-steps"></a>Próximas etapas

* Introdução ao Controle de Acesso Baseado em Função a fim de [Usar atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](role-based-access-control-configure.md)
* Confira as [Funções internas do RBAC do Azure](role-based-access-built-in-roles.md)

