---
title: Definir senhas para VMs no Azure Lab Services | Microsoft Docs
description: Saiba como definir e redefinir senhas para VMs (máquinas virtuais) em laboratórios de Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6ae577ee4c0c7e31760e0fb12afeaeac1ef8b7e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434219"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurar e gerenciar o pool de máquinas virtuais 
Este artigo mostra como fazer as seguinte tarefas:

- Aumentar o número de VMs (máquinas virtuais) no laboratório
- Iniciar todas as VMs ou as VMs selecionadas 
- Redefinir VMs

## <a name="update-the-lab-capacity"></a>Atualizar a capacidade do laboratório
Para aumentar ou diminuir a capacidade do laboratório (número de máquinas virtuais em um laboratório), execute as seguintes etapas:

1. Na página **Pool de máquinas virtuais**, selecione **Capacidade do laboratório: &lt;número&gt; computadores**.
2. Insira o novo **número de VMs** desejado no laboratório. Esse número deve ser maior ou igual ao número de usuários registrados no laboratório. 
3. Em seguida, selecione **Salvar**. 

    ![Captura de tela que mostra a janela "capacidade do laboratório" com o botão "máximo de computadores no laboratório" e "salvar" selecionado.](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Se você tiver aumentado a capacidade, poderá ver a VM ou as VMs que estão sendo criadas. Se você não vir a nova VM na lista, atualize a página. 

    ![VM sendo criada](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Iniciar VMs

### <a name="start-ot-stop-all-vms"></a>Iniciar ou parar todas as VMs
1. Alterne para a página **Pool de máquinas virtuais**. 
2. Selecione **Iniciar tudo** na barra de ferramentas. 

    ![Botão Iniciar tudo](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Depois que todas as VMs forem iniciadas, você poderá parar todas elas selecionando o botão **Parar tudo** na barra de ferramentas. 

    ![Botão Parar tudo](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Iniciar VMs selecionadas
Há duas maneiras de iniciar as VMs selecionadas (uma ou mais). A primeira delas é selecionar a VM ou as VMs na lista e, em seguida, selecionar **Iniciar** na barra de ferramentas. 

A segunda maneira é selecionar uma ou mais VMs na lista e alternar o botão na coluna **Estado**. 

![Iniciar VMs selecionadas](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Da mesma forma, você pode parar uma ou mais VMs alternando o botão na coluna **Estado** ou selecionando **Parar** na barra de ferramentas. 

> [!NOTE]
> Quando um educador ativa a VM de um aluno, a cota do aluno não é afetada. A cota de um usuário especifica o número de horas de laboratório disponíveis para o usuário fora da hora da classe agendada. Para obter mais informações sobre cotas, confira [Definir cotas para usuários](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Redefinir VMs

Para redefinir uma ou mais VMs, selecione-as na lista e, em seguida, selecione **Redefinir** na barra de ferramentas. 

![Redefinir VMs selecionadas](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Na caixa de diálogo **Redefinir máquinas virtuais**, selecione **Redefinir**. 

![Caixa de diálogo Redefinir VM](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)

## <a name="set-password-for-vms"></a>Definir senha para VMs
Um proprietário do laboratório (educador) pode definir/redefinir a senha para VMs no momento da criação do laboratório (assistente de criação do laboratório) ou após criá-lo na página **Modelo**. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Definir a senha no momento da criação do laboratório
Um proprietário do laboratório (educador) pode definir uma senha para VMs no laboratório na página **Credenciais de máquina virtual** do assistente de criação de laboratório.

![Janela Novo laboratório](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Ao habilitar/desabilitar a opção **Usar a mesma senha para todas as máquinas virtuais** nesta página, um educador pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para as VMs deles. Por padrão, essa configuração é habilitada para todas as imagens dos sistemas operacionais Windows e Linux, exceto o Ubuntu. Quando essa configuração estiver desabilitada, os alunos deverão definir uma senha quando tentarem se conectar à VM pela primeira vez. 

### <a name="reset-password-later"></a>Redefinir senha posteriormente

1. Na página **Modelo** do laboratório, selecione **Redefinir senha** na barra de ferramentas. 
1. Na caixa de diálogo **Redefinir senha**, insira uma senha e selecione **Redefinir senha**.
    
    ![Caixa de diálogo Definir senha](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Conectar-se às VMs dos alunos
O criador do laboratório (educador) poderá se conectar a uma VM de aluno se as seguintes condições forem atendidas: 

- A opção **Usar a mesma senha para todas as máquinas virtuais** foi selecionada ao criar o laboratório
- A VM está em execução 

 Para se conectar à VM do aluno, passe o mouse sobre a VM na lista e selecione o botão de computador.  

![Botão Conectar-se à VM do aluno](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Quando o educador inicia a VM e se conecta a ela, a cota do aluno não é afetada. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Exportar a lista de máquinas virtuais para um arquivo CSV

1. Alterne para a guia **Pool de máquinas virtuais**.
2. Selecione **...** (reticências) na barra de ferramentas e **Exportar CSV**. 

    ![Exportar lista de máquinas virtuais](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as outras opções de uso do aluno que você (como proprietário do laboratório) pode configurar, confira o seguinte artigo: [Configurar o uso do aluno](how-to-configure-student-usage.md).

Para saber mais sobre como os alunos podem redefinir senhas para suas VMs, consulte [definir ou Redefinir senha para máquinas virtuais em laboratórios (alunos)](how-to-set-virtual-machine-passwords-student.md).