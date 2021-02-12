---
title: Azure Lab Services no Microsoft Teams
description: Fornece uma visão geral do uso de Azure Lab Services no Microsoft Teams.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433920"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Azure Lab Services no Microsoft Teams

Azure Lab Services podem ser aproveitadas no Microsoft Teams usando o aplicativo **Azure Lab Services** Teams. Qualquer proprietário de equipe com acesso de proprietário/colaborador/criador às contas de laboratório poderá criar laboratórios e provisionar máquinas virtuais para todos na equipe.

Este artigo descreve os benefícios de usar Azure Lab Services dentro de equipes e fornece links para outros artigos para obter instruções sobre como criar e gerenciar laboratórios dentro de equipes. 

> [!NOTE]
>**Azure Lab Services** O aplicativo de equipes pode ser adicionado somente a uma equipe, ele não pode ser adicionado a bate-papos individuais ou a chats de grupo.

## <a name="benefits"></a>Benefícios

Azure Lab Services integração com o Microsoft Teams ajudará educadores a configurar um ambiente de sala de aula e a fornecer ambientes de laboratório virtual dentro da equipe (classe): 

* Os educadores podem configurar os laboratórios para que os alunos possam acessar suas VMs de dentro das equipes, sem sair das equipes e ter de navegar até o [site Azure Lab Services](https://labs.azure.com).
* SSO (logon único) de equipes para Azure Lab Services.
* Os proprietários de equipe e de laboratório não precisam manter as escalas de classe em dois sistemas diferentes – a lista de usuários do laboratório é preenchida automaticamente a partir da associação da equipe e uma sincronização é executada a cada 24 horas. 
* Após a publicação inicial da VM do modelo, a capacidade do laboratório (ou seja, o número de VMs no laboratório) seria ajustada automaticamente com base na adição/exclusão de usuários da associação da equipe. 
* Os proprietários de equipe e de laboratório exibirão apenas os laboratórios relacionados à equipe e os alunos exibirão apenas as VMs, que são provisionadas para a equipe específica. 
* Os usuários serão registrados automaticamente no laboratório e as VMs serão automaticamente atribuídas após o primeiro logon depois que o laboratório for publicado. Os educadores não precisam enviar convites e os alunos não precisam se registrar no laboratório separadamente.  

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Comece e crie um laboratório dentro das equipes](how-to-get-started-create-lab-within-teams.md)
- [Gerenciar listas de usuários do laboratório dentro das equipes](how-to-manage-user-lists-within-teams.md)
- [Gerenciar o pool de VMs do laboratório dentro das equipes](how-to-manage-vm-pool-within-teams.md)
- [Criar e gerenciar agendas de laboratório dentro das equipes](how-to-create-schedules-within-teams.md)
- [Acessar uma VM nas equipes – exibição de aluno](how-to-access-vm-for-students-within-teams.md)
