---
title: Usar uma VM de solução de problemas do Linux no Portal do Azure | Microsoft Docs
description: Saiba como solucionar problemas da máquina virtual do Linux conectando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: affdee6871649102ef7881fb0f540eba6ab450ca
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200950"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Solucionar problemas de uma VM do Linux anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure
Se a VM (máquina virtual) do Linux tiver um erro de disco ou de inicialização, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida em `/etc/fstab` que impede que a VM possa ser inicializada corretamente. Este artigo fornece detalhes sobre como usar o portal do Azure para conectar o disco rígido virtual a outra VM do Linux para corrigir erros e recriar a VM original.

## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Pare a VM afetada.
1. Tire um instantâneo do disco do sistema operacional da VM.
1. Crie um disco rígido virtual a partir do instantâneo.
1. Anexe e monte o disco rígido virtual em outra VM do Linux para fins de solução de problemas.
1. Conecte-se à VM de solução de problemas. Edite os arquivos ou execute as ferramentas para corrigir os problemas no disco rígido virtual original.
1. Desmonte e desanexe o disco rígido virtual da VM de solução de problemas.
1. Troque o disco do sistema operacional da VM.

> [!NOTE]
> Este artigo não se aplica à VM com disco não gerenciado.

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Examine o diagnóstico de inicialização e a captura de tela da VM para determinar por que a VM não pode ser inicializada corretamente. Um exemplo comum seria uma entrada inválida em `/etc/fstab` ou a exclusão ou movimentação do disco rígido virtual subjacente.

Selecione a VM no portal e role para baixo até a seção **Suporte + Solução de problemas**. Clique em **Diagnóstico de inicialização** para exibir as mensagens do console transmitidas pela VM. Examine os logs do console para ver se você pode determinar por que a VM está com problemas. O seguinte exemplo mostra uma VM parada no modo de manutenção que exige interação manual:

![Exibindo os logs do console do diagnóstico de inicialização da VM](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Também é possível clicar em **Captura de tela** na parte superior do log do diagnóstico de inicialização para baixar uma captura de tela da VM.

## <a name="take-a-snapshot-of-the-os-disk"></a>Tirar um instantâneo do disco do sistema operacional
Um instantâneo é uma cópia completa, somente leitura de um disco rígido virtual (VHD). Recomendamos que você desligue corretamente a VM antes de tirar um instantâneo, para limpar todos os processos em andamento. Para tirar um instantâneo de um disco do sistema operacional, siga estas etapas:

1. Vá para [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e, em seguida, selecione a VM que tem o problema.
1. No painel esquerdo, selecione **discos** e, em seguida, selecione o nome do disco do sistema operacional.
    ![Imagem sobre o nome do disco do sistema operacional](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na página **visão geral** do disco do sistema operacional, selecione **criar instantâneo**.
1. Crie um instantâneo no mesmo local que o disco do sistema operacional.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo
Para criar um disco a partir do instantâneo, siga estas etapas:

1. Selecione **Cloud Shell** na portal do Azure.

    ![Imagem sobre abrir Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Execute os comandos do PowerShell a seguir para criar um disco gerenciado a partir do instantâneo. Você deve substituir esses nomes de exemplo pelos nomes apropriados.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Se os comandos forem executados com êxito, você verá o novo disco no grupo de recursos que você forneceu.

## <a name="attach-disk-to-another-vm"></a>Anexar disco a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Depois de anexar o disco à VM de solução de problemas, você pode procurar e editar o conteúdo do disco. Esse processo permite que você corrija quaisquer erros de configuração ou examine arquivos adicionais de log do sistema ou do aplicativo. Para anexar o disco a outra VM, siga estas etapas:

1. Selecione o grupo de recursos no portal e a VM de solução de problemas. Selecione **discos**, selecione **Editar** e, em seguida, clique em **adicionar disco de dados**:

    ![Anexar um disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Na lista **discos de dados** , selecione o disco do sistema operacional da VM que você identificou. Se você não vir o disco do sistema operacional, verifique se a solução de problemas da VM e do disco do sistema operacional estão na mesma região (local). 
3. Selecione **salvar** para aplicar as alterações.

## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir fornecem detalhes das etapas necessárias em uma VM do Ubuntu. Se você estiver usando outra distribuição Linux, como Red Hat Enterprise Linux ou SUSE, as localizações do arquivo de log e os comandos `mount` poderão ser um pouco diferentes. Consulte a documentação da distribuição específica para obter as alterações apropriadas aos comandos.

1. SSH para a VM de solução de problemas usando as credenciais apropriadas. Se esse disco for o primeiro disco de dados anexado à VM de solução de problemas, provavelmente ele estará conectado a `/dev/sdc`. Use `dmseg` para listar os discos anexados:

    ```bash
    dmesg | grep SCSI
    ```
    A saída deverá ser semelhante ao seguinte exemplo:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    No exemplo anterior, o disco do sistema operacional está em `/dev/sda` e o disco temporário fornecido para cada VM está em `/dev/sdb`. Se você tiver vários discos de dados, eles deverão estar em `/dev/sdd`, `/dev/sde` e assim por diante.

2. Crie um diretório para montar o disco rígido virtual existente. O seguinte exemplo cria um diretório chamado `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se você tiver várias partições no disco rígido virtual existente, monte a partição necessária. O seguinte exemplo monta a primeira partição primária em `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > A prática recomendada é montar os discos de dados em VMs no Azure usando o UUID (identificador universal exclusivo) do disco rígido virtual. Para este cenário de solução de problemas breve, não é necessário montar o disco rígido virtual usando o UUID. No entanto, no uso normal, editar `/etc/fstab` para montar os discos rígidos virtuais usando o nome do dispositivo em vez do UUID poderá causar falha de inicialização da VM.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois de resolver os erros, desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão do SSH da VM de solução de problemas, desmonte o disco rígido virtual existente. Altere do diretório pai para o ponto de montagem primeiro:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O seguinte exemplo desmonta o dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Selecione a VM no portal e clique em **Discos**. Selecione o disco rígido virtual existente e clique em **Desanexar**:

    ![Desanexar um disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que a VM tenha desanexo o disco de dados corretamente antes de continuar.

## <a name="swap-the-os-disk-for-the-vm"></a>Trocar o disco do sistema operacional da VM

Portal do Azure agora dá suporte à alteração do disco do sistema operacional da VM. Para fazer isso, execute estas etapas:

1. Vá para [portal do Azure](https://portal.azure.com). Selecione **máquinas virtuais** na barra lateral e, em seguida, selecione a VM que tem o problema.
1. No painel esquerdo, selecione **discos** e, em seguida, selecione **trocar disco do sistema operacional**.
        ![A imagem sobre o disco do sistema operacional de permuta no portal do Azure](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Escolha o novo disco que você reparou e digite o nome da VM para confirmar a alteração. Se você não vir o disco na lista, aguarde 10 ~ 15 minutos depois de desanexar o disco da VM de solução de problemas. Verifique também se o disco está no mesmo local que a VM.
1. Selecione OK.

## <a name="next-steps"></a>Próximas etapas
Se você estiver tendo problemas para se conectar à VM, consulte [Solucionar problemas de conexões SSH com uma VM do Azure](troubleshoot-ssh-connection.md). Para ver problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Linux](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).

Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
