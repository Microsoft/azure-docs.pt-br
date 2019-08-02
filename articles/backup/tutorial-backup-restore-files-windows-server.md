---
title: Recuperar arquivos do Azure para um Windows Server
description: Este tutorial fornece detalhes sobre como recuperar itens do Azure para um Windows Server.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 575ebfb62b3eed70f783bc68b7f06559e5632490
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273976"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Recuperar arquivos do Azure para um Windows Server

O Backup do Azure habilita a recuperação de itens individuais dos backups do seu Windows Server. A recuperação de arquivos individuais será útil se você precisar restaurar rapidamente os arquivos que forem excluídos acidentalmente. Este tutorial aborda como você pode usar o Agente MARS (Serviços de Recuperação do Microsoft Azure) para recuperar itens dos backups que você já realizou no Azure. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Iniciar recuperação de itens individuais 
> * Selecionar um ponto de recuperação 
> * Restaurar itens de um ponto de recuperação

Este tutorial presume que você já executou as etapas para [Fazer backup de um Windows Server para o Azure](backup-configure-vault.md) e tenha, pelo menos, um backup dos seus arquivos do Windows Server no Azure.

## <a name="initiate-recovery-of-individual-items"></a>Iniciar recuperação de itens individuais

Um assistente útil de interface do usuário chamado Backup do Microsoft Azure é instalado com o Agente MARS (Serviços de Recuperação do Microsoft Azure). O assistente de Backup do Microsoft Azure funciona com o Agente MARS (Serviços de Recuperação do Microsoft Azure) para recuperar dados de backup dos pontos de recuperação armazenados no Azure. Use o assistente de Backup do Microsoft Azure para identificar os arquivos ou pastas que você deseja restaurar para o Windows Server. 

1. Abra o snap-in do **Backup do Microsoft Azure** . Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.

    ![Backup pendente](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. No assistente, clique em **Recuperar Dados** no **Painel de Ações** do console do agente para iniciar o assistente **Recuperar Dados**.

    ![Backup pendente](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Na página **Introdução**, selecione **Este servidor (nome do servidor)** e clique em **Avançar**.

4. Na página **Selecionar Modo de Recuperação**, selecione **Arquivos e pastas individuais** e, em seguida, clique em **Avançar** para iniciar o processo de seleção de ponto de recuperação.
 
5. No painel **Selecionar Volume e Data**, selecione o volume que contém os arquivos ou pastas que você deseja restaurar e clique em **Montar**. Selecione uma data e uma hora no menu suspenso que correspondam a um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de, pelo menos, um ponto de recuperação naquele dia.

    ![Backup pendente](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    Quando você clica em **Montar**, o Backup do Azure disponibiliza o ponto de recuperação como um disco. Procurar e recuperar os arquivos do disco.

## <a name="restore-items-from-a-recovery-point"></a>Restaurar itens de um ponto de recuperação

1. Depois que o volume de recuperação estiver montado, clique em **Procurar** para abrir o Windows Explorer e localizar os arquivos e pastas que deseja recuperar. 

    ![Backup pendente](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Você pode abrir os arquivos diretamente do volume de recuperação e verificá-los.

2. No Windows Explorer, copie os arquivos e/ou pastas que deseja restaurar e cole-os em qualquer local desejado no servidor.

    ![Backup pendente](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Quando você terminar de restaurar os arquivos e/ou pastas, na página **Procurar e Recuperar Arquivos** do assistente **Recuperar Dados**, clique em **Desmontar**. 

    ![Backup pendente](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Clique em **Sim** para confirmar que deseja desmontar o volume.

    Depois que o instantâneo estiver desmontado, a mensagem **Trabalho Concluído** aparecerá no painel **Trabalhos** no console do agente.

## <a name="next-steps"></a>Próximas etapas

Isso conclui os tutoriais de backup e restauração dos dados do Windows Server para o Azure. Para saber mais sobre o Backup do Azure, consulte o exemplo do PowerShell para fazer backup de máquinas virtuais criptografadas.

> [!div class="nextstepaction"]
> [Fazer backup da VM criptografada](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
