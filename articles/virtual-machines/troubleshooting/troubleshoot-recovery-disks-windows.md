---
title: Usar uma VM Windows de solução de problemas com o Azure PowerShell | Microsoft Docs
description: Saiba como solucionar problemas de VMs Windows no Azure conectando o disco do sistema operacional a uma VM de recuperação usando o Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 94abf9c8621e842605a4fab521fa4df853e1fb4a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709293"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Solucionar problemas de uma VM Windows anexando o disco do sistema operacional a uma VM de recuperação usando o Azure PowerShell
Se ocorrer um erro de disco ou de inicialização na VM (máquina virtual) Windows no Azure, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma atualização de aplicativo com falha que impede a inicialização bem-sucedida da VM. Este artigo fornece detalhes sobre como usar o Azure PowerShell para conectar o disco rígido virtual a outra VM Windows para corrigir erros e, em seguida, recriar a VM original. 

> [!Important]
> Os scripts neste artigo se aplicam somente às VMs que usam [Disco Gerenciado](../windows/managed-disks-overview.md). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
Agora podemos usar Azure PowerShell para alterar o disco do sistema operacional de uma VM. Não precisamos mais excluir e recriar a VM.

O processo de solução de problemas é o seguinte:

1. Pare a VM afetada.
2. Crie um instantâneo do disco do sistema operacional da VM.
3. Crie um disco do instantâneo do disco do sistema operacional.
4. Anexe o disco do sistema operacional a uma VM de recuperação.
5. Conecte-se à VM de recuperação. Edite os arquivos ou execute quaisquer ferramentas para corrigir os problemas no disco do sistema operacional copiado.
6. Desmonte e desanexe o disco da VM de recuperação.
7. Altere o disco do sistema operacional para a VM afetada.

Você pode usar os scripts de recuperação de VM para automatizar as etapas 1, 2, 3, 4, 6 e 7. Para obter mais documentação e instruções, veja [Scripts de recuperação de VM para a VM do Resource Manager](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager).

Verifique se você tem [o último Azure PowerShell](/powershell/azure/overview) instalado e conectado à sua assinatura:

```powershell
Connect-AzAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro pelos seus próprios valores. 

## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
É possível exibir uma captura de tela da VM no Azure para ajudar a solucionar problemas de inicialização. Essa captura de tela pode ajudar a identificar por que uma VM falha em ser inicializada. O seguinte exemplo obtém a captura de tela da VM Windows denominada `myVM` no grupo de recursos denominado `myResourceGroup`:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Examine a captura de tela para determinar por que a VM falha em ser inicializada. Anote as mensagens de erro ou os códigos de erro específicos fornecidos.

## <a name="stop-the-vm"></a>Pare a VM.

O seguinte exemplo para a VM chamada `myVM` do grupo de recursos chamado `myResourceGroup`:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Aguarde até que a VM tenha concluído a exclusão antes de processar para a próxima etapa.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Crie um instantâneo do disco do sistema operacional da VM

O exemplo a seguir cria um instantâneo com o nome `mySnapshot` do sistema operacional disco da VM denominada 'myVM'. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Um instantâneo é uma cópia completa somente leitura de um VHD. Ele não pode ser anexado a uma VM. Na próxima etapa, vamos criar um disco desse instantâneo.

## <a name="create-a-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo

Esse script cria um disco gerenciado com o nome `newOSDisk` desde o instantâneo chamado `mysnapshot`.  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Agora você tem uma cópia do disco do sistema operacional original. Você pode montar o disco rígido virtual em outra VM Windows para fins de solução de problemas.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Anexe o disco a outra VM do Windows para solução de problemas

Agora podemos anexar a cópia do disco do sistema operacional original para uma VM como um disco de dados. Esse processo permite que você corrija os erros de configuração ou examine arquivos de aplicativo ou de log do sistema adicionais, por exemplo. O exemplo a seguir anexa o disco denominado `newOSDisk` à VM chamada `RecoveryVM`.

> [!NOTE]
> Para anexar o disco, a cópia de disco do sistema operacional original e a VM de recuperação deve estar no mesmo local.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Conecte-se para a VM de recuperação e corrigir problemas no disco anexado

1. RDP para a VM de solução de problemas usando as credenciais apropriadas. O exemplo a seguir baixa o arquivo de conexão RDP da VM denominada `RecoveryVM` do grupo de recursos denominado `myResourceGroup` e o baixa em `C:\Users\ops\Documents`.

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. O disco de dados é detectado e anexado automaticamente. Exiba a lista de volumes anexados para determinar a letra da unidade da seguinte maneira:

    ```powershell
    Get-Disk
    ```

    A saída de exemplo a seguir mostra o disco rígido virtual conectado a um disco **2**. (Também é possível usar `Get-Volume` para exibir a letra da unidade):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Depois que a cópia do disco do SO original for montada, você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.

## <a name="unmount-and-detach-original-os-disk"></a>Desmontar e desanexar o disco rígido virtual original
Depois de resolver os erros, desmonte e desanexe o disco rígido virtual existente da VM de solução de problemas. Não é possível usar o disco rígido virtual com nenhuma outra VM até que a concessão que anexa o disco rígido virtual à VM de solução de problemas seja liberada.

1. Na sessão RDP, desmonte o disco de dados da VM de recuperação. É necessário o número do disco do cmdlet `Get-Disk` anterior. Em seguida, use `Set-Disk` para definir o disco como offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Confirme se o disco agora está definido como offline usando `Get-Disk` novamente. A seguinte saída de exemplo mostra que o disco agora está definido como offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Saia da sessão RDP. Na sua sessão do Microsoft Azure PowerShell, remova o disco denominado `newOSDisk` da VM denominada 'RecoveryVM'.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Altere o disco do sistema operacional para a VM afetada.

Você pode usar o Microsoft Azure PowerShell para trocar os discos do sistema operacional. Você não precisa excluir e recriar a VM.

Este exemplo interrompe a VM denominada `myVM` e atribui o disco denominado `newOSDisk` como o novo disco de sistema operacional. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Verificar e habilitar o diagnóstico de boot

O seguinte exemplo habilita a extensão de diagnóstico na VM chamada `myVMDeployed` no grupo de recursos chamado `myResourceGroup`:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Próximas etapas
Se estiver tendo problemas para se conectar à VM, consulte [Troubleshoot RDP connections to an Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Solucionar conexões RDP a uma VM do Azure). Para problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
