---
title: Adicionar ou remover atribuições de função do Azure para usuários externos usando o portal do Azure-RBAC do Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários externos a uma organização usando o portal do Azure e o controle de acesso baseado em função do Azure (RBAC do Azure).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: a18fc3e4851c2daf03c662cf40cef58cc7d9e77a
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117700"
---
# <a name="add-or-remove-azure-role-assignments-for-external-guest-users-using-the-azure-portal"></a>Adicionar ou remover atribuições de função do Azure para usuários convidados externos usando o portal do Azure

O Azure [RBAC (controle de acesso baseado em função)](overview.md) do Azure permite um melhor gerenciamento de segurança para grandes organizações e para empresas de pequeno e médio porte que trabalham com colaboradores externos, fornecedores ou freelanceres que precisam de acesso a recursos específicos em seu ambiente, mas não necessariamente à infraestrutura inteira ou a quaisquer escopos relacionados à cobrança. Você pode usar os recursos do [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) para colaborar com usuários convidados externos e pode usar o RBAC do Azure para conceder apenas as permissões que os usuários convidados precisam em seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="when-would-you-invite-guest-users"></a>Quando você convidar usuários convidados?

Aqui estão alguns cenários de exemplo em que você pode convidar usuários convidados para sua organização e conceder permissões:

- Permitir que um fornecedor externo autônomo tenha apenas uma conta de email para acessar os recursos do Azure para um projeto.
- Permitir que um parceiro externo gerencie determinados recursos ou uma assinatura inteira.
- Permitir que os engenheiros de suporte que não são da sua organização (como o suporte da Microsoft) acessem temporariamente o recurso do Azure para solucionar problemas.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Diferenças de permissão entre usuários Membros e usuários convidados

Os membros nativos de um diretório (usuários Membros) têm permissões diferentes dos usuários convidados de outro diretório como um convidado de colaboração B2B (usuários convidados). Por exemplo, os membros usuário podem ler quase todas as informações de diretório enquanto os usuários convidados têm permissões de diretório restritas. Para obter mais informações sobre usuários Membros e usuários convidados, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Adicionar um usuário convidado ao diretório

Siga estas etapas para adicionar um usuário convidado ao seu diretório usando a página Azure Active Directory.

1. Verifique se as definições de colaboração externa de sua organização estão configuradas de forma que você tenha permissão para convidar participantes. Para obter mais informações, consulte [habilitar colaboração externa B2B e gerenciar quem pode convidar convidados](../active-directory/external-identities/delegate-invitations.md).

1. Na portal do Azure, clique em **Azure Active Directory**  >  **usuários**  >  **novo usuário convidado**.

    ![Novo recurso de usuário convidado no portal do Azure](./media/role-assignments-external-users/invite-guest-user.png)

1. Siga as etapas para adicionar um novo usuário convidado. Para obter mais informações, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory).

Depois de adicionar um usuário convidado ao diretório, você poderá enviar ao usuário convidado um link direto para um aplicativo compartilhado, ou o usuário convidado poderá clicar na URL de resgate no email de convite.

![Email de convite do usuário convidado](./media/role-assignments-external-users/invite-email.png)

Para que o usuário convidado possa acessar seu diretório, ele deve concluir o processo de convite.

![Permissões de revisão de convite de usuário convidado](./media/role-assignments-external-users/invite-review-permissions.png)

Para obter mais informações sobre o processo de convite, consulte [Azure Active Directory resgate de convite de colaboração B2B](../active-directory/external-identities/redemption-experience.md).

## <a name="add-a-role-assignment-for-a-guest-user"></a>Adicionar uma atribuição de função para um usuário convidado

No RBAC do Azure, para conceder acesso, você atribui uma função. Para adicionar uma atribuição de função para um usuário convidado, siga [as mesmas etapas necessárias](role-assignments-portal.md#add-a-role-assignment) para um usuário membro, grupo, entidade de serviço ou identidade gerenciada. Siga estas etapas para adicionar uma atribuição de função para um usuário convidado em escopos diferentes.

1. No portal do Azure, clique em **Todos os serviços**.

1.  Selecione o conjunto de recursos ao qual o acesso se aplica, também conhecido como o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique em **IAM (Controle de Acesso)**.

    A captura de tela a seguir mostra um exemplo da folha Controle de acesso (IAM) para um grupo de recursos. Se você fizer alterações de controle de acesso aqui, elas se aplicarão apenas ao grupo de recursos.

    ![Lâmina de controle de acesso (IAM) para um grupo de recursos](./media/role-assignments-external-users/access-control-resource-group.png)

1. Clique na guia **Atribuições de função** para visualizar todas as atribuições de função nesse escopo.

1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

    Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

    ![Adicionar menu de atribuição de função](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **selecionar** , selecione o usuário convidado. Se você não vir o usuário na lista, poderá digitar na caixa **selecionar** para pesquisar o diretório em busca de nomes de exibição, endereços de email e identificadores de objeto.

   ![Adicionar painel de atribuição de função](./media/role-assignments-external-users/add-role-assignment.png)

1. Clique em **salvar** para atribuir a função no escopo selecionado.

    ![Atribuição de função para colaborador de máquina virtual](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Adicionar uma atribuição de função para um usuário convidado que ainda não está em seu diretório

Para adicionar uma atribuição de função para um usuário convidado, siga [as mesmas etapas necessárias](role-assignments-portal.md#add-a-role-assignment) para um usuário membro, grupo, entidade de serviço ou identidade gerenciada.

Se o usuário convidado ainda não estiver em seu diretório, você poderá convidar o usuário diretamente do painel Adicionar atribuição de função.

1. No portal do Azure, clique em **Todos os serviços**.

1.  Selecione o conjunto de recursos ao qual o acesso se aplica, também conhecido como o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique em **IAM (Controle de Acesso)**.

1. Clique na guia **Atribuições de função** para visualizar todas as atribuições de função nesse escopo.

1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

    ![Adicionar menu de atribuição de função](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **selecionar** , digite o endereço de email da pessoa que você deseja convidar e selecione essa pessoa.

   ![Convidar usuário convidado no painel Adicionar atribuição de função](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Clique em **salvar** para adicionar o usuário convidado ao seu diretório, atribuir a função e enviar um convite.

    Após alguns instantes, você verá uma notificação da atribuição de função e informações sobre o convite.

    ![Atribuição de função e notificação de usuário convidado](./media/role-assignments-external-users/invited-user-notification.png)

1. Para convidar manualmente o usuário convidado, clique com o botão direito do mouse e copie o link do convite na notificação. Não clique no link de convite porque ele inicia o processo de convite.

    O link do convite terá o seguinte formato:

    `https://invitations.microsoft.com/redeem/...`

1. Envie o link do convite para o usuário convidado para concluir o processo de convite.

    Para obter mais informações sobre o processo de convite, consulte [Azure Active Directory resgate de convite de colaboração B2B](../active-directory/external-identities/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Remover um usuário convidado do seu diretório

Antes de remover um usuário convidado de um diretório, primeiro você deve remover as atribuições de função para esse usuário convidado. Siga estas etapas para remover um usuário convidado de um diretório.

1. **Controle de acesso aberto (iam)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, em que o usuário convidado tem uma atribuição de função.

1. Clique na guia **atribuições de função** para exibir todas as atribuições de função.

1. Na lista de atribuições de função, adicione uma marca de seleção ao lado do usuário convidado com a atribuição de função que você deseja remover.

   ![Remover atribuição de função](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Clique em **Remover**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-external-users/remove-role-assignment.png)

1. Na mensagem para remover a atribuição de função exibida, clique em **Sim**.

1. Na barra de navegação à esquerda, clique em **Azure Active Directory**  >  **usuários**.

1. Clique no usuário convidado que você deseja remover.

1. Clique em **Excluir**.

   ![Excluir usuário convidado](./media/role-assignments-external-users/delete-guest-user.png)

1. Na mensagem de exclusão exibida, clique em **Sim**.

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="guest-user-cannot-browse-the-directory"></a>O usuário convidado não pode navegar no diretório

Usuários convidados têm permissões de diretório restritas. Por exemplo, os usuários convidados não podem navegar no diretório e não podem pesquisar grupos ou aplicativos. Para obter mais informações, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![O usuário convidado não pode procurar usuários em um diretório](./media/role-assignments-external-users/directory-no-users.png)

Se um usuário convidado precisar de privilégios adicionais no diretório, você poderá atribuir uma função de diretório ao usuário convidado. Se você realmente deseja que um usuário convidado tenha acesso de leitura total ao seu diretório, você pode adicionar o usuário convidado à função [leitores de diretório](../active-directory/roles/permissions-reference.md) no Azure AD. Para obter mais informações, consulte [conceder permissões a usuários de organizações parceiras em seu locatário Azure Active Directory](../active-directory/external-identities/add-guest-to-role.md).

![Atribuir função de leitores de diretório](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>O usuário convidado não pode procurar usuários, grupos ou entidades de serviço para atribuir funções

Usuários convidados têm permissões de diretório restritas. Mesmo que um usuário convidado seja um [proprietário](built-in-roles.md#owner) em um escopo, se eles tentarem adicionar uma atribuição de função para conceder a outra pessoa acesso, eles não poderão procurar a lista de usuários, grupos ou entidades de serviço.

![O usuário convidado não pode procurar entidades de segurança para atribuir funções](./media/role-assignments-external-users/directory-no-browse.png)

Se o usuário convidado souber o nome de entrada exato de alguém no diretório, ele poderá conceder acesso. Se você realmente deseja que um usuário convidado tenha acesso de leitura total ao seu diretório, você pode adicionar o usuário convidado à função [leitores de diretório](../active-directory/roles/permissions-reference.md) no Azure AD. Para obter mais informações, consulte [conceder permissões a usuários de organizações parceiras em seu locatário Azure Active Directory](../active-directory/external-identities/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>O usuário convidado não pode registrar aplicativos ou criar entidades de serviço

Usuários convidados têm permissões de diretório restritas. Se um usuário convidado precisar registrar aplicativos ou criar entidades de serviço, você poderá adicionar o usuário convidado à função de [desenvolvedor do aplicativo](../active-directory/roles/permissions-reference.md) no Azure AD. Para obter mais informações, consulte [conceder permissões a usuários de organizações parceiras em seu locatário Azure Active Directory](../active-directory/external-identities/add-guest-to-role.md).

![O usuário convidado não pode registrar aplicativos](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>O usuário convidado não vê o novo diretório

Se um usuário convidado tiver recebido acesso a um diretório, mas não vir o novo diretório listado na portal do Azure quando tentarem alternar para o seu **diretório +** painel de assinatura, verifique se o usuário convidado concluiu o processo de convite. Para obter mais informações sobre o processo de convite, consulte [Azure Active Directory resgate de convite de colaboração B2B](../active-directory/external-identities/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>O usuário convidado não vê os recursos

Se um usuário convidado tiver recebido acesso a um diretório, mas não vir os recursos aos quais eles receberam acesso no portal do Azure, verifique se o usuário convidado selecionou o diretório correto. Um usuário convidado pode ter acesso a vários diretórios. Para alternar os diretórios, no canto superior esquerdo, clique em **diretório + assinatura** e, em seguida, clique no diretório apropriado.

![Diretórios + painel de assinaturas no portal do Azure](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure](../active-directory/external-identities/add-users-administrator.md)
- [Propriedades de um usuário de colaboração B2B do Azure Active Directory](../active-directory/external-identities/user-properties.md)
- [Os elementos do email de convite para colaboração B2B – Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [Adicionar um usuário convidado como um coadministrador](classic-administrators.md#add-a-guest-user-as-a-co-administrator)