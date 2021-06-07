---
title: Gerenciar um pool de VMs em Azure Lab Services das equipes
description: Saiba como gerenciar um pool de VMs em Azure Lab Services de equipes.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91946594"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Gerenciar um pool de VMs no Lab Services das equipes

A criação da VM (máquina virtual) é iniciada assim que a VM do modelo é publicada pela primeira vez. As VMs que forem iguais ao número de usuários na lista de usuários do laboratório serão criadas. As VMs são automaticamente atribuídas aos alunos no primeiro logon para Azure Lab Services. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Publicar um modelo e gerenciar um pool de VMs

Para publicar o modelo, vá para a janela do teams Lab Services, selecione guia de **modelo** -> **...**  ->  **Publicar**.

Depois que a VM de modelo estiver configurada e quando o professor selecionar publicar o modelo, o número de VMs equivalentes ao número de usuários na lista de usuários do laboratório será criado. Depois que o laboratório for publicado e as VMs forem criadas, os usuários serão automaticamente registrados no laboratório e as VMs serão atribuídas a eles em seu primeiro logon para Azure Lab Services ou seja, quando eles acessam pela primeira vez a guia que tem **Azure Lab Services** aplicativo. 

Quando uma sincronização da lista de usuários é disparada, a capacidade do laboratório (número de VMs no laboratório) será atualizada automaticamente com base nas alterações feitas na associação da equipe. Novas VMs serão criadas à medida que novos usuários forem adicionados e as VMs atribuídas aos usuários removidos da equipe também serão excluídas. Para obter mais informações, consulte [como gerenciar usuários em equipes](how-to-manage-user-lists-within-teams.md). 

Os educadores podem continuar acessando VMs de estudante diretamente na guia pool de VMs. E educadores podem acessar VMs atribuídas a si mesmos na guia **pool de máquinas virtuais** ou clicando no botão **minhas máquinas virtuais** (canto superior/direito da tela). 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="Pool de VMs":::

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Visão geral do uso de Azure Lab Services nas equipes](lab-services-within-teams-overview.md)
- [Comece e crie um laboratório de serviços de laboratório das equipes](how-to-get-started-create-lab-within-teams.md)
- [Gerenciar listas de usuários do Lab Services de equipes](how-to-manage-user-lists-within-teams.md)
- [Criar agendas de serviços de laboratório de equipes](how-to-create-schedules-within-teams.md)
- [Acessar uma VM (exibição de aluno) no Lab Services das equipes](how-to-access-vm-for-students-within-teams.md)


