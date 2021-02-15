---
title: Solucionar problemas de uma VM do Windows no portal do Azure | Microsoft Docs
description: Saiba como solucionar problemas de máquina virtual do Windows no Azure conectando o disco do sistema operacional a uma VM de recuperação por meio do portal do Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735221"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>Solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação por meio do portal do Azure
Se sua VM (máquina virtual) do Windows no Azure encontrar um erro de inicialização ou de disco, talvez seja necessário executar as etapas de solução de problemas no disco rígido virtual (VHD). Um exemplo comum é uma atualização de aplicativo com falha que impede que a VM seja iniciada com êxito. Este artigo fornece detalhes sobre como usar o portal do Azure para conectar seu disco rígido virtual a outra VM do Windows para corrigir erros e, em seguida, recriar a VM original. 

## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Pare a VM afetada.
1. Crie um instantâneo para o disco do sistema operacional da VM.
1. Crie um disco rígido virtual a partir do instantâneo.
1. Anexe e monte o disco rígido virtual em outra VM Windows para fins de solução de problemas.
1. Conecte-se à VM de solução de problemas. Edite arquivos ou execute qualquer ferramenta para corrigir problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de solução de problemas.
1. Troque o disco do sistema operacional da VM.

> [!NOTE]
> Este artigo não se aplica a VMs que têm discos não gerenciados.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tirar um instantâneo do disco do sistema operacional
Um instantâneo é uma cópia completa e somente leitura de um disco rígido virtual. Recomendamos que você desligue corretamente a VM antes de tirar um instantâneo, para limpar todos os processos em andamento. Para tirar um instantâneo de um disco do sistema operacional, siga estas etapas:

1. Acesse o [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e, em seguida, selecione a VM que tem o problema.
1. No painel esquerdo, selecione **discos** e, em seguida, selecione o nome do disco do sistema operacional.
    ![Captura de tela que mostra o nome do disco do sistema operacional em configurações de disco.](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na página **visão geral** do disco do sistema operacional, selecione **criar instantâneo**.
1. Crie um instantâneo no mesmo local que o disco do sistema operacional.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo
Para criar um disco a partir do instantâneo, siga estas etapas:

1. Selecione **Cloud Shell** na portal do Azure.

    ![Captura de tela que mostra o botão para abrir Azure Cloud Shell.](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Execute os comandos do PowerShell a seguir para criar um disco gerenciado a partir do instantâneo. Substitua os nomes de exemplo pelos nomes apropriados.

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Se os comandos forem executados com êxito, você verá o novo disco no grupo de recursos que você forneceu.

## <a name="attach-the-disk-to-another-vm"></a>Anexar o disco a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Depois de anexar o disco à VM de solução de problemas, você pode procurar e editar o conteúdo do disco. Esse processo permite que você corrija quaisquer erros de configuração ou examine arquivos adicionais de log do sistema ou do aplicativo. Para anexar o disco a outra VM, siga estas etapas:

1. Selecione o grupo de recursos no portal e, em seguida, selecione a VM de solução de problemas. Selecione **discos**  >  **Editar**  >  **adicionar disco de dados**.

    ![Captura de tela que mostra seleções para anexar um disco existente no Portal.](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **discos de dados** , selecione o disco do sistema operacional da VM que você identificou. Se você não vir o disco do sistema operacional, verifique se a VM de solução de problemas e o disco do sistema operacional estão na mesma região (local). 
3. Selecione **salvar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Montar o disco de dados anexado para a VM

1. Abra uma conexão de Área de Trabalho Remota para a VM de solução de problemas. 
2. Na VM de solução de problemas, abra **Gerenciador do servidor** e, em seguida, selecione **serviços de arquivo e armazenamento**. 

    ![Captura de tela que mostra a seleção de serviços de arquivo e armazenamento no Gerenciador do Servidor.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. O disco de dados é detectado e anexado automaticamente. Para ver uma lista dos discos conectados, selecione **Discos**. É possível selecionar o disco de dados para exibir informações de volume, incluindo a letra da unidade. O exemplo a seguir mostra o disco de dados anexado e usando a unidade **F**.

    ![Captura de tela que mostra informações de disco e volume anexados em Gerenciador do Servidor.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver todos os erros, continue com as etapas a seguir.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Desanexe o disco rígido virtual existente da VM de solução de problemas. Você não pode usar seu disco rígido virtual com qualquer outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão de Área de Trabalho Remota para sua VM, abra **Gerenciador do servidor** e, em seguida, selecione **serviços de arquivo e armazenamento**.

    ![Captura de tela que mostra a seleção de serviços de arquivo e armazenamento no Gerenciador do Servidor.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selecione **discos**, clique com o botão direito do mouse no disco de dados e selecione **colocar offline**.

    ![Captura de tela que mostra a configuração do disco de dados como offline no Gerenciador do Servidor.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Desanexe o disco rígido virtual da VM. Selecione sua VM no portal do Azure e, em seguida, selecione **discos**. 
4. Selecione **Editar**, selecione o disco do sistema operacional que você anexou e, em seguida, selecione **excluir**.

    ![Captura de tela que mostra seleções para desanexar um disco rígido virtual existente.](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Antes de continuar, aguarde até que o disco de dados seja excluído com êxito na VM.

## <a name="swap-the-os-disk-for-the-vm"></a>Trocar o disco do sistema operacional da VM

Portal do Azure agora dá suporte à alteração do disco do sistema operacional da VM. Para fazer isso, execute estas etapas:

1. Acesse o [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e, em seguida, selecione a VM que tem o problema.
1. No painel esquerdo, selecione **discos** e, em seguida, selecione **trocar disco do sistema operacional**.
   
    ![Captura de tela que mostra o botão para alternar um disco do sistema operacional no portal do Azure.](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Escolha o novo disco que você reparou e, em seguida, insira o nome da VM para confirmar a alteração. Se você não vir o disco na lista, aguarde de 10 a 15 minutos depois de desanexar o disco da VM de solução de problemas. Verifique também se o disco está no mesmo local que a VM.
1. Selecione **OK**.

## <a name="next-steps"></a>Próximas etapas
Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões área de trabalho remota a uma VM do Azure](troubleshoot-rdp-connection.md). Para problemas de acesso a aplicativos em execução em sua VM, consulte [solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md).

Para obter mais informações sobre como usar Azure Resource Manager, consulte a [visão geral Azure Resource Manager](../../azure-resource-manager/management/overview.md).


