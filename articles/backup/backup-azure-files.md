---
title: Backup de compartilhamentos de arquivos do Azure
description: Este artigo fornece detalhes sobre como fazer backup e restaurar seus compartilhamentos de arquivos do Azure e explica as tarefas de gerenciamento.
author: dcurwin
ms.author: dacurwin
ms.date: 07/29/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 9cb0150efcb4860af98b47aa5da4cfd24d2e9de9
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602020"
---
# <a name="back-up-azure-file-shares"></a>Backup de compartilhamentos de arquivos do Azure
Este artigo explica como usar o Portal do Azure para fazer backup e restaurar [compartilhamentos de arquivos do Azure](../storage/files/storage-files-introduction.md) no Azure.

Neste guia, você aprenderá a:
> [!div class="checklist"]
> * Configurar um cofre de Serviços de Recuperação para fazer backup de Arquivos do Azure
> * Executar um trabalho de backup sob demanda para criar um ponto de restauração
> * Restaurar um ou mais arquivos de um ponto de restauração
> * Gerenciar trabalhos de Backup
> * Interromper a proteção em Arquivos do Azure
> * Excluir os dados de backup

## <a name="prerequisites"></a>Pré-requisitos
Antes de fazer backup de um compartilhamento de arquivos do Azure, verifique se ele está presente em um dos [tipos de Conta de Armazenamento com suporte](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Depois fazer essa verificação, é possível proteger o compartilhamento dos arquivos.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações do backup do compartilhamento de arquivos do Azure durante a versão prévia
O backup para compartilhamentos de arquivos do Azure está em versão prévia. Há suporte para compartilhamentos de arquivos do Azure em contas de armazenamento de uso geral v1 e de uso geral v2. Não há suporte para os cenários de backup a seguir para compartilhamentos de arquivos do Azure:
- O suporte para Backup de Compartilhamentos de Arquivos do Azure em Contas de Armazenamento com replicação de ZRS ([armazenamento com redundância de zona](../storage/common/storage-redundancy-zrs.md)) está limitado no momento a [estas regiões](backup-azure-files-faq.md#in-which-geos-can-i-back-up-azure-file-shares-).
- Não é possível proteger compartilhamentos de arquivos no Azure em contas de armazenamento que têm redes virtuais ou firewall habilitados.
- Não há nenhuma CLI disponível para a proteção de Arquivos do Azure usando o Backup do Azure.
- No momento, o Backup do Azure é compatível com a configuração de backups agendados uma vez por dia do Compartilhamento de Arquivos do Azure.
- A quantidade máxima de backups agendados por dia é de um.
- A quantidade máxima de backups sob demanda por dia é de quatro.
- Use [bloqueios de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na conta de armazenamento para impedir a exclusão acidental de backups em seu cofre dos Serviços de Recuperação.
- Não exclua os instantâneos criados pelo Backup do Azure. A exclusão de instantâneos pode resultar na perda de pontos de recuperação e/ou em falhas de restauração.
- Não exclua os compartilhamentos de arquivos protegidos pelo Backup do Azure. A solução atual excluirá todos os instantâneos tirados pelo Backup do Azure após a exclusão do compartilhamento de arquivo e, portanto, perderá todos os pontos de restauração.



## <a name="configuring-backup-for-an-azure-file-share"></a>Configurar o backup para um compartilhamento de arquivos do Azure
Este tutorial presume que você já estabeleceu um compartilhamento de arquivos do Azure. Para fazer o backup do compartilhamento de arquivos do Azure:

1. Crie um cofre de Serviços de Recuperação na mesma região que o compartilhamento de arquivos. Caso já tenha um cofre, abra a página de Visão geral do cofre e clique em **Backup**.

    ![Escolha o compartilhamento de arquivos do Azure como meta de Backup](./media/backup-file-shares/overview-backup-page.png)

2. No menu **Meta de backup**, em **Do que deseja fazer backup?** , escolha o Compartilhamento de Arquivos do Azure.

    ![Escolha o compartilhamento de arquivos do Azure como meta de Backup](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Clique em **Backup** para configurar o compartilhamento de arquivos do Azure para seu cofre de Serviços de Recuperação.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/set-backup-goal.png)

    Depois de o cofre ser associado ao compartilhamento de arquivos do Azure, o menu Backup é aberto e solicita que você selecione uma Conta de armazenamento. O menu exibe todas as Contas de armazenamento com suporte na região do cofre e que ainda não estão associadas a um cofre de Serviços de Recuperação.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/list-of-storage-accounts.png)

4. Na lista de Contas de armazenamento, selecione uma conta e clique em **OK**. Na conta de armazenamento, o Azure procura compartilhamentos de arquivos cujo backup pode ser feito. Caso tenha adicionado seus compartilhamentos de arquivos e não os esteja vendo na lista, aguarde um pouco até que eles sejam exibidos.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/discover-file-shares.png)

5. Na lista de **Compartilhamentos de Arquivos**, selecione um ou mais dos compartilhamentos de arquivos dos quais deseja fazer backup e clique em **OK**.

6. Depois de escolher seus Compartilhamentos de Arquivos, o menu Backup alterna para **Política de backup**. Nesse menu, selecione uma política de backup existente ou crie uma nova, depois clique em **Habilitar backup**.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/apply-backup-policy.png)

    Depois de estabelecer uma política de backup, um instantâneo dos Compartilhamentos de Arquivos será executado no horário agendado, e o ponto de recuperação fica retido para o período escolhido.

## <a name="create-an-on-demand-backup"></a>Criar um backup sob demanda
Ocasionalmente, talvez você queira gerar um instantâneo de backup ou um ponto de recuperação fora dos horários agendados na política de backup. Um horário comum para gerar um backup sob demanda é logo depois de configurar a política de backup. Com base no agendamento na política de backup, pode levar horas ou mesmo dias até que um instantâneo seja tirado. Para proteger seus dados até que a política de backup seja ativada, inicie um backup sob demanda. Muitas vezes, é preciso criar um backup sob demanda antes de fazer alterações planejadas em seus compartilhamentos de arquivos.

### <a name="to-create-an-on-demand-backup"></a>Para criar um backup sob demanda

1. Abra o cofre de Serviços de Recuperação que contém os pontos de recuperação do compartilhamento de arquivos e clique em **Itens de Backup**. A lista com os tipos de Itens de Backup é exibida.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/list-of-backup-items.png)

2. Na lista, selecione **Armazenamento do Azure (Arquivos do Azure)** . A lista de compartilhamentos de arquivos do Azure é exibida.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Na lista de compartilhamentos de arquivos do Azure, selecione o compartilhamento de arquivo desejado. O menu Item de Backup do compartilhamento de arquivo selecionado é aberto.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/backup-item-menu.png)

4. No menu Item de Backup, clique em **Fazer Backup Agora**. Como esse é um trabalho de backup sob demanda, não há nenhuma política de retenção associada ao ponto de recuperação. A caixa de diálogo **Fazer Backup Agora** é aberta. Especifique até que dia deseja manter o ponto de recuperação escolhendo o último dia.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Restaurar a partir do backup do compartilhamento de arquivos do Azure
Quer você precise restaurar um compartilhamento de arquivo inteiro, arquivos individuais ou pastas de um Ponto de Restauração, vá até o Item de Backup, conforme detalhado na seção anterior. Escolha **Restaurar Compartilhamento** para restaurar um compartilhamento de arquivo inteiro de um ponto no tempo. Na lista de Pontos de Restauração que aparece, selecione um para ser capaz de Substituir o compartilhamento de arquivos atual ou restaurá-lo para um compartilhamento de arquivo alternativo na mesma região.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Restaurar arquivos individuais ou pastas do backup dos compartilhamentos de arquivos do Azure
O Backup do Azure fornece a capacidade de procurar um Ponto de Restauração no Portal do Azure. Para restaurar um arquivo ou a pasta de sua escolha, clique na Recuperação de Arquivo da página de Item de Backup e escolha a partir da lista de Pontos de Restauração. Selecione o Destino de Recuperação e clique em **Selecionar Arquivo** para navegar até o ponto de restauração. Selecione o arquivo ou a pasta de sua escolha e depois **Restaurar**.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Gerenciar backups de compartilhamento de arquivos do Azure

Você pode executar várias tarefas de gerenciamento para backups de compartilhamento de arquivos na página **Trabalhos de Backup**, incluindo:
- [Monitorar trabalhos](backup-azure-files.md#monitor-jobs)
- [Criar uma nova política](backup-azure-files.md#create-a-new-policy)
- [Interromper a proteção em um compartilhamento de arquivos](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [Retomar a proteção em um compartilhamento de arquivos](backup-azure-files.md#resume-protection-for-azure-file-share)
- [Excluir dados de backup](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Monitorar trabalhos

Você pode monitorar o progresso de todos os trabalhos na página **Trabalhos de Backup**.

Para abrir a página **Trabalhos de Backup**:

- Abra o cofre de Serviços de Recuperação que deseja monitorar e, no menu Cofre de Serviços de Recuperação, clique em **Trabalhos** e depois em **Trabalhos de Backup**.

   ![Selecionar o trabalho que deseja monitorar](./media/backup-file-shares/open-backup-jobs.png)

    A lista de trabalhos de backup e o status desses trabalhos são exibidos.

    ![Selecionar o trabalho que deseja monitorar](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Criar uma nova política

É possível criar uma nova política de backup de compartilhamentos de arquivos do Azure a partir das **Políticas de Backup** do cofre dos Serviços de Recuperação. Todas as políticas criadas ao configurar o backup para compartilhamentos de arquivos são exibidas com o Tipo de Política como Compartilhamento de arquivos do Azure.

Para exibir as políticas de backup existentes:

- Abra o cofre de Serviços de Recuperação que deseja e, no menu Cofre de Serviços de Recuperação, clique em **Políticas de backup**. Todas as políticas de backup são listadas.

   ![Selecionar o trabalho que deseja monitorar](./media/backup-file-shares/list-of-backup-policies.png)

Para criar uma nova política de backup:

1. No menu Cofre de Serviços de Recuperação, clique em **Políticas de backup**.
2. Na lista de políticas de backup, clique em **Adicionar**.

   ![Selecionar o trabalho que deseja monitorar](./media/backup-file-shares/new-backup-policy.png)

3. No menu **Adicionar**, selecione **Compartilhamento de Arquivos do Azure**. O menu Política de Backup para o compartilhamento de arquivos do Azure é aberto. Forneça o nome para a política, a frequência de backup e o intervalo de retenção dos pontos de recuperação. Clique em OK quando você tiver definido a política.

   ![Selecionar o trabalho que deseja monitorar](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Interromper a proteção de um compartilhamento de arquivos do Azure

Caso opte por interromper a proteção de um compartilhamento de arquivos do Azure, você será perguntado se deseja manter os pontos de recuperação. Há duas maneiras de interromper a proteção de compartilhamentos de arquivos do Azure:

- Parar todos os trabalhos de backup futuros e excluir todos os pontos de recuperação ou
- Parar todos os trabalhos de backup futuros, mas deixar os pontos de recuperação

Pode haver um custo associado ao deixar os pontos de recuperação no armazenamento, uma vez que os instantâneos subjacentes criados pelo Backup do Azure serão retidos. No entanto, a vantagem de deixar os pontos de recuperação é que você pode restaurar o compartilhamento de arquivos mais tarde, se desejado. Para obter informações sobre o custo de deixar os pontos de recuperação, confira os detalhes de preços. Caso opte por excluir todos os pontos de recuperação, não poderá restaurar o compartilhamento de arquivos.

Para parar a proteção de um compartilhamento de arquivos do Azure:

1. Abra o cofre de Serviços de Recuperação que contém os pontos de recuperação do compartilhamento de arquivos e clique em **Itens de Backup**. A lista com os tipos de Itens de Backup é exibida.

   ![Clique em Backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-file-shares/list-of-backup-items.png)

2. Na lista **Tipo de Gerenciamento de Backup**, selecione **Armazenamento do Azure (Arquivos do Azure)** . A lista de itens de backup para (Armazenamento do Azure (Arquivos do Azure)) é exibida.

   ![clicar no item para abrir o menu adicional](./media/backup-file-shares/azure-file-share-backup-items.png)

3. Na lista de Itens de Backup (Armazenamento do Azure (Arquivos do Azure)), selecione o item de backup que você deseja interromper.

4. Nos itens de compartilhamento de arquivos do Azure, clique no menu **Mais** e selecione **Parar Backup**.

   ![clicar no item para abrir o menu adicional](./media/backup-file-shares/stop-backup.png)

5. No menu Parar Backup, escolha **Reter** ou **Excluir Dados de Backup** e clique em **Parar Backup**.

   ![clicar no item para abrir o menu adicional](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Parar a proteção de um compartilhamento de arquivos do Azure

Se a opção Reter Dados do Backup foi selecionada quando a proteção do compartilhamento de arquivos foi interrompida, será possível retomar a proteção. Se a opção **Excluir Dados do Backup** foi escolhida, então, a proteção do compartilhamento de arquivos não poderá ser retomada.

Para retomar a proteção do compartilhamento de arquivos, vá até o Item de Backup e clique em Retomar Backup. A Política de Backup é aberta, e você pode escolher uma política para retomar o backup.

   ![Selecionar o trabalho que deseja monitorar](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Excluir dados de backup

Você pode excluir o backup de um compartilhamento de arquivos durante a interrupção do trabalho de backup ou a qualquer momento depois de interromper a proteção. Pode até mesmo ser benéfico aguardar dias ou semanas antes de excluir os pontos de recuperação. Ao contrário de restaurar os pontos de recuperação, ao excluir os dados do backup, você não pode escolher os pontos de recuperação específicos para excluir. Se você escolher excluir os dados de backup, apagará todos os pontos de recuperação associados ao item.

O procedimento a seguir pressupõe que o trabalho de backup da máquina virtual foi interrompido. Depois que o trabalho de backup for interrompido, as opções Retomar Backup e Excluir Dados do Backup ficam disponíveis no painel de itens do backup. Clique em Excluir Dados de Backup e digite o nome do compartilhamento de arquivos para confirmar a exclusão. Como opção, forneça um Motivo para a exclusão ou Comentário.

## <a name="see-also"></a>Veja também
Para saber mais sobre os compartilhamentos de arquivos do Azure, confira
- [Perguntas frequentes para o backup do compartilhamento de arquivos do Azure](backup-azure-files-faq.md)
- [Solucionar problemas de backup de compartilhamento de arquivos do Azure](troubleshoot-azure-files.md)
