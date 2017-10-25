---
title: "Gerenciar backups de máquina virtual implantados pelo Resource Manager | Microsoft Docs"
description: "Saiba como gerenciar e monitorar os backups da máquina virtual implantados pelo Gerenciador de Recursos"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: f3050283-d60f-472d-b464-cb844e70d67e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: trinadhk;markgal
ms.openlocfilehash: 35a21cb99ca4bad124a9f764cef9da453e1fe47f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-virtual-machine-backups"></a>Gerenciar backups de máquinas virtuais do Azure
> [!div class="op_single_selector"]
> * [Gerenciar backups da VM do Azure](backup-azure-manage-vms.md)
> * [Gerenciar backups da VM Clássica](backup-azure-manage-vms-classic.md)
>
>

Este artigo fornece orientações sobre como gerenciar backups de VM e explica as informações sobre alertas de backup disponíveis no painel do portal. As diretrizes neste artigo se aplicam ao uso das VMs com cofres dos Serviços de Recuperação. Este artigo não aborda a criação das máquinas virtuais, nem explica como protegê-las. Para ter instruções elementares sobre como proteger as VMs implantadas pelo Azure Resource Manager no Azure com um cofre dos Serviços de Recuperação, consulte [Primeiro examinar: fazer backup das VMs em um cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Gerenciar cofres e máquinas virtuais protegidas
No portal do Azure, o painel do cofre dos Serviços de Recuperação fornece acesso a informações sobre o cofre, inclusive:

* o instantâneo de backup mais recente, que também é o ponto de restauração mais recente <br\>
* política de backup <br\>
* tamanho total de todos os instantâneos do backup <br\>
* número de máquinas virtuais que são protegidas com o cofre <br\>

Muitas tarefas de gerenciamento com um backup da máquina virtual começam abrindo o cofre no painel de controle. No entanto, como os cofres podem ser usados para proteger vários itens (ou várias VMs), para exibir detalhes sobre uma determinada VM, abra o painel do item do cofre. O procedimento a seguir mostra como abrir o *painel do cofre*, em seguida, ir para o *painel do item do cofre*. Há "dicas" em ambos os procedimentos que mostram como adicionar o cofre e o item do cofre ao painel do Azure usando o Pin no comando do painel. Fixar no painel é uma maneira de criar um atalho para o cofre ou o item. Você também pode executar os comandos comuns a partir do atalho.

> [!TIP]
> Se você tiver vários painéis e folhas abertos, use o controle azul escuro na parte inferior da janela para deslizar o painel do Azure.
>
>

![Exibição completa com controle deslizante](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Abra um cofre dos Serviços de Recuperação no painel:
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Procurar** e, na lista de recursos, digite **Serviços de Recuperação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Clique em **Cofre de Serviços de Recuperação**.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    A lista de cofres de Serviços de Recuperação será exibida.

    ![Listar cofres de Serviços de Recuperação ](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

   > [!TIP]
   > Se você fixar um cofre no Painel do Azure, esse cofre ficará imediatamente acessível quando você abrir o portal do Azure. Para fixar um cofre no painel, na lista de cofres, clique com o botão direito no cofre e selecione **Fixar no painel**.
   >
   >
3. Na lista de cofres, escolha o cofre para abrir seu painel. Quando você seleciona o cofre, o painel do cofre e a folha **Configurações** são abertos. Na imagem a seguir, o painel **Cofre Contoso** é destacado.

    ![Abrir o painel do cofre e a folha de Configurações](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Abra o painel do item do cofre
No procedimento anterior, você abriu o painel do cofre. Para abrir o painel do item do cofre:

1. No painel do cofre, no bloco **Itens de Backup**, clique em **Máquinas Virtuais do Azure**.

    ![Bloco Abrir itens de backup](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    A folha **Itens de Backup** lista o último trabalho de backup de cada item. Neste exemplo, há uma máquina virtual, demovm-markgal, protegida por esse cofre.  

    ![Bloco Itens de backup](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > Para facilitar o acesso, você pode fixar um item do cofre no Painel do Azure. Para fixar um item do cofre, na lista de itens do cofre, clique com o botão direito no item e selecione **Fixar no painel**.
   >
   >
2. Na folha **Itens de Backup** , clique no item para abrir o painel do item do cofre.

    ![Bloco Itens de backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    O painel do item do cofre e sua folha **Configurações** são abertos.

    ![Painel dos itens de backup com a folha Configurações](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    No painel de itens do cofre, você pode executar muitas tarefas de gerenciamento principais, como:

   * alterar as políticas ou criar uma nova política de backup <br\>
   * exibir pontos de restauração e ver seu estado de consistência <br\>
   * Backup sob demanda de uma máquina virtual <br\>
   * interromper a proteção das máquinas virtuais <br\>
   * retomar a proteção de uma máquina virtual <br\>
   * excluir os dados do backup (ou ponto de recuperação) <br\>
   * [restaurar discos de backup](backup-azure-arm-restore-vms.md#restore-backed-up-disks)  <br\>

Para os procedimentos a seguir, o ponto de partida é o painel de itens do cofre.

## <a name="manage-backup-policies"></a>Gerenciar políticas de backup
1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Todas as Configurações** para abrir a folha **Configurações**.

    ![Folha Política de backup](./media/backup-azure-manage-vms/all-settings-button.png)
2. Na folha **Configurações**, clique em **Política de backup** para abrir essa folha.

    Na folha, são mostrados os detalhes do intervalo de retenção e da frequência dos backups.

    ![Folha Política de backup](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. No menu **Escolher política de backup** :

   * Para alterar as políticas, selecione uma política diferente e clique em **Salvar**. A nova política será aplicada imediatamente no cofre. <br\>
   * Para criar uma política, selecione **Criar Nova**.

     ![Backup de máquinas virtuais](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Para obter instruções sobre como criar uma política de backup, consulte [Definindo uma política de backup](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Ao gerenciar políticas de backup, certifique-se de seguir as [práticas recomendadas](backup-azure-vms-introduction.md#best-practices) para um desempenho de backup ideal
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>Backup sob demanda de uma máquina virtual
Você pode obter um backup sob demanda de uma máquina virtual quando ela estiver configurada para proteção. Se o backup inicial está pendente, o backup sob demanda cria uma cópia completa da máquina virtual no cofre dos Serviços de Recuperação. Se o backup inicial for concluído, um backup sob demanda enviará apenas as alterações do instantâneo anterior para o cofre dos Serviços de Recuperação. Ou seja, os backups subsequentes serão sempre incrementais.

> [!NOTE]
> O intervalo de retenção para um backup sob demanda é o valor de retenção especificado para o ponto de backup Diário na política. Se nenhum ponto de backup Diário for selecionado, o ponto de backup Semanal será usado.
>
>

Para inicializar um backup sob demanda de uma máquina virtual:

* No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Fazer backup agora**.

    ![Botão Fazer backup agora](./media/backup-azure-manage-vms/backup-now-button.png)

    O portal verificará você deseja iniciar um trabalho de backup sob demanda. Clique em **Sim** para iniciar o trabalho de backup.

    ![Botão Fazer backup agora](./media/backup-azure-manage-vms/backup-now-check.png)

    O trabalho de backup cria um ponto de recuperação. O intervalo de retenção do ponto de recuperação é o mesmo especificado na política associada à máquina virtual. Para acompanhar o progresso do trabalho, no painel do cofre, clique no bloco **Trabalhos de Backup** .  

## <a name="stop-protecting-virtual-machines"></a>Interromper a proteção de máquinas virtuais
Se você optar por interromper a proteção de uma máquina virtual, será perguntado se deseja manter os pontos de recuperação. Há duas maneiras de interromper a proteção das máquinas virtuais:

* parar todos os trabalhos de backup futuros e excluir todos os pontos de recuperação ou
* parar todos os trabalhos de backup futuros, mas deixar os pontos de recuperação  <br/>

Há um custo associado a deixar os pontos de recuperação no armazenamento. No entanto, a vantagem de deixar os pontos de recuperação é que você pode restaurar a máquina virtual mais tarde, se desejado. Para obter informações sobre o custo de deixar os pontos de recuperação, confira os [detalhes de preços](https://azure.microsoft.com/pricing/details/backup/). Se você optar por excluir todos os pontos de recuperação, não poderá restaurar a máquina virtual.

Para interromper a proteção para uma máquina virtual:

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Interromper backup**.

    ![Botão Interromper backup](./media/backup-azure-manage-vms/stop-backup-button.png)

    A folha Interromper Backup é aberta.

    ![Folha Interromper backup](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. Na folha **Interromper Backup** , escolha se deseja manter ou excluir os dados do backup. A caixa de informações fornece detalhes sobre sua escolha.

    ![Parar a proteção](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Se você escolheu manter os dados do backup, vá para a etapa 4. Se escolheu excluir os dados do backup, confirme se deseja interromper os trabalhos de backup e excluir os pontos de recuperação - digite o nome do item.

    ![Interromper verificação](./media/backup-azure-manage-vms/item-verification-box.png)

    Se você não tiver certeza do nome do item, passe o mouse sobre o ponto de exclamação para exibir o nome. Além disso, o nome do item está sob **Interromper Backup** na parte superior da folha.
4. Como opção, forneça um **Motivo** ou **Comentário**.
5. Para interromper o trabalho de backup do item atual, clique no ![botão Parar Backup](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Uma mensagem de notificação permite que você conheça os trabalhos de backup que foram interrompidos.

    ![Confirmar a interrupção da proteção](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>Retomar a proteção de uma máquina virtual
Se a opção **Reter Dados do Backup** foi selecionada quando a proteção da máquina virtual foi interrompida, será possível retomar a proteção. Se a opção **Excluir Dados do Backup** foi escolhida, então, a proteção da máquina virtual não poderá retomar.

Retomar a proteção da máquina virtual

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Retomar backup**.

    ![Retomar proteção](./media/backup-azure-manage-vms/resume-backup-button.png)

    A folha Política de Backup será aberta.

   > [!NOTE]
   > Ao proteger novamente a máquina virtual, você pode escolher uma política diferente da política com a qual a máquina virtual foi inicialmente protegida.
   >
   >
2. Siga as etapas em [Gerenciar políticas de backup](backup-azure-manage-vms.md#manage-backup-policies), para atribuir a política da máquina virtual.

    Depois da política de backup ser aplicada à máquina virtual, você vê a mensagem a seguir.

    ![VM protegida com êxito](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Excluir Dados do Backup
Você pode excluir os dados de backup associados a uma máquina virtual durante o trabalho **Interromper backup** a qualquer momento depois que o trabalho de backup é concluído. Pode até mesmo ser benéfico aguardar dias ou semanas antes de excluir os pontos de recuperação. Ao contrário de restaurar os pontos de recuperação, ao excluir os dados do backup, você não pode escolher os pontos de recuperação específicos para excluir. Se você escolher excluir os dados de backup, apagará todos os pontos de recuperação associados ao item.

O procedimento a seguir pressupõe que o trabalho de Backup da máquina virtual foi interrompido ou desabilitado. Depois que o trabalho de Backup é desabilitado, as opções **Retomar backup** e **Excluir backup** ficam disponíveis no painel de itens do cofre.

![Botões para retomar e excluir](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Para excluir os dados de backup em uma máquina virtual com o *Backup desabilitado*:

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Excluir backup**.

    ![Tipo de VM](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    A folha **Excluir Dados do Backup** será aberta.

    ![Tipo de VM](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Você deve digitar o nome do item para confirmar que deseja excluir os pontos de recuperação.

    ![Interromper verificação](./media/backup-azure-manage-vms/item-verification-box.png)

    Se você não tiver certeza do nome do item, passe o mouse sobre o ponto de exclamação para exibir o nome. Além disso, o nome do item está sob **Excluir Dados do Backup** na parte superior da folha.
3. Como opção, forneça um **Motivo** ou **Comentário**.
4. Para excluir os dados do backup do item atual, clique no ![Botão Parar Backup](./media/backup-azure-manage-vms/delete-button.png)

    Uma mensagem de notificação permite que você conheça os dados do backup que foram excluídos.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre como recriar uma máquina virtual a partir de um ponto de recuperação, verifique [Restaurar VMs do Azure](backup-azure-restore-vms.md). Se você precisar de informações sobre como proteger suas máquinas virtuais, consulte [Primeira consideração: fazer backup das VMs em um cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md). Para obter informações sobre o monitoramento de eventos, confira [Monitorar alertas para backups de máquina virtual do Azure](backup-azure-monitor-vms.md).
