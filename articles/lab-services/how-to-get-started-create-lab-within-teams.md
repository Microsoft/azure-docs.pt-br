---
title: Comece e crie um laboratório de Azure Lab Services dentro das equipes
description: Saiba como começar e criar um laboratório de Azure Lab Services dentro das equipes.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433964"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Comece e crie um laboratório de serviços de laboratório dentro das equipes

Este artigo mostra como adicionar o aplicativo **Azure Lab Services** a uma equipe e como criar um laboratório dentro do ambiente do MS Teams.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, você configura um laboratório com máquinas virtuais para sua equipe. Para configurar um laboratório em uma conta de laboratório, você deve ser um membro de uma dessas funções na conta de laboratório: proprietário, criador de laboratório ou colaborador. A conta que você usou para criar uma conta de laboratório é adicionada automaticamente à função de proprietário. Portanto, você pode usar a conta de usuário que usou para criar uma conta de laboratório para criar um laboratório.

Este é o fluxo de trabalho típico ao usar Azure Lab Services nas equipes

1. O usuário [cria uma conta de laboratório](tutorial-setup-lab-account.md#create-a-lab-account) no portal do Azure.
1. Um [criador de conta de laboratório adiciona outros usuários](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) à função de **criador de laboratório** . Por exemplo, o criador/administrador da conta de laboratório adiciona educadores à função **Criador de Laboratório** para que eles possam criar laboratórios para suas aulas.
1. Em seguida, os educadores criam laboratórios, predefinem a VM de modelo e publica o laboratório para provisionar VMs para todos na equipe.
1. Depois que o laboratório for publicado, uma VM será atribuída a todos na lista de membros da equipe em seu primeiro logon para Azure Lab Services, clicando na guia que contém **Azure Lab Services** aplicativo dentro de equipes (SSO) ou acessando o [site do Labs](https://labs.azure.com). Os usuários podem usar a VM para fazer a classe funcionar e de casa.

> [!IMPORTANT]
> Azure Lab Services só poderá ser usada em equipes se as contas de laboratório forem criadas no mesmo locatário que as equipes.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Adicionar Azure Lab Services aplicativo como uma guia a uma equipe

Você, como proprietário da equipe, pode adicionar **Azure Lab Services** aplicativo diretamente em seus canais de equipes e, em seguida, o aplicativo está disponível para todos na equipe usarem. Siga as três etapas abaixo:

1. Navegue até o canal de equipes em que você deseja adicionar o aplicativo e selecione **+** para adicionar uma guia. 
1. Procure **Azure Lab Services** nas opções da guia e adicione este aplicativo. 

    > [!NOTE]
    > Somente os **proprietários** da equipe poderão criar laboratórios para a equipe.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="Adicionar uma guia":::
1. Selecione uma conta do Lab Services que você deseja usar para criar laboratórios nesta equipe. 

    O Azure Lab Services usa o logon único no [site do Azure Lab Services](https://labs.azure.com) e efetua pull de todas as contas de laboratório às quais você tem acesso. 

    As contas que estão no mesmo locatário das equipes e para as quais você tem acesso de **proprietário**, **colaborador** ou **criador** são exibidas. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="Bem-vindo ao ALS":::
1. Pressione **salvar** e a guia será adicionada ao canal.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="Criou a guia ALS":::

    Agora você pode selecionar a guia **Azure Lab Services** do seu canal e começar a gerenciar laboratórios, conforme descrito nos artigos a seguir.

Depois que a conta do laboratório for selecionada, os proprietários da equipe poderão criar laboratórios para a equipe. Todo o processo de criação de laboratório e todas as tarefas no nível de laboratório podem ser executados dentro das equipes. Os usuários terão a opção de criar vários laboratórios dentro da mesma equipe e o proprietário da equipe, com acesso apropriado no nível da conta do laboratório, verão apenas os laboratórios associados à equipe específica.

## <a name="next-steps"></a>Próximas etapas

Quando um laboratório é criado dentro das equipes, a lista de usuários do laboratório é automaticamente populada e sincronizada com a associação da equipe. Todos na equipe, incluindo proprietários, membros e convidados, serão adicionados automaticamente à lista de usuários do laboratório. Os serviços de laboratório do Azure manterão uma sincronização com a associação da equipe e uma sincronização automática será disparada a cada 24 horas. Para obter detalhes, confira:

[Gerenciar listas de usuários do Lab Services dentro das equipes](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Veja também

Consulte também os seguintes artigos:

- [Visão geral do uso de Azure Lab Services nas equipes](lab-services-within-teams-overview.md)
- [Gerenciar o pool de VMs do laboratório dentro das equipes](how-to-manage-vm-pool-within-teams.md)
- [Criar e gerenciar agendas de laboratório dentro das equipes](how-to-create-schedules-within-teams.md)
- [Acessar uma VM nas equipes – exibição de aluno](how-to-access-vm-for-students-within-teams.md)
- [Excluir laboratórios dentro de equipes](how-to-delete-lab-within-teams.md)
