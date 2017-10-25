---
title: "Backup do Azure: restaurar máquinas virtuais usando o portal do Azure | Microsoft Docs"
description: "Restaure uma máquina virtual do Azure de um ponto de recuperação usando o portal do Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "restaurar o backup; como restaurar; ponto de recuperação;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: b659d5dc894afd2beef529c6b4f736e888b4540e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Usar o portal do Azure para restaurar máquinas virtuais
> [!div class="op_single_selector"]
> * [Restaurar VMs no portal clássico](backup-azure-restore-vms.md)
> * [Restaurar VMs no portal do Azure](backup-azure-arm-restore-vms.md)
>
>

Proteja seus dados criando instantâneos de dados em intervalos definidos. Esses instantâneos são conhecidos como pontos de recuperação e são armazenados nos cofres dos Serviços de Recuperação. Se for necessário reparar ou recriar uma VM (máquina virtual), você poderá restaurá-la de qualquer um dos pontos de recuperação salvos. Quando restaura um ponto de recuperação, você pode:

* Criar uma nova VM, que é uma representação pontual da VM copiada em backup.
* Restaurar discos e usar o modelo que acompanha o processo para personalizar a VM restaurada ou fazer uma recuperação de arquivos individuais. 

Este artigo explica como restaurar uma VM em uma nova VM, ou restaurar todos os discos de backup. Para a recuperação de arquivos individuais, consulte [Recuperar arquivos do backup de VM do Azure](backup-azure-restore-files-from-vm.md).

![Três maneiras de restaurar do backup de VM](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [o Azure Resource Manager e o clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo fornece as informações e os procedimentos usados para restaurar VMs implantadas usando o modelo do Resource Manager.
>
>

A restauração de uma VM ou de todos os discos do backup de VM envolve duas etapas:

* Selecionar um ponto para restauração.
* Selecionar o tipo de restauração, criar uma nova VM ou restaurar discos e especificar os parâmetros necessários. 

## <a name="select-a-restore-point-for-restore"></a>Selecionar um ponto de restauração para restauração
1. Entre no [Portal do Azure](http://portal.azure.com/).

2. No menu do Azure, selecione **Procurar**. Na lista de serviços, digite **Serviços de Recuperação**. A lista de serviços é ajustada de acordo com o que você digitar. Quando você vir a opção **cofres de Serviços de Recuperação**, selecione-a.

    ![Cofre dos Serviços de Recuperação](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    A lista de cofres na assinatura é exibida.

    ![Listar cofres de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. Na lista, selecione o cofre associado à VM que deseja restaurar. Ao selecionar o cofre, o painel é aberto.

    ![Cofre dos Serviços de Recuperação selecionado](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. No painel do cofre, no bloco **Itens de Backup**, selecione **Máquinas Virtuais do Azure**.

    ![Painel do cofre](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    A folha **Itens de Backup** é aberta e exibe a lista de VMs do Azure.

    ![Lista de VMs no cofre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. Na lista, selecione uma VM para abrir o painel. O painel da VM é aberto na área de monitoramento, que contém o bloco **Pontos de restauração**.

    ![Pontos de restauração](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. No menu do painel da VM, selecione **Restaurar**.

    ![Botão Restaurar](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    A folha **Restaurar** é aberta.

    ![Folha Restaurar](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. Na folha **Restaurar**, selecione **Ponto de restauração**. A folha **Selecionar ponto de restauração** é aberta.

    ![Folha Selecionar ponto de restauração](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Por padrão, a caixa de diálogo exibe todos os pontos de restauração dos últimos 30 dias. Use o **Filtro** alterar o intervalo de tempo dos pontos de restauração exibidos. Por padrão, são exibidos pontos de restauração de todas as consistências. Modifique o filtro **Todos os pontos de restauração** para selecionar uma consistência de ponto de restauração específica. Para saber mais sobre cada tipo de ponto de restauração, consulte [Consistência de dados](backup-azure-vms-introduction.md#data-consistency).

    Opções de consistência de ponto de restauração:

     * Pontos de restauração com controle de falhas
     * Pontos de restauração consistentes do aplicativo
     * Pontos de restauração consistentes de sistema de arquivos
     * Todos os pontos de restauração

8. Escolha um ponto de restauração e clique em **OK**.

    ![Opções de ponto de restauração](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    A folha **Restaurar** mostra que o ponto de restauração foi definido.

9. Se ainda não estiver nela, vá até a folha **Restaurar**. Verifique se um [ponto de restauração está selecionado](#select-restore-point-for-restore) e selecione **Configuração de restauração**. A folha **Configuração de restauração** é aberta.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauração de VM
Após selecionar o ponto de restauração, escolha uma configuração de restauração de VM. Para configurar a VM restaurada, você pode usar o portal do Azure ou o PowerShell.

1. Se ainda não estiver nela, vá até a folha **Restaurar**. Verifique se um [ponto de restauração está selecionado](#select-restore-point-for-restore) e selecione **Configuração de restauração**. A folha **Configuração de restauração** é aberta.

    ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. Na folha **Restaurar configuração**, você tem duas opções:

   * **Criar máquina virtual**

   * **Restaurar discos**

O portal fornece uma opção de **Criação Rápida** para a VM restaurada. Para personalizar a configuração da VM ou os nomes dos recursos criados como parte da criação de uma nova opção de VM, use o PowerShell ou o portal para restaurar os discos de backup. Use comandos do PowerShell para anexá-los à sua opção de configuração da VM. Ou você pode usar o modelo fornecido com os discos restaurados para personalizar a VM restaurada. Para obter informações sobre como restaurar uma VM que tem várias NICs ou que está sob um balanceador de carga, consulte [Restaurar uma VM com configurações de rede especiais](#restore-a vm-with-special-network-configurations). Se sua VM do Windows usar o [licenciamento de HUB](../virtual-machines/windows/hybrid-use-benefit-licensing.md), restaure os discos e use o PowerShell/modelo conforme especificado neste artigo para criar a VM. Não deixe de especificar o **Tipo de Licença** como "Windows_Server" ao criar a VM para tirar proveito dos benefícios do HUB na VM restaurada. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Criar uma nova VM de um ponto de restauração
1. Se ainda não tiver feito isto, [selecione um ponto de restauração](#restore-a vm-with-special-network-configurations) antes de começar a criar uma nova VM de um ponto de restauração. Após selecionar um ponto de restauração, na folha **Configuração de restauração**, insira ou selecione valores para cada um dos campos a seguir:

    a. **Tipo de Restauração**. Crie uma máquina virtual.

    b. **Nome da máquina virtual**. Forneça um nome para a VM. O nome deve ser exclusivo para o grupo de recursos (para uma VM implantada pelo Azure Resource Manager) ou serviço de nuvem (para uma VM clássica). Não será possível substituir a VM se ela já existir na assinatura.

    c. **Grupo de recursos**. Use um grupo de recursos existente ou crie um novo. Se estiver restaurando uma VM clássica, use este campo para especificar o nome de um novo serviço de nuvem. Se estiver criando um novo grupo de recursos/serviço de nuvem, o nome deverá ser exclusivo globalmente. Normalmente, o nome do serviço de nuvem está associado a uma URL voltada para o público, por exemplo: [cloudservice].cloudapp.net. Se você tentar usar um nome de serviço de nuvem/grupo de recursos de nuvem que já esteja sendo usado, o Azure atribuirá ao serviço de nuvem/grupo de recursos o mesmo nome que o da VM. O Azure exibe serviços de nuvem/grupos de recursos e VMs não associadas a nenhum grupo de afinidades. Para obter mais informações, consulte [Como migrar de grupos de afinidades para uma rede virtual regional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Rede virtual**. Selecione a rede virtual quando criar a VM. O campo fornece todas as redes virtuais associadas à assinatura. O grupo de recursos da VM é exibido entre parênteses.

    e. **Sub-rede**. Se a rede virtual tiver sub-redes, a primeira sub-rede será selecionada por padrão. Se houver sub-redes adicionais, selecione a sub-rede desejada.

    f. **Conta de armazenamento**. Este menu lista as contas de armazenamento no mesmo local que o cofre dos Serviços de Recuperação. Não há suporte para contas de armazenamento com redundância de zona. Se não houver nenhuma conta de armazenamento no mesmo local que o cofre dos Serviços de Recuperação, você deverá criar uma antes de iniciar a operação de restauração. O tipo de replicação da conta de armazenamento é exibido entre parênteses.

    ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Se restaurar uma VM implantada pelo Resource Manager, você deverá identificar uma rede virtual. A rede virtual é opcional para uma VM clássica.

    > * Se você restaurar VMs com Managed Disks, certifique-se de que a conta de armazenamento selecionada não esteja habilitada para a Criptografia do Serviço de Armazenamento do Azure em seu tempo de vida.

    > * Com base no tipo de armazenamento da conta de armazenamento selecionada (premium ou standard), todos os discos restaurados serão discos premium ou standard. Atualmente, não há suporte para um modo misto de discos durante a restauração.
    >
    >

2. Na folha **Configuração de restauração**, clique em **OK** para finalizar a configuração da restauração. Na folha **Restaurar**, selecione **Restaurar** para disparar a operação de restauração.

## <a name="restore-backed-up-disks"></a>Restaurar discos com backup
Para personalizar a VM que você gostaria de criar de discos com backup diferentes dos que estão presentes na folha **Configuração de restauração**, selecione **Restaurar discos** como o valor para **Tipo de Restauração**. Essa opção solicita uma conta de armazenamento na qual discos de backups devem ser copiados. Ao escolher uma conta de armazenamento, selecione uma conta que compartilhe o mesmo local que o cofre dos Serviços de Recuperação. Não há suporte para contas de armazenamento com redundância de zona. Se não houver nenhuma conta de armazenamento no mesmo local que o cofre dos Serviços de Recuperação, você deverá criar uma antes de iniciar a operação de restauração. O tipo de replicação da conta de armazenamento é exibido entre parênteses.

Depois que a operação de restauração for concluída, você pode:

* [Usar o modelo para personalizar a VM restaurada](#use-templates-to-customize-restore-vm)
* [Usar os discos restaurados para anexar a uma VM existente](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Criar uma nova VM usando o PowerShell de discos restaurados](./backup-azure-vms-automation.md#restore-an-azure-vm)

Na folha **Configuração de restauração**, clique em **OK** para finalizar a configuração da restauração. Na folha **Restaurar**, selecione **Restaurar** para disparar a operação de restauração.

![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Acompanhar a operação de restauração
Após você disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhar a operação de restauração. O serviço de backup também cria e exibe temporariamente a notificação na área de **Notificações** do portal. Se não vir a notificação, selecione o símbolo **Notificações** para exibir suas notificações.

![Restauração disparada](./media/backup-azure-arm-restore-vms/restore-notification.png)

Para exibir a operação durante seu processamento ou para exibi-la quando for concluída, abra a lista de **Trabalhos de backup**.

1. No menu do Azure, clique em **Procurar** e, na lista de serviços, digite **Serviços de Recuperação**. A lista de serviços é ajustada de acordo com o que você digitar. Quando você vir a opção **cofres de Serviços de Recuperação**, selecione-a.

    ![Abrir cofre de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    A lista de cofres na assinatura é exibida.

    ![Listar cofres de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. Na lista, selecione o cofre associado à VM que restaurada. Ao selecionar o cofre, o painel é aberto.

3. No painel do cofre, no bloco **Trabalhos de Backup**, selecione **Máquinas virtuais do Azure** para exibir os trabalhos associados ao cofre.

    ![Painel do cofre](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    A folha **Trabalhos de backup** é aberta e exibe a lista de trabalhos.

    ![Lista de VMs em um cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Usar modelos para personalizar uma VM restaurada
Após a [conclusão da operação de restauração de discos](#Track-the-restore-operation), use o modelo que foi gerado como parte da operação de restauração para criar uma nova VM com uma configuração diferente da configuração de backup. Você também pode usar isso para personalizar os nomes dos recursos que foram criados durante o processo de criação de uma nova VM de um ponto de restauração. 

> [!NOTE]
> Os modelos são adicionados como parte da restauração de discos para pontos de recuperação feitos após 1º de março de 2017. Eles são aplicáveis a VMs de disco não gerenciado. O suporte para VMs de disco gerenciado estará disponível em versões futuras. 
>
>

Para obter o modelo que foi gerado como parte da opção de discos de restauração:

1. Acesse os detalhes do trabalho de restauração correspondente ao trabalho.

2. Na tela **Detalhes do Trabalho de Restauração**, selecione **Implantar Modelo** para iniciar a implantação de modelo. 

     ![Detalhamento do trabalho de restauração](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
3. Na folha **Implantar modelo** para a implantação personalizada, use a implantação de modelo para [editar e implantar o modelo](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) ou acrescentar mais personalizações [criando um modelo](../azure-resource-manager/resource-group-authoring-templates.md) antes de implantar. 

   ![Carregar a implantação de modelo](./media/backup-azure-arm-restore-vms/loading-template.png)
   
4. Depois de inserir os valores necessários, aceite os **Termos e Condições** e selecione **Comprar**.

   ![Enviar a implantação de modelo](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Etapas pós-restauração
* Se você usar uma distribuição Linux baseada em inicialização da nuvem, como o Ubuntu, a senha será bloqueada após a restauração por motivos de segurança. Use uma extensão VMAccess na VM restaurada para [redefinir a senha](../virtual-machines/linux/classic/reset-access.md). Recomendamos o uso de chaves SSH nessas distribuições para evitar a redefinição da senha após a restauração.
* Extensões presentes durante a configuração do backup serão instaladas, mas não serão habilitadas. Se encontrar um problema, reinstale as extensões. 
* Se, após a restauração, a VM de backup tiver IP estático, a VM restaurada terá um IP dinâmico para evitar conflitos ao criar uma VM restaurada. Saiba mais sobre como você pode [adicionar um IP estático a uma VM restaurada](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Uma VM restaurada não tem um valor de disponibilidade definido. Recomendamos o uso da opção de discos de restauração para [adicionar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) ao criar uma VM do PowerShell ou de modelos usando discos restaurados. 


## <a name="backup-for-restored-vms"></a>Backup de VMs restauradas
Se você tiver restaurado uma VM no mesmo grupo de recursos e com o mesmo nome que a VM do backup original, o backup continuará na VM após a restauração. Se você tiver restaurado a VM para outro grupo de recursos ou tiver especificado um nome diferente para a VM restaurada, a VM será tratada como se fosse uma nova VM. Você precisa configurar o backup para a VM restaurada.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Restaurar uma VM durante um desastre de datacenter do Azure
O Backup do Azure permite restaurar backups de VMs para o data center emparelhado caso ocorra um desastre no datacenter principal em que as VMs estão sendo executadas e você tenha configurado o cofre de backup para ser redundante geograficamente. Durante esses cenários, selecione uma conta de armazenamento que esteja presente em um datacenter emparelhado. O restante do processo de restauração permanece o mesmo. O Backup usa o serviço de computação da área geográfica emparelhada para criar a VM restaurada. Para obter mais informações, consulte [Resiliência de datacenter do Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Restaurar VMs do controlador de domínio
O backup de VMs do DC (controlador de domínio) é um cenário com suporte no Backup. No entanto, é necessário ter cuidado durante o processo de restauração. O processo de restauração correto depende da estrutura do domínio. No caso mais simples, você tem um único DC em um único domínio. Mais comumente para cargas de produção, você terá um único domínio com vários DCs, talvez com alguns DCs locais. Por fim, você pode ter uma floresta com vários domínios. 

Da perspectiva do Active Directory, a VM do Azure é como qualquer outra VM em um hipervisor moderno com suporte. A principal diferença com hipervisores locais é que nenhum console da VM está disponível no Azure. Um console é necessário para determinados cenários, como a recuperação usando um backup do tipo BMR (recuperação bare-metal). No entanto, a restauração de VM do cofre de backup é uma substituição completa para a BMR. O DSRM (Modo de Restauração dos Serviços de Diretório) também está disponível, portanto, todos os cenários de recuperação do Active Directory são viáveis. Para obter mais informações, consulte as [Considerações de backup e restauração para controladores de domínio virtualizados](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) e [Planejamento para recuperação de floresta do Active Directory](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Controlador de domínio único em um único domínio
A VM pode ser restaurada (como qualquer outra VM) do portal do Azure ou usando o PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Vários controladores de domínio em um único domínio
Quando outros controladores de domínio do mesmo domínio podem ser acessados pela rede, o controlador de domínio pode ser restaurado como uma VM. Se for o último DC restante no domínio ou se uma recuperação em uma rede isolada for executada, um procedimento de recuperação de floresta deverá ser seguido.

### <a name="multiple-domains-in-one-forest"></a>Vários domínios em uma floresta
Quando outros controladores de domínio do mesmo domínio podem ser acessados pela rede, o controlador de domínio pode ser restaurado como uma VM. Em todos os outros casos, é recomendável uma recuperação de floresta.

## <a name="restore-vms-with-special-network-configurations"></a>Restaurar VMs com configurações de rede especiais
É possível fazer backup e restaurar VMs com as seguintes configurações de rede especiais. No entanto, essas configurações exigem algumas considerações especiais ao percorrer o processo de restauração:

* VMs sob balanceadores de carga (internos e externos)
* VMs com vários IPs reservados
* VMs com vários NICs

> [!IMPORTANT]
> Ao criar a configuração de rede especial para VMs, você precisa usar o PowerShell para criar VMs dos discos restaurados.
>
>

Para recriar completamente as VMs após restaurar para o disco, siga estas etapas:

1. Restaure os discos de um cofre dos Serviços de Recuperação usando o [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

2. Crie a configuração da VM necessária para o balanceador de carga/múltiplos NICs/ múltiplos IPs reservados usando os cmdlets do PowerShell. Use-a para criar a VM com a configuração desejada:

   a. Criar uma VM no serviço de nuvem com um [balanceador de carga interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Criar uma VM para se conectar a um [balanceador de carga voltado para a Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/).

   c. Criar uma VM com [diversas NICs](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Criar uma VM com [vários IPs reservados](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Próximas etapas
Agora que você pode restaurar suas VMs, consulte o artigo de solução de problemas para obter informações sobre erros comuns com VMs. Além disso, confira o artigo sobre como gerenciar tarefas com suas VMs.

* [Solucionar erros](backup-azure-vms-troubleshoot.md#restore)
* [Gerenciar máquinas virtuais](backup-azure-manage-vms.md)
