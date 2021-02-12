---
title: Usar uma VM de solução de problemas do Linux com a CLI do Azure | Microsoft Docs
description: Saiba como solucionar problemas de VM do Linux conectando o disco do sistema operacional a uma VM de recuperação usando a CLI do Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 70855f7956e42d815e0b8135ccfe6e374853d8aa
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197516"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Solucionar problemas de uma VM do Linux anexando o disco do SO a uma VM de recuperação com a CLI do Azure
Se a VM (máquina virtual) do Linux tiver um erro de disco ou de inicialização, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma entrada inválida em `/etc/fstab` que impede que a VM possa ser inicializada corretamente. Este artigo fornece detalhes sobre como usar a CLI do Azure para conectar o disco rígido virtual a outra VM do Linux para corrigir erros e recriar a VM original. 

## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Pare a VM afetada.
1. Tire um instantâneo do disco do sistema operacional da VM.
1. Crie um disco do instantâneo do disco do sistema operacional.
1. Anexe e monte o novo disco do sistema operacional em outra VM do Linux para fins de solução de problemas.
1. Conecte-se à VM de solução de problemas. Edite arquivos ou execute qualquer ferramenta para corrigir problemas no novo disco do sistema operacional.
1. Desmonte e desanexe o novo disco do sistema operacional da VM de solução de problemas.
1. Altere o disco do sistema operacional para a VM afetada.

Para realizar essas etapas de solução de problemas, é preciso ter a [CLI do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/reference-index).

Você pode usar os comandos de reparo da VM para automatizar as etapas 1, 2, 3, 4, 6 e 7. Para obter mais documentação e instruções, consulte [reparar uma VM do Linux usando os comandos de reparo da máquina virtual do Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

> [!Important]
> Os scripts neste artigo se aplicam somente às VMs que usam [Disco Gerenciado](../managed-disks-overview.md). 

Nos exemplos a seguir, substitua os nomes de parâmetro pelos seus próprios valores, como `myResourceGroup` e `myVM` .

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
Examine a saída serial para determinar por que a VM não pode ser inicializada corretamente. Um exemplo comum é uma entrada inválida em `/etc/fstab` ou a exclusão ou movimentação do disco rígido virtual subjacente.

Obtenha os logs de inicialização com [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). O exemplo a seguir obtém a saída serial da VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Examine a saída serial para determinar por que a VM não é inicializada corretamente. Se a saída serial não fornecer nenhuma indicação, talvez seja necessário examinar os arquivos de log em `/var/log` depois de conectar o disco rígido virtual a uma VM de solução de problemas.

## <a name="stop-the-vm"></a>Pare a VM.

O seguinte exemplo para a VM chamada `myVM` do grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Tirar um instantâneo do disco do sistema operacional da VM afetada

Um instantâneo é uma cópia completa somente leitura de um VHD. Ele não pode ser anexado a uma VM. Na próxima etapa, vamos criar um disco desse instantâneo. O exemplo a seguir cria um instantâneo com o nome `mySnapshot` do sistema operacional disco da VM denominada 'myVM'. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo

Esse script cria um disco gerenciado com o nome `myOSDisk` desde o instantâneo chamado `mySnapshot`.  

```azurecli
#Provide the name of your resource group
$resourceGroup="myResourceGroup"

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot="mySnapshot"

#Provide the name of the Managed Disk
$osDisk="myNewOSDisk"

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType="Premium_LRS"

#Provide the OS type
$osType="linux"

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query id -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Se o grupo de recursos e o instantâneo de origem não estiverem na mesma região, você receberá o erro "recurso não encontrado" ao executar `az disk create` . Nesse caso, você deve especificar `--location <region>` para criar o disco na mesma região que o instantâneo de origem.

Agora você tem uma cópia do disco do sistema operacional original. Você pode montar esse novo disco em outra VM do Windows para fins de solução de problemas.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Anexar o novo disco rígido virtual a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Você anexa o disco a essa VM de solução de problemas para procurar e editar o conteúdo do disco. Esse processo permite que você corrija quaisquer erros de configuração ou examine arquivos adicionais de log do sistema ou do aplicativo.

Esse script anexa o disco `myNewOSDisk` à VM `MyTroubleshootVM` :

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az disk show -g $resourceGroup -n $osDisk --query id -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $myNewOSDiskid --resource-group $resourceGroup --size-gb $diskSize --sku $storageType --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

> [!NOTE]
> Os exemplos a seguir fornecem detalhes das etapas necessárias em uma VM do Ubuntu. Se você estiver usando outra distribuição Linux, como Red Hat Enterprise Linux ou SUSE, as localizações do arquivo de log e os comandos `mount` poderão ser um pouco diferentes. Consulte a documentação da distribuição específica para obter as alterações apropriadas aos comandos.

1. SSH para a VM de solução de problemas usando as credenciais apropriadas. Se esse disco for o primeiro disco de dados anexado à VM de solução de problemas, provavelmente ele estará conectado a `/dev/sdc`. Use `dmesg` para exibir os discos anexados:

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


## <a name="fix-issues-on-the-new-os-disk"></a>Corrigir problemas no novo disco do sistema operacional
Com o disco rígido virtual existente montado, agora você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.


## <a name="unmount-and-detach-the-new-os-disk"></a>Desmontar e desanexar o novo disco do sistema operacional
Depois de resolver os erros, desmonte e desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão do SSH da VM de solução de problemas, desmonte o disco rígido virtual existente. Altere do diretório pai para o ponto de montagem primeiro:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O seguinte exemplo desmonta o dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Saia da sessão SSH para a VM de solução de problemas:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Altere o disco do sistema operacional para a VM afetada.

Você pode usar CLI do Azure para trocar os discos do sistema operacional. Você não precisa excluir e recriar a VM.

Este exemplo interrompe a VM denominada `myVM` e atribui o disco denominado `myNewOSDisk` como o novo disco de sistema operacional.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g $resourceGroup -n $osDisk --query id -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas
Se você estiver tendo problemas para se conectar à VM, consulte [Solucionar problemas de conexões SSH com uma VM do Azure](troubleshoot-ssh-connection.md). Para ver problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Linux](troubleshoot-app-connection.md).

