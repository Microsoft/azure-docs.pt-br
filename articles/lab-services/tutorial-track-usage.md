---
title: Acompanhar o uso de um laboratório no Azure Lab Services | Microsoft Docs
description: Neste tutorial, você, como um criador/proprietário de laboratório, acompanha o uso do laboratório.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 8a3ca9e011eb89b3db8b202bab11d14f10d74e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434610"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Tutorial: Acompanhar o uso de um laboratório no Azure Lab Services
Este tutorial mostra como um criador/proprietário de laboratório pode acompanhar o uso de um laboratório.

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Exibir usuários registrados em seu laboratório
> * Exibir o uso de VMs no laboratório
> * Gerenciar VMs de aluno 


## <a name="view-registered-users"></a>Exibir usuários registrados

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). 
2. Selecione **Iniciar sessão** e insira suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft.
3. Na página **Meus Laboratórios**, selecione o laboratório do qual você deseja acompanhar o uso. 
4. Selecione **Usuários** no menu à esquerda ou no bloco **Usuários**. Você verá os alunos que foram registrados no laboratório.  

    ![Usuários registrados](./media/tutorial-track-usage/registered-users.png)

    Para obter mais informações sobre como adicionar e gerenciar usuários para o laboratório, confira [Adicionar e gerenciar os usuários do laboratório](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Exibir o log de VMs

1. Selecione **Máquinas Virtuais** no menu à esquerda. 
2. Confirme se você vê o status das máquinas virtuais e o número de horas em que as VMs estão em execução. O tempo que um proprietário de laboratório gasta em uma VM de aluno não é contado em relação ao tempo de uso mostrado na última coluna. 

    ![Uso de VM](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gerenciar VMs de aluno 
Nessa página, você pode iniciar, interromper ou redefinir as VMs dos alunos usando os controles na coluna **Estado** ou na barra de ferramentas.

![Ações de VM](./media/tutorial-track-usage/vm-controls.png)

Para obter mais informações sobre como gerenciar um pool de máquinas virtuais para o laboratório, confira [Configurar e gerenciar um pool de máquinas virtuais](how-to-set-virtual-machine-passwords.md).

> [!NOTE]
> Quando um educador ativa a VM de um aluno, a cota do aluno não é afetada. A cota de um usuário especifica o número de horas de laboratório disponíveis para o usuário fora da hora da classe agendada. Para obter mais informações sobre cotas, confira [Definir cotas para usuários](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre laboratórios, confira os artigos nos [Guias de instruções](how-to-manage-lab-accounts.md).
