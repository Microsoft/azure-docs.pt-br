---
title: 'Tutorial: Recuperar itens para o Windows Server'
description: Neste tutorial, saiba como usar o Agente do MARS (Serviços de Recuperação do Microsoft Azure) para recuperar itens do Azure para um Windows Server.
ms.topic: tutorial
ms.date: 02/14/2018
ms.custom: mvc
ms.openlocfilehash: 746c901747cf1c0b87612a31fbabcb657d5c4a0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88263105"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Recuperar arquivos do Azure para um Windows Server

O Backup do Azure habilita a recuperação de itens individuais dos backups do seu Windows Server. A recuperação de arquivos individuais será útil se você precisar restaurar rapidamente os arquivos que forem excluídos acidentalmente. Este tutorial aborda como você pode usar o Agente MARS (Serviços de Recuperação do Microsoft Azure) para recuperar itens dos backups que você já realizou no Azure. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Iniciar recuperação de itens individuais
> * Selecionar um ponto de recuperação
> * Restaurar itens de um ponto de recuperação

Este tutorial presume que você já tenha executado as etapas necessárias para [Fazer backup de um Windows Server no Azure](backup-windows-with-mars-agent.md) e tenha, pelo menos, um backup dos seus arquivos do Windows Server no Azure.

## <a name="initiate-recovery-of-individual-items"></a>Iniciar recuperação de itens individuais

Um assistente útil de interface do usuário chamado Backup do Microsoft Azure é instalado com o Agente MARS (Serviços de Recuperação do Microsoft Azure). O assistente de Backup do Microsoft Azure funciona com o Agente MARS (Serviços de Recuperação do Microsoft Azure) para recuperar dados de backup dos pontos de recuperação armazenados no Azure. Use o assistente de Backup do Microsoft Azure para identificar os arquivos ou pastas que você deseja restaurar para o Windows Server.

1. Abra o snap-in do **Backup do Microsoft Azure** . Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.

    ![Snap-in Backup do Microsoft Azure](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. No assistente, selecione **Recuperar Dados** no **painel Ações** do console do agente para iniciar o assistente **Recuperar Dados**.

    ![Selecionar Recuperar Dados](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Na página **Introdução**, selecione **Este servidor (nome do servidor)** e escolha **Avançar**.

4. Na página **Selecionar Modo de Recuperação**, selecione **Arquivos e pastas individuais** e escolha **Avançar** para iniciar o processo de seleção do ponto de recuperação.

5. Na página **Selecionar Volume e Data**, selecione o volume que contém os arquivos ou as pastas que deseja restaurar e escolha **Montar**. Selecione uma data e uma hora no menu suspenso que correspondam a um ponto de recuperação. As datas em **negrito** indicam a disponibilidade de, pelo menos, um ponto de recuperação naquele dia.

    ![Selecionar volume e data](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    Quando você seleciona **Montar**, o Backup do Azure disponibiliza o ponto de recuperação como um disco. Procurar e recuperar os arquivos do disco.

## <a name="restore-items-from-a-recovery-point"></a>Restaurar itens de um ponto de recuperação

1. Depois que o volume de recuperação estiver montado, selecione **Procurar** para abrir o Windows Explorer e localizar os arquivos e as pastas que deseja recuperar.

    ![Selecionar Procurar](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Você pode abrir os arquivos diretamente do volume de recuperação e verificá-los.

2. No Windows Explorer, copie os arquivos e as pastas que deseja restaurar e cole-os em qualquer localização desejada no servidor.

    ![Copiar os arquivos e as pastas](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Quando terminar de restaurar os arquivos e as pastas, na página **Procurar e Recuperar Arquivos** do assistente **Recuperar Dados**, selecione **Desmontar**.

    ![Selecione desmontar](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. Selecione **Sim** para confirmar que deseja desmontar o volume.

    Depois que o instantâneo estiver desmontado, a mensagem **Trabalho Concluído** aparecerá no painel **Trabalhos** no console do agente.

## <a name="next-steps"></a>Próximas etapas

Isso conclui os tutoriais de backup e restauração dos dados do Windows Server para o Azure. Para saber mais sobre o Backup do Azure, consulte o exemplo do PowerShell para fazer backup de máquinas virtuais criptografadas.

> [!div class="nextstepaction"]
> [Fazer backup da VM criptografada](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
