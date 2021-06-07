---
title: Restaurar Managed Disks do Azure
description: Saiba como restaurar Managed Disks do Azure do portal do Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 94adc8512987b50a8df07d295215ffcff873162f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108580"
---
# <a name="restore-azure-managed-disks"></a>Restaurar Managed Disks do Azure

Este artigo explica como restaurar [Managed disks do Azure](../virtual-machines/managed-disks-overview.md) de um ponto de restauração criado pelo backup do Azure.

Atualmente, a opção de recuperação de Original-Location (OLR) de restauração substituindo o disco de origem existente do qual os backups foram feitos não tem suporte. Você pode restaurar de um ponto de recuperação para criar um novo disco no mesmo grupo de recursos do disco de origem do qual os backups foram feitos ou em qualquer outro grupo de recursos. Isso é conhecido como ALR (recuperação de Alternate-Location) e isso ajuda a manter o disco de origem e o disco restaurado (novo).

Neste artigo, você aprenderá a:

- Restaurar para criar um novo disco

- Acompanhar o status da operação de restauração

## <a name="restore-to-create-a-new-disk"></a>Restaurar para criar um novo disco

O cofre de backup usa identidade gerenciada para acessar outros recursos do Azure. Para restaurar do backup, a identidade gerenciada do cofre de backup requer um conjunto de permissões no grupo de recursos em que o disco deve ser restaurado.

O cofre de backup usa uma identidade gerenciada atribuída pelo sistema, que é restrita a um por recurso e está vinculada ao ciclo de vida deste recurso. Você pode conceder permissões para a identidade gerenciada usando o controle de acesso baseado em função do Azure (RBAC do Azure). A identidade gerenciada é uma entidade de serviço de um tipo especial que pode ser usada apenas com recursos do Azure. Saiba mais sobre [identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md).

Os pré-requisitos a seguir são necessários para executar uma operação de restauração:

1. Atribua a função de **operador de restauração de disco** à identidade gerenciada do cofre de backup no grupo de recursos em que o disco será restaurado pelo serviço de backup do Azure.

    >[!NOTE]
    > Você pode escolher o mesmo grupo de recursos do disco de origem de onde os backups são feitos ou para qualquer outro grupo de recursos na mesma ou em uma assinatura diferente.

    1. Vá para o grupo de recursos no qual o disco deve ser restaurado. Por exemplo, o grupo de recursos é *TargetRG*.

    1. Vá para **controle de acesso (iam)** e selecione **Adicionar atribuições de função**

    1. No painel de contexto à direita, selecione **operador de restauração de disco** na lista suspensa **função** . Selecione a identidade gerenciada do cofre de backup e **salve**.

        >[!TIP]
        >Digite o nome do cofre de backup para selecionar a identidade gerenciada do cofre.

        ![Selecionar função de operador de restauração de disco](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Verifique se a identidade gerenciada do cofre de backup tem o conjunto correto de atribuições de função no grupo de recursos em que o disco será restaurado.

    1. Vá para **cofre de backup-> identidade** e selecione **atribuições de função do Azure**

        ![Selecionar atribuições de função do Azure](./media/restore-managed-disks/azure-role-assignments.png)

    1. Verifique se a função, o nome do recurso e o tipo de recurso aparecem corretamente.

        ![Verificar função, nome do recurso e tipo de recurso](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Embora as atribuições de função sejam refletidas corretamente no portal, pode levar aproximadamente 15 minutos para que a permissão seja aplicada na identidade gerenciada do cofre de backup.
    >
    >Durante os backups agendados ou uma operação de backup sob demanda, o backup do Azure armazena os instantâneos incrementais de disco no grupo de recursos de instantâneo fornecido durante a configuração do backup do disco. O backup do Azure usa esses instantâneos incrementais durante a operação de restauração. Se os instantâneos forem excluídos ou movidos do grupo de recursos de instantâneo ou se as atribuições de função do cofre de backup forem revogadas no grupo de recursos de instantâneo, a operação de restauração falhará.

1. Se o disco a ser restaurado for criptografado com [chaves gerenciadas pelo cliente (CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) ou usando a [criptografia dupla usando chaves gerenciadas por plataforma e chaves gerenciadas pelo cliente](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md), atribua a permissão de função de **leitor** à identidade gerenciada do cofre de backup no recurso de **conjunto de criptografia de disco** .

Depois que os pré-requisitos forem atendidos, siga estas etapas para executar a operação de restauração.

1. Na [portal do Azure](https://portal.azure.com/), vá para o **centro de backup**. Selecione **instâncias de backup** na seção **gerenciar** . Na lista de instâncias de backup, selecione a instância de backup em disco para a qual você deseja executar a operação de restauração.

    ![Lista de instâncias de backup](./media/restore-managed-disks/backup-instances.png)

    Como alternativa, você pode executar essa operação no cofre de backup usado para configurar o backup do disco.

1. Na tela **instância de backup** , selecione o ponto de restauração que você deseja usar para executar a operação de restauração e selecione **restaurar**.

    ![Folha Selecionar ponto de restauração](./media/restore-managed-disks/select-restore-point.png)

1. No fluxo de trabalho de **restauração** , examine os **conceitos básicos** e selecione informações da guia **ponto de recuperação** e selecione **Avançar: restaurar parâmetros**.

    ![Examinar noções básicas e selecionar informações de ponto de recuperação](./media/restore-managed-disks/review-information.png)

1. Na guia **restaurar parâmetros** , selecione a **assinatura de destino** e o grupo de **recursos de destino** para onde você deseja restaurar o backup. Forneça o nome do disco a ser restaurado. Selecione **Avançar: revisar + restaurar**.

    ![Restaurar parâmetros](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Os discos cujo backup foi feito pelo backup do Azure usando a solução de backup em disco também podem ser submetidos a backup pelo backup do Azure usando a solução de backup de VM do Azure com o cofre dos serviços de recuperação. Se você tiver configurado a proteção da VM do Azure à qual esse disco está anexado, também poderá usar a operação de restauração de VM do Azure. Você pode optar por restaurar a VM, ou discos e arquivos ou pastas do ponto de recuperação da instância de backup de VM do Azure correspondente. Para obter mais informações, consulte [backup de VM do Azure](./about-azure-vm-restore.md).

1. Depois que a validação for bem-sucedida, selecione **restaurar** para iniciar a operação de restauração.

    ![Iniciar operação de restauração](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > A validação pode levar alguns minutos para ser concluída antes que você possa disparar a operação de restauração. A validação poderá falhar se:
    >
    > - um disco com o mesmo nome fornecido no **nome do disco restaurado** já existe no **grupo de recursos de destino**
    > - a identidade gerenciada do cofre de backup não tem atribuições de função válidas no **grupo de recursos de destino**
    > - as atribuições da função de identidade gerenciada do cofre de backup são revogadas no **grupo de recursos de instantâneo** em que os instantâneos incrementais são armazenados
    > - Se instantâneos incrementais forem excluídos ou movidos do grupo de recursos de instantâneo

A restauração criará um novo disco do ponto de recuperação selecionado no grupo de recursos de destino que foi fornecido durante a operação de restauração. Para usar o disco restaurado em uma máquina virtual existente, você precisará executar mais etapas:

- Se o disco restaurado for um disco de dados, você poderá anexar um disco existente a uma máquina virtual. Se o disco restaurado for um disco do sistema operacional, você poderá trocar o disco do sistema operacional de uma máquina virtual da portal do Azure no menu painel da **máquina virtual** – > **discos** na seção **configurações** .

    ![Trocar discos do sistema operacional](./media/restore-managed-disks/swap-os-disks.png)

- Para máquinas virtuais do Windows, se o disco restaurado for um disco de dados, siga as instruções para [desanexar o disco de dados original](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) da máquina virtual. Em seguida, [anexe o disco restaurado](../virtual-machines/windows/attach-managed-disk-portal.md) à máquina virtual. Siga as instruções para [trocar o disco do sistema operacional](../virtual-machines/windows/os-disk-swap.md) da máquina virtual pelo disco restaurado.

- Para máquinas virtuais do Linux, se o disco restaurado for um disco de dados, siga as instruções para [desanexar o disco de dados original](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) da máquina virtual. Em seguida, [anexe o disco restaurado](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) à máquina virtual. Siga as instruções para [trocar o disco do sistema operacional](../virtual-machines/linux/os-disk-swap.md) da máquina virtual pelo disco restaurado.

É recomendável revogar a atribuição de função do **operador de restauração de disco** da identidade gerenciada do cofre de backup no grupo de recursos de **destino** após a conclusão bem-sucedida da operação de restauração.

## <a name="track-a-restore-operation"></a>Rastrear uma operação de restauração

Após disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhamento. O Backup do Azure exibe notificações sobre o trabalho no portal. Para exibir o progresso do trabalho de restauração:

1. Vá para a tela de **instância de backup** . Ele mostra o painel trabalhos com a operação e o status dos últimos sete dias.

    ![Painel de trabalhos](./media/restore-managed-disks/jobs-dashboard.png)

1. Para exibir o status da operação de restauração, selecione **Exibir tudo** para mostrar os trabalhos em andamento e passados desta instância de backup.

    ![Selecionar Exibir tudo](./media/restore-managed-disks/view-all.png)

1. Examine a lista de trabalhos de backup e restauração e seu status. Selecione um trabalho na lista de trabalhos para exibir detalhes do trabalho.

    ![Lista de trabalhos](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Próximas etapas

- [FAQ do backup em disco do Azure](disk-backup-faq.md)