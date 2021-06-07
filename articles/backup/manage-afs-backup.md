---
title: Gerenciar backups de compartilhamento de arquivos do Azure
description: Este artigo descreve as tarefas comuns para gerenciar e monitorar os compartilhamentos de arquivos do Azure cujo backup é feito pelo backup do Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 784b22f11b57fb025f6d9401e10d527c83751898
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88757618"
---
# <a name="manage-azure-file-share-backups"></a>Gerenciar backups de compartilhamento de arquivos do Azure

Este artigo descreve as tarefas comuns para gerenciar e monitorar os compartilhamentos de arquivos do Azure cujo backup é feito pelo [backup do Azure](./backup-overview.md). Você aprenderá como realizar tarefas de gerenciamento no cofre dos serviços de recuperação.

## <a name="monitor-jobs"></a>Monitorar trabalhos

Quando você dispara uma operação de backup ou restauração, o serviço de backup cria um trabalho para acompanhamento. Você pode monitorar o progresso de todos os trabalhos na página **Trabalhos de Backup**.

Para abrir a página **Trabalhos de Backup**:

1. Abra o cofre dos serviços de recuperação que você usou para configurar o backup para seus compartilhamentos de arquivos. No painel **visão geral** , selecione **trabalhos de backup** na seção **monitoramento** .

   ![Trabalhos de backup na seção monitoramento](./media/manage-afs-backup/backup-jobs.png)

1. Depois de selecionar **OK**, o painel **trabalhos de backup** lista o status de todos os trabalhos. Selecione o nome da carga de trabalho que corresponde ao compartilhamento de arquivos que você deseja monitorar.

   ![Nome da carga de trabalho](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Criar uma nova política

Você pode criar uma nova política para fazer backup de compartilhamentos de arquivos do Azure na seção **políticas de backup** do cofre dos serviços de recuperação. Todas as políticas criadas quando você configurou o backup para compartilhamentos de arquivos aparecem com o **tipo de política** como **compartilhamento de arquivos do Azure**.

Para exibir as políticas de backup existentes:

1. Abra o cofre dos serviços de recuperação que você usou para configurar o backup para o compartilhamento de arquivos. No menu do cofre dos serviços de recuperação, selecione **políticas de backup** na seção **gerenciar** . Todas as políticas de backup configuradas no cofre são exibidas.

   ![Todas as políticas de backup](./media/manage-afs-backup/all-backup-policies.png)

1. Para exibir políticas específicas do **compartilhamento de arquivos do Azure**, selecione **compartilhamento de arquivos do Azure** na lista suspensa no canto superior direito.

   ![Selecionar compartilhamento de arquivos do Azure](./media/manage-afs-backup/azure-file-share.png)

Para criar uma nova política de backup:

1. No painel **políticas de backup** , selecione **+ Adicionar**.

   ![Nova política de backup](./media/manage-afs-backup/new-backup-policy.png)

1. No painel **Adicionar** , selecione **compartilhamento de arquivos do Azure** como o **tipo de política**. O painel **política de backup** para o compartilhamento de **arquivos do Azure** é aberto. Especifique o nome da política, a frequência de backup e o período de retenção para os pontos de recuperação. Depois de definir a política, selecione **OK**.

   ![Definir a política de backup](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modificar a política

Você pode modificar uma política de backup para alterar a frequência de backup ou o período de retenção.

Para modificar uma política:

1. Abra o cofre dos serviços de recuperação que você usou para configurar o backup para o compartilhamento de arquivos. No menu do cofre dos serviços de recuperação, selecione **políticas de backup** na seção **gerenciar** . Todas as políticas de backup configuradas no cofre são exibidas.

   ![Todas as políticas de backup no cofre](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Para exibir as políticas específicas de um compartilhamento de arquivos do Azure, selecione **compartilhamento de arquivos do Azure** na lista suspensa no canto superior direito. Selecione a política de backup que você deseja modificar.

   ![Compartilhamento de arquivos do Azure para modificar](./media/manage-afs-backup/azure-file-share-modify.png)

1. O painel **agenda** é aberto. Edite o **agendamento de backup** e o período de **retenção** conforme necessário e selecione **salvar**. Você verá uma mensagem "atualização em andamento" no painel. Depois que a política alterar a atualização com êxito, você verá a mensagem "a política de backup foi atualizada com êxito".

   ![Salvar a política modificada](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Interromper a proteção em um compartilhamento de arquivo

Há duas maneiras de interromper a proteção de compartilhamentos de arquivos do Azure:

* Pare todos os trabalhos de backup futuros e *exclua todos os pontos de recuperação*.
* Pare todos os trabalhos de backup futuros, mas *deixe os pontos de recuperação*.

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, pois os instantâneos subjacentes criados pelo backup do Azure serão mantidos. O benefício de deixar os pontos de recuperação é que você pode restaurar o compartilhamento de arquivos posteriormente. Para obter informações sobre o custo de deixar os pontos de recuperação, confira os [detalhes de preços](https://azure.microsoft.com/pricing/details/backup/). Se você decidir excluir todos os pontos de recuperação, não poderá restaurar o compartilhamento de arquivos.

Para parar a proteção de um compartilhamento de arquivos do Azure:

1. Abra o cofre dos serviços de recuperação que contém os pontos de recuperação do compartilhamento de arquivos. Selecione **itens de backup** na seção **itens protegidos** . A lista de tipos de item de backup é exibida.

   ![Itens de Backup](./media/manage-afs-backup/backup-items.png)

1. Na lista **Tipo de Gerenciamento de Backup**, selecione **Armazenamento do Azure (Arquivos do Azure)**. A lista **itens de backup (armazenamento do Azure (arquivos do Azure))** é exibida.

   ![Selecionar o armazenamento do Azure (arquivos do Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na lista **itens de backup (armazenamento do Azure (arquivos do Azure))** , selecione o item de backup para o qual você deseja interromper a proteção.

1. Selecione a opção **parar backup** .

   ![Selecionar Parar Backup](./media/manage-afs-backup/stop-backup.png)

1. No painel **parar backup** , selecione **reter dados de backup** ou **excluir dados de backup**. Em seguida, selecione **parar backup**.

    ![Selecione reter dados de backup ou excluir dados de backup](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção em um compartilhamento de arquivo

Se a opção **reter dados de backup** foi selecionada quando a proteção para o compartilhamento de arquivos foi interrompida, é possível retomar a proteção. Se a opção **excluir dados de backup** tiver sido selecionada, a proteção para o compartilhamento de arquivos não poderá ser retomada.

Para retomar a proteção para o compartilhamento de arquivos do Azure:

1. Abra o cofre dos serviços de recuperação que contém os pontos de recuperação do compartilhamento de arquivos. Selecione **itens de backup** na seção **itens protegidos** . A lista de tipos de item de backup é exibida.

   ![Itens de backup para retomar](./media/manage-afs-backup/backup-items-resume.png)

1. Na lista **Tipo de Gerenciamento de Backup**, selecione **Armazenamento do Azure (Arquivos do Azure)**. A lista **itens de backup (armazenamento do Azure (arquivos do Azure))** é exibida.

   ![Lista de armazenamento do Azure (arquivos do Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na lista **itens de backup (armazenamento do Azure (arquivos do Azure))** , selecione o item de backup para o qual você deseja retomar a proteção.

1. Selecione a opção **retomar backup** .

   ![Selecione retomar backup](./media/manage-afs-backup/resume-backup.png)

1. O painel **política de backup** é aberto. Selecione uma política de sua escolha para retomar o backup.

1. Depois de selecionar uma política de backup, selecione **salvar**. Você verá uma mensagem de "atualização em andamento" no Portal. Depois que o backup for retomado com êxito, você verá a mensagem "política de backup atualizada com êxito para o compartilhamento de arquivos do Azure protegido".

   ![Política de backup atualizada com êxito](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Excluir dados de backup

Você pode excluir o backup de um compartilhamento de arquivos durante o trabalho **parar backup** ou a qualquer momento depois de interromper a proteção. Pode ser benéfico esperar dias ou até mesmo semanas antes de excluir os pontos de recuperação. Ao excluir dados de backup, você não pode escolher pontos de recuperação específicos para exclusão. Se você decidir excluir os dados de backup, exclua todos os pontos de recuperação associados ao compartilhamento de arquivos.

O procedimento a seguir pressupõe que a proteção foi interrompida para o compartilhamento de arquivos.

Para excluir dados de backup para o compartilhamento de arquivos do Azure:

1. Depois que o trabalho de backup for interrompido, as opções **retomar backup** e **excluir dados de backup** estarão disponíveis no painel do **item de backup** . Selecione a opção **excluir dados de backup** .

   ![Excluir dados de backup](./media/manage-afs-backup/delete-backup-data.png)

1. O painel **excluir dados de backup** é aberto. Insira o nome do compartilhamento de arquivos para confirmar a exclusão. Opcionalmente, forneça mais informações nas caixas **motivo** ou **comentários** . Depois de ter certeza sobre a exclusão dos dados de backup, selecione **excluir**.

   ![Confirmar exclusão de dados](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Cancelar o registro de uma conta de armazenamento

Para proteger seus compartilhamentos de arquivos em uma determinada conta de armazenamento usando um cofre de serviços de recuperação diferente, primeiro [interrompa a proteção para todos os compartilhamentos de arquivos](#stop-protection-on-a-file-share) nessa conta de armazenamento. Em seguida, cancele o registro da conta do cofre de serviços de recuperação atual usado para proteção.

O procedimento a seguir pressupõe que a proteção foi interrompida para todos os compartilhamentos de arquivos na conta de armazenamento cujo registro você deseja cancelar.

Para cancelar o registro da conta de armazenamento:

1. Abra o cofre dos serviços de recuperação em que sua conta de armazenamento está registrada.
1. No painel **visão geral** , selecione a opção **infraestrutura de backup** na seção **gerenciar** .

   ![Selecionar Infraestrutura de Backup](./media/manage-afs-backup/backup-infrastructure.png)

1. O painel **infraestrutura de backup** é aberto. Selecione **contas de armazenamento** na seção **contas de armazenamento do Azure** .

   ![Selecionar contas de armazenamento](./media/manage-afs-backup/storage-accounts.png)

1. Depois de selecionar **contas de armazenamento**, uma lista de contas de armazenamento registradas com o cofre é exibida.
1. Clique com o botão direito do mouse na conta de armazenamento que você deseja cancelar o registro e selecione **Cancelar registro**.

   ![Selecione cancelar registro](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Troubleshoot Azure File shares backup](./troubleshoot-azure-files.md).
