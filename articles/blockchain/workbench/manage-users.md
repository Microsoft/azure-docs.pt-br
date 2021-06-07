---
title: Gerenciar usuários no Azure Blockchain Workbench
description: Como gerenciar usuários no Azure Blockchain Workbench.
ms.date: 07/15/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: be078e7149ba008ab8b7ad44a016a0d1cf82df0e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91263004"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Gerenciar usuários no Azure Blockchain Workbench

O Azure Workbench de Blockchain inclui gerenciamento de usuário para as pessoas e organizações que fazem parte de seu consórcio.

## <a name="prerequisites"></a>Pré-requisitos

Uma implantação do Blockchain Workbench é necessária. Consulte [implantação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implantação.

## <a name="add-azure-ad-users"></a>Adicionar usuários do Microsoft Azure Active Directory

O Workbench de Blockchain do Azure usa o Microsoft Azure Active Directory (AD do Azure) para autenticação, controle de acesso e funções. Os usuários no locatário Blockchain Workbench do Microsoft Azure Active Directory podem autenticar e usar Blockchain Workbench. Adicione usuários à função de aplicativo de administrador para interagir e executar ações.

Usuários Blockchain Workbench precisam existir no locatário do Microsoft Azure Active Directory e antes de atribuí-los para aplicativos e funções. Para adicionar usuários ao Microsoft Azure Active Directory, use as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione sua conta no canto superior direito e alterne para o locatário do Microsoft Azure Active Directory associado ao Blockchain Workbench.
1. Selecione **Microsoft Azure Active Directory > Usuários**. Você verá uma lista de usuários em seu diretório.
1. Para adicionar usuários ao diretório, selecione **Novo usuário**. Para usuários externos, selecione **Novo usuário convidado**.
1. Preencha os campos obrigatórios para o novo usuário. Selecione **Criar**.

Visite a documentação [Microsoft Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) para obter mais detalhes sobre como gerenciar os usuários no Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Gerenciar administradores Blockchain Workbench

Depois que os usuários foram adicionados ao diretório, a próxima etapa é escolher quais usuários são administradores de Blockchain Workbench. Os usuários no grupo **Administrador** estão associados à **função de aplicativo do Administrador** no Blockchain Workbench. Os administradores podem adicionar ou remover usuários, atribuir usuários a cenários específicos e criar novos aplicativos.

Para adicionar usuários ao grupo **Administrador** no diretório do Microsoft Azure Active Directory:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está no locatário do Azure AD associado ao Blockchain Workbench selecionando sua conta no canto superior direito.
1. Selecione **Azure Active Directory > aplicativos empresariais**.
1. Altere o filtro suspenso de **tipo de aplicativo** para **todos os aplicativos** e selecione **aplicar**.
1. Selecione o aplicativo cliente do Azure AD para o Azure Blockchain Workbench

    ![Todos os registros do aplicativo](./media/manage-users/select-blockchain-client-app.png)

1. Selecionar **Usuários e grupos > Adicionar usuário**.
1. Em **Adicionar atribuição**, selecione **Usuários**. Escolha ou pesquise o usuário que você deseja adicionar como administrador. Clique em **Selecionar** quando terminar de escolher.

    ![Adicionar atribuição](./media/manage-users/add-user-assignment.png)

1. Verifique se a **Função** é definida como **Administrador**
1. Selecione **Atribuir**. Os usuários adicionados são exibidos na lista com a função de administrador atribuída.

    ![Usuários do aplicativo de cliente Blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Gerenciando membros que usam o Blockchain Workbench

Use o aplicativo Blockchain Workbench para gerenciar usuários e organizações que fazem parte de seu consórcio. Você pode adicionar ou remover usuários para aplicativos e funções.

1. [Abra o Blockchain Workbench](deploy.md#blockchain-workbench-web-url) no seu navegador e entre como administrador.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Os membros são adicionados a cada aplicativo. Os membros podem ter uma ou mais funções de aplicativo de contratos para iniciar ou executar ações.

1. Para gerenciar membros de um aplicativo, selecione um bloco de aplicativo no painel **Aplicativos**.

    O número de membros associados ao aplicativo selecionado é refletido no bloco de membros.

    ![Selecionar aplicativo](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Adicionar membro ao aplicativo

1. Selecione o bloco de membro para exibir uma lista de membros atuais.
1. Selecione **Adicionar membros**.

    ![Captura de tela mostra a janela associação do aplicativo com o botão Adicionar um membro realçado.](./media/manage-users/application-add-members.png)

1. Procure o nome do usuário.  Somente usuários do Microsoft Azure AD que existem no locatário Blockchain Workbench estão listados. Se o usuário não for encontrado, você precisará [Adicionar usuários do Azure ad](#add-azure-ad-users).

    ![Adicionar membros](./media/manage-users/find-user.png)

1. Selecione um **VHD** no menu suspenso.

    ![Selecione os membros da função](./media/manage-users/application-select-role.png)

1. Selecione **Adicionar** para adicionar o membro com a função associada ao aplicativo.

#### <a name="remove-member-from-application"></a>Remover membro do aplicativo

1. Selecione o bloco de membro para exibir uma lista de membros atuais.
1. Para o usuário que deseja remover, escolha **Remover** da função de lista suspensa.

    ![Remover membro](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Alterar ou adicionar função

1. Selecione o bloco de membro para exibir uma lista de membros atuais.
1. Para o usuário que você deseja alterar, clique na lista suspensa e selecione a nova função.

    ![Alterar a função](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo de procedimentos, você aprendeu como gerenciar os usuários do Azure Blockchain Workbench. Para saber como usar o aplicativo blockchain, continue no próximo artigo de instruções.

> [!div class="nextstepaction"]
> [Criar um aplicativo blockchain no Azure Blockchain Workbench](create-app.md)
