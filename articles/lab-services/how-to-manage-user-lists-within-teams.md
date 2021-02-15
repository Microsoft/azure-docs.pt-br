---
title: Gerenciar Azure Lab Services listas de usuários de equipes
description: Saiba como gerenciar Azure Lab Services listas de usuários de equipes.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946585"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Gerenciar listas de usuários do Lab Services de equipes

Quando um laboratório é criado dentro das equipes (Confira [introdução e criação de um laboratório de serviços de laboratório das equipes](how-to-get-started-create-lab-within-teams.md)), a lista de usuários do laboratório é automaticamente populada e sincronizada com a associação da equipe. Todos na equipe, incluindo proprietários, membros e convidados, serão adicionados automaticamente à lista de usuários do laboratório. Os serviços de laboratório do Azure mantêm uma sincronização com a associação da equipe e uma sincronização automática é disparada a cada 24 horas. 

## <a name="sync-users"></a>Sincronizar usuários

Os educadores podem usar o botão **sincronizar** para disparar uma sincronização manual quando a associação da equipe é atualizada. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Sincronizar usuários":::

Depois que a sincronização automática ou manual for concluída, o seguinte será verdadeiro, dependendo se o laboratório foi publicado.

* Se o laboratório não tiver sido publicado pelo menos uma vez:
    * Os usuários serão adicionados ou excluídos da lista de usuários do laboratório de acordo com as alterações feitas na associação da equipe. 
* Se o laboratório tiver sido publicado pelo menos uma vez, além de adicionar ou excluir usuários, a capacidade do laboratório será atualizada automaticamente.
    * Se houver qualquer adição nova à equipe, novas VMs serão criadas.
    * Se qualquer usuário tiver sido excluído da equipe, a VM associada também será excluída.

## <a name="next-steps"></a>Próximas etapas

Depois que a VM de modelo estiver configurada e quando o professor selecionar publicar o modelo, o número de VMs equivalentes ao número de usuários na lista de usuários do laboratório será criado. Depois que o laboratório for publicado e as VMs forem criadas, os usuários serão automaticamente registrados no laboratório e as VMs serão atribuídas a eles em seu primeiro logon para Azure Lab Services ou seja, quando eles acessam pela primeira vez a guia que tem **Azure Lab Services** aplicativo. 

Para publicar a VM do modelo, vá para a janela do teams Lab Services, selecione guia de **modelo** -> **...**  ->  **Publicar**.

Para gerenciar pools de VMs, consulte [gerenciar um pool de VMs em Lab Services from Teams](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>Examine também

Confira os seguintes artigos:

- [Visão geral do uso de Azure Lab Services nas equipes](lab-services-within-teams-overview.md)
- [Comece e crie um laboratório de serviços de laboratório das equipes](how-to-get-started-create-lab-within-teams.md)
- [Criar agendas de serviços de laboratório de equipes](how-to-create-schedules-within-teams.md)
- [Acessar uma VM (exibição de aluno) no Lab Services das equipes](how-to-access-vm-for-students-within-teams.md)

