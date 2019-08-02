---
title: Gerenciar e monitorar backups de VM do Azure usando o serviço de backup do Azure
description: Saiba como gerenciar e monitorar backups de VM do Azure usando o serviço de backup do Azure.
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: 3a958e2397071407f6283938382ad7dd8c07bc09
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466265"
---
# <a name="manage-azure-vm-backups"></a>Gerenciar backups de VM do Azure

Este artigo descreve como gerenciar VMs (máquinas virtuais) do Azure cujo backup é feito usando o serviço de [backup do Azure](backup-overview.md). O artigo também resume as informações de backup que você pode encontrar no painel do cofre.


No portal do Azure, o painel do cofre dos serviços de recuperação fornece acesso a informações do cofre, incluindo:

* O backup mais recente, que também é o ponto de restauração mais recente.
* A política de backup.
* O tamanho total de todos os instantâneos de backup.
* O número de VMs que estão habilitadas para backups.

Você pode gerenciar backups usando o painel e fazendo Drill down para VMs individuais. Para iniciar os backups do computador, abra o cofre no painel.

![Exibição de painel completa com controle deslizante](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Exibir VMs no painel

Para exibir as VMs no painel do cofre:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, selecione **procurar**. Na lista de recursos, digite **Serviços de Recuperação**. Conforme você digita, a lista é filtrada com base em sua entrada. Selecione **Cofres de Serviços de Recuperação**.

    ![Criar um cofre dos Serviços de Recuperação](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Para facilitar o uso, clique com o botão direito do mouse no cofre e selecione **fixar no painel**.
4. Abra o painel do cofre.

    ![Abra o painel do cofre e a folha de configurações](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. No bloco **itens de backup** , selecione **máquinas virtuais do Azure**.

    ![Abrir o bloco itens de backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Na folha **itens** de backup, você pode exibir a lista de VMs protegidas. Neste exemplo, o cofre protege uma máquina virtual: demobackup.  

    ![Exibir a folha itens de backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. No painel do item do cofre, modifique as políticas de backup, execute um backup sob demanda, pare ou retome a proteção de VMs, exclua dados de backup, exiba pontos de restauração e execute uma restauração.

    ![O painel itens de backup e a folha configurações](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gerenciar a política de backup para uma VM

Para gerenciar uma política de backup:

1. Entre no [Portal do Azure](https://portal.azure.com/). Abra o painel do cofre.
2. No bloco **itens de backup** , selecione **máquinas virtuais do Azure**.

    ![Abrir o bloco itens de backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Na folha **itens** de backup, você pode exibir a lista de VMs protegidas e o status do último backup com o tempo dos pontos de restauração mais recentes.

    ![Exibir a folha itens de backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. No painel do item do cofre, você pode selecionar uma política de backup.

   * Para alternar políticas, selecione uma política diferente e, em seguida, selecione **salvar**. A nova política será aplicada imediatamente no cofre.

     ![Escolha uma política de backup](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda
Você pode executar um backup sob demanda de uma VM depois de configurar sua proteção. Tenha estes detalhes em mente:

- Se o backup inicial estiver pendente, o backup sob demanda criará uma cópia completa da VM no cofre dos serviços de recuperação.
- Se o backup inicial for concluído, um backup sob demanda só enviará alterações do instantâneo anterior para o cofre dos serviços de recuperação. Ou seja, os backups posteriores são sempre incrementais.
- O período de retenção para um backup sob demanda é o valor de retenção que você especifica ao disparar o backup.

Para disparar um backup sob demanda:

1. No [painel do item do cofre](#view-vms-on-the-dashboard), em **item protegido**, selecione **item de backup**.

    ![A opção fazer backup agora](./media/backup-azure-manage-vms/backup-now-button.png)

2. Em **tipo de gerenciamento de backup**, selecione **máquina virtual do Azure**. A folha **item de backup (máquina virtual do Azure)** é exibida.
3. Selecione uma VM e selecione **fazer backup agora** para criar um backup sob demanda. A folha **fazer backup agora** é exibida.
4. No campo **reter backup até** , especifique uma data para o backup a ser retido.

    ![O calendário de backup agora](./media/backup-azure-manage-vms/backup-now-check.png)

5. Selecione **OK** para executar o trabalho de backup.

Para acompanhar o progresso do trabalho, no painel do cofre, selecione o bloco **trabalhos de backup** .

## <a name="stop-protecting-a-vm"></a>Interromper a proteção de uma VM

Há duas maneiras de interromper a proteção de uma VM:

* **Interromper a proteção e manter os dados de backup**. Esta opção impedirá que todos os trabalhos de backup futuros protejam sua VM; no entanto, o serviço de backup do Azure manterá os pontos de recuperação que foram armazenados em backup.  Você precisará pagar para manter os pontos de recuperação no cofre (consulte [preços de backup do Azure](https://azure.microsoft.com/pricing/details/backup/) para obter detalhes). Você poderá restaurar a VM, se necessário. Se você decidir retomar a proteção da VM, poderá usar a opção *retomar backup* .
* **Interrompa a proteção e exclua os dados de backup**. Esta opção impedirá que todos os trabalhos de backup futuros protejam sua VM e exclua todos os pontos de recuperação. Você não poderá restaurar a VM nem usar a opção *retomar backup* .

>[!NOTE]
>Se você excluir uma fonte de dados sem interromper os backups, novos backups falharão. Os pontos de recuperação antigos expirarão de acordo com a política, mas um último ponto de recuperação será sempre mantido até que você interrompa os backups e exclua os dados.
>

### <a name="stop-protection-and-retain-backup-data"></a>Interromper a proteção e manter os dados de backup

Para interromper a proteção e manter os dados de uma VM:

1. No [painel do item do cofre](#view-vms-on-the-dashboard), selecione **parar backup**.
2. Escolha **reter dados de backup**e confirme sua seleção, conforme necessário. Se desejar, adicione um comentário. Se você não tiver certeza do nome do item, passe o mouse sobre o ponto de exclamação para exibir o nome.

    ![Reter dados de backup](./media/backup-azure-manage-vms/retain-backup-data.png)

Uma notificação permite que você saiba que os trabalhos de backup foram interrompidos.

### <a name="stop-protection-and-delete-backup-data"></a>Interromper a proteção e excluir dados de backup

Para interromper a proteção e excluir dados de uma VM:

1. No [painel do item do cofre](#view-vms-on-the-dashboard), selecione **parar backup**.
2. Escolha **excluir dados de backup**e confirme sua seleção, conforme necessário. Insira o nome do item de backup e adicione um comentário, se desejar.

    ![Excluir dados de backup](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Retomar a proteção de uma VM

Se você tiver escolhido a opção [parar proteção e manter dados de backup](#stop-protection-and-retain-backup-data) durante a proteção da VM de parada, você poderá usar retomar **backup**. Essa opção não estará disponível se você escolher a opção [parar proteção e excluir dados de backup](#stop-protection-and-delete-backup-data) ou [excluir dados de backup](#delete-backup-data).

Para retomar a proteção de uma VM:

1. No [painel do item do cofre](#view-vms-on-the-dashboard), selecione **retomar backup**.

2. Siga as etapas em [gerenciar políticas de backup](#manage-backup-policy-for-a-vm) para atribuir a política para a VM. Você não precisa escolher a política de proteção inicial da VM.
3. Depois de aplicar a política de backup à VM, você verá a seguinte mensagem:

    ![Mensagem indicando uma VM protegida com êxito](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Excluir dados de backup

Há duas maneiras de excluir os dados de backup de uma VM:

- No painel do item do cofre, selecione parar backup e siga as instruções para [parar proteção e excluir dados de backup](#stop-protection-and-delete-backup-data) .

  ![Selecionar Parar Backup](./media/backup-azure-manage-vms/stop-backup-buttom.png)

- No painel do item do cofre, selecione excluir dados de backup. Essa opção será habilitada se você tiver optado por [interromper a proteção e manter a opção de dados de backup](#stop-protection-and-retain-backup-data) durante a proteção da VM de parada

  ![Selecione Excluir backup](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  - No [painel do item do cofre](#view-vms-on-the-dashboard), selecione **excluir dados de backup**.
  - Digite o nome do item de backup para confirmar que você deseja excluir os pontos de recuperação.

    ![Excluir dados de backup](./media/backup-azure-manage-vms/delete-backup-data1.png)

  - Para excluir os dados de backup do item, selecione **excluir**. Uma mensagem de notificação permite que você saiba que os dados de backup foram excluídos.

  > [!NOTE]
  > Ao excluir dados de backup, você exclui todos os pontos de recuperação associados. Você não pode escolher pontos de recuperação específicos para excluir.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [fazer backup de VMs do Azure nas configurações da VM](backup-azure-vms-first-look-arm.md).
- Saiba como [restaurar VMs](backup-azure-arm-restore-vms.md).
- Saiba como [monitorar backups de VM do Azure](backup-azure-monitor-vms.md).
