---
title: Gerenciar e monitorar backups de VM do Azure usando o serviço de Backup do Azure
description: Saiba como gerenciar e monitorar backups de VM do Azure usando o serviço de Backup do Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: aa953440f03137f3359276bc9e06cb0c73f0ab4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218968"
---
# <a name="manage-azure-vm-backups"></a>Gerenciar backups de VM do Azure

Este artigo descreve como gerenciar máquinas virtuais (VMs) que passam por backup usando o [serviço de Backup do Azure](backup-overview.md). O artigo também resume as informações de backup que você pode encontrar no painel do cofre.


No portal do Azure, o painel do Cofre de serviços de recuperação fornece acesso para o Cofre de informações, incluindo:

* O backup mais recente, que também é o ponto de restauração mais recente.
* A política de backup.
* O tamanho total de todos os instantâneos de backup.
* O número de VMs que são habilitados para backups.

Você pode gerenciar backups usando o painel e Detalhar para VMs individuais. Para começar a backups de máquina, abra o cofre no painel.

![Exibição de painel completo com controle deslizante](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Exibir as VMs no painel

Para exibir as VMs no painel do cofre:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, selecione **procurar**. Na lista de recursos, digite **Serviços de Recuperação**. Conforme você digita, a lista é filtrada com base na sua entrada. Selecione **Cofres de Serviços de Recuperação**.

    ![Criar um cofre dos Serviços de Recuperação](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Para facilidade de uso, clique com botão direito do cofre e selecione **fixar no painel**.
4. Abra o painel do cofre.

    ![Abra o painel do cofre e a folha de configurações](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Sobre o **itens de Backup** lado a lado, selecione **máquinas virtuais do Azure**.

    ![Abra o bloco de itens de Backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Sobre o **itens de Backup** folha, você pode exibir a lista de VMs protegidas. Neste exemplo, o cofre protege uma máquina virtual: demobackup.  

    ![Exiba a folha de itens de Backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. No painel do item do cofre, modificar políticas de backup, execute uma parada de backup, sob demanda ou retomar a proteção de VMs, excluir dados de backup, exibir pontos de restauração e executar uma restauração.

    ![O painel de itens de Backup e a folha de configurações](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gerenciar política de backup para uma VM

Para gerenciar uma política de backup:

1. Entre no [Portal do Azure](https://portal.azure.com/). Abra o painel do cofre.
2. Sobre o **itens de Backup** lado a lado, selecione **máquinas virtuais do Azure**.

    ![Abra o bloco de itens de Backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Sobre o **itens de Backup** folha, você pode exibir a lista de VMs protegidas e status do último backup com o tempo de pontos de restauração mais recente.

    ![Exiba a folha de itens de Backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. No painel do item do cofre, você pode selecionar uma política de backup.

   * Para alternar as políticas, selecione uma política diferente e, em seguida, selecione **salvar**. A nova política será aplicada imediatamente no cofre.

     ![Escolha uma política de backup](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda
Você pode executar um backup sob demanda de uma VM depois de configurar sua proteção. Lembre-se esses detalhes:

- Se o backup inicial estiver pendente, o backup sob demanda cria uma cópia completa da VM no cofre de serviços de recuperação.
- Se o backup inicial for concluído, um backup sob demanda enviará apenas as alterações desde o instantâneo anterior para o cofre dos serviços de recuperação. Ou seja, os backups posteriores serão sempre incrementais.
- O período de retenção para um backup sob demanda é o valor de retenção que você especifique quando você dispara o backup.

Para disparar um backup sob demanda:

1. Sobre o [painel do item do cofre](#view-vms-on-the-dashboard), em **Item protegido**, selecione **Item de Backup**.

    ![A opção de Backup agora](./media/backup-azure-manage-vms/backup-now-button.png)

2. Partir **tipo de gerenciamento de Backup**, selecione **Máquina Virtual do Azure**. O **Item de Backup (Máquina Virtual do Azure)** folha é exibida.
3. Selecione uma máquina virtual e selecione **fazer Backup agora** para criar um backup sob demanda. O **fazer Backup agora** folha é exibida.
4. No **reter Backup até** , especifique uma data para o backup a ser retido.

    ![O calendário fazer Backup agora](./media/backup-azure-manage-vms/backup-now-check.png)

5. Selecione **Okey** para executar o trabalho de backup.

Para acompanhar o progresso do trabalho, no painel do cofre, selecione a **trabalhos de Backup** lado a lado.

## <a name="stop-protecting-a-vm"></a>Interromper a proteção de uma VM

Há duas maneiras de interromper a proteção de uma VM:

- Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação. Nesse caso, você não poderá restaurar a VM.
- Parar todos os trabalhos de backup futuros e manter os pontos de recuperação. Embora você precisará pagar manter os pontos de recuperação no cofre, você poderá restaurar a VM, se necessário. Para obter mais informações, consulte [preços de Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Se você excluir uma fonte de dados sem interromper os backups, novos backups falhará. Pontos de recuperação antigos irá expirar de acordo com a política, mas um último ponto de recuperação sempre será mantido até você interrompê-los e excluir os dados.
>

Para interromper a proteção para uma VM:

1. Sobre o [painel do item do cofre](#view-vms-on-the-dashboard), selecione **parar backup**.
2. Escolha se deseja reter ou excluir os dados de backup e confirme sua seleção, conforme necessário. Se desejar, adicione um comentário. Se você não tiver certeza do nome do item, passe o mouse sobre o ponto de exclamação para exibir o nome.

    ![Parar a proteção](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     Uma notificação permite que você saiba o que os trabalhos de backup foram interrompidos.

## <a name="resume-protection-of-a-vm"></a>Retomar a proteção de uma VM

Se você mantiver os dados de backup quando você interromper a VM, você pode retomar a proteção mais tarde. Se você excluir os dados de backup, não é possível retomar a proteção.

Para retomar a proteção para uma VM:

1. Sobre o [painel do item do cofre](#view-vms-on-the-dashboard), selecione **retomar backup**.

2. Siga as etapas em [gerenciar políticas de backup](#manage-backup-policy-for-a-vm) para atribuir a política para a VM. Você não precisa escolher a política de proteção inicial da VM.
3. Depois de aplicar a política de backup na VM, você verá a seguinte mensagem:

    ![Mensagem indicando que uma VM protegida com êxito](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Excluir dados de backup

Você pode excluir dados de backup da VM durante o **parar backup** trabalho ou após a conclusão do trabalho de backup. Antes de excluir dados de backup, tenha esses detalhes em mente:

- Ele pode ser uma boa ideia aguardar dias ou semanas antes de excluir os pontos de recuperação.
- Ao contrário do processo de restauração de pontos de recuperação, quando você exclui dados de backup, você não pode escolher pontos de recuperação específicos para excluir. Se você excluir os dados de backup, você pode excluir todos os pontos de recuperação associados.

Depois que você pare ou desabilite o trabalho de backup da VM, você pode excluir os dados de backup:


1. Sobre o [painel do item do cofre](#view-vms-on-the-dashboard), selecione **excluir dados de backup**.

    ![Selecione Excluir backup](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. Digite o nome do item backup para confirmar que você deseja excluir os pontos de recuperação.

    ![Confirme que você deseja excluir os pontos de recuperação](./media/backup-azure-manage-vms/item-verification-box.png)

1. Para excluir os dados de backup para o item, selecione **excluir**. Uma mensagem de notificação permite que você saiba o que os dados de backup foi excluídos.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [fazer backup de VMs do Azure de configurações da VM](backup-azure-vms-first-look-arm.md).
- Saiba como [restauração de VMs](backup-azure-arm-restore-vms.md).
- Saiba como [monitorar os backups de VM do Azure](backup-azure-monitor-vms.md).
