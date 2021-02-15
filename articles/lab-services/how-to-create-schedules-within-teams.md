---
title: Criar Azure Lab Services agendas dentro das equipes
description: Saiba como criar agendas de serviços de laboratório dentro das equipes.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042330"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Criar e gerenciar agendas de serviços de laboratório dentro de equipes

Agendamentos permitem que você configure um laboratório de sala de aula, de modo que as VMs no laboratório iniciam e desligam automaticamente em um horário especificado. Você pode definir um agendamento único ou recorrente. Os procedimentos a seguir fornecem as etapas para criar e gerenciar agendamentos para um laboratório de sala de aula: 

Veja como as agendas afetam as máquinas virtuais do laboratório: 

- A máquina virtual de modelo não está incluída em agendas. 
- Somente as máquinas virtuais atribuídas são iniciadas. Isso significa que, se um computador não for reivindicado por um usuário final (aluno), o computador não será iniciado nas horas agendadas. 
- Todas as máquinas virtuais (se declaradas por um usuário ou não) são interrompidas com base na agenda do laboratório. 

> [!IMPORTANT]
> O tempo de execução agendado de VMs não conta com a cota alocada a um usuário. A cota é para o tempo fora dos horários agendados que um aluno passa em VMs. 

Os usuários podem criar, editar e excluir agendas de laboratório em equipes, assim como no [site de laboratórios](https://labs.azure.com). Consulte o artigo sobre como [criar e gerenciar agendas](how-to-create-schedules-within-teams.md).

## <a name="automatic-shutdown-and-disconnect-settings"></a>Configurações de desligamento e desconexão automáticas

Você pode habilitar vários recursos de controle de custos de desligamento automático para prevenir de forma proativa custos adicionais quando as máquinas virtuais não estiverem sendo usadas ativamente. A combinação dos seguintes três recursos automáticos de desligamento e desconexão captura a maioria dos casos em que os usuários deixam acidentalmente suas máquinas virtuais em execução:
 
- Desconectar automaticamente os usuários de máquinas virtuais que o sistema operacional considera ocioso.
- Desligar automaticamente as máquinas virtuais quando os usuários se desconectarem.
- Desligar automaticamente as máquinas virtuais que são iniciadas, mas os usuários não se conectam.

Para obter mais detalhes, consulte o artigo sobre como [definir as configurações de desligamento automático para um laboratório](how-to-enable-shutdown-disconnect.md).

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Visão geral do uso de Azure Lab Services nas equipes](lab-services-within-teams-overview.md)
- [Comece e crie um laboratório dentro das equipes](how-to-get-started-create-lab-within-teams.md)
- [Gerenciar listas de usuários do laboratório dentro das equipes](how-to-manage-user-lists-within-teams.md)
- [Gerenciar o pool de VMs do laboratório dentro das equipes](how-to-manage-vm-pool-within-teams.md)
- [Acessar uma VM nas equipes – exibição de aluno](how-to-access-vm-for-students-within-teams.md)
