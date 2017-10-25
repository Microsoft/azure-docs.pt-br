---
title: "Usar uma VM Windows de solução de problemas com o Azure PowerShell | Microsoft Docs"
description: "Saiba como solucionar problemas de VMs Windows no Azure conectando o disco do sistema operacional a uma VM de recuperação usando o Azure PowerShell"
services: virtual-machines-windows
documentationCenter: 
authors: genlin
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/26/2017
ms.author: genli
ms.openlocfilehash: 8b7821b4285e73d461af426bfdfb3fdcc4454517
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Solucionar problemas de uma VM Windows anexando o disco do sistema operacional a uma VM de recuperação usando o Azure PowerShell
Se ocorrer um erro de disco ou de inicialização na VM (máquina virtual) Windows no Azure, talvez você precise realizar etapas de solução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma atualização de aplicativo com falha que impede a inicialização bem-sucedida da VM. Este artigo fornece detalhes sobre como usar o Azure PowerShell para conectar o disco rígido virtual a outra VM Windows para corrigir erros e, em seguida, recriar a VM original.


## <a name="recovery-process-overview"></a>Visão geral do processo de recuperação
O processo de solução de problemas é o seguinte:

1. Exclua a VM que tem problemas, mantendo os discos rígidos virtuais.
2. Anexe e monte o disco rígido virtual em outra VM Windows para fins de solução de problemas.
3. Conecte-se à VM de solução de problemas. Edite os arquivos ou execute as ferramentas para corrigir os problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de solução de problemas.
5. Crie uma VM usando o disco rígido virtual original.

Verifique se você tem [o último Azure PowerShell](/powershell/azure/overview) instalado e conectado à sua assinatura:

```powershell
Login-AzureRMAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro pelos seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `mystorageaccount` e `myVM`.


## <a name="determine-boot-issues"></a>Determinar problemas de inicialização
É possível exibir uma captura de tela da VM no Azure para ajudar a solucionar problemas de inicialização. Essa captura de tela pode ajudar a identificar por que uma VM falha em ser inicializada. O seguinte exemplo obtém a captura de tela da VM Windows denominada `myVM` no grupo de recursos denominado `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Examine a captura de tela para determinar por que a VM falha em ser inicializada. Anote as mensagens de erro ou os códigos de erro específicos fornecidos.


## <a name="view-existing-virtual-hard-disk-details"></a>Exibir detalhes do disco rígido virtual existente
Antes de anexar o disco rígido virtual a outra VM, você precisa identificar o nome do VHD (disco rígido virtual).

O exemplo a seguir obtém informações sobre a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Procure `Vhd URI` na seção `StorageProfile` da saída do comando anterior. A seguinte saída de exemplo truncada mostra o `Vhd URI` no final do bloco de códigos:

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Excluir VM existente
Discos rígidos virtuais e VMs são dois recursos distintos no Azure. Um disco rígido virtual é o local em que o próprio sistema operacional, os aplicativos e as configurações são armazenados. A VM em si são apenas os metadados que definem o tamanho ou a localização e que faz referência a recursos como um disco rígido virtual ou a NIC (placa de interface de rede) virtual. Cada disco rígido virtual tem uma concessão atribuída quando anexado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo durante a execução da VM, o disco do sistema operacional não pode ser desanexado, a menos que o recurso de VM seja excluído. A concessão continua associando o disco do sistema operacional a uma VM mesmo quando a VM está em um estado parado e desalocado.

A primeira etapa para recuperar a VM é excluir o recurso de VM em si. A exclusão da VM deixa os discos rígidos virtuais na conta de armazenamento. Depois que a VM for excluída, você anexa o disco rígido virtual a outra VM para solucionar os erros.

O seguinte exemplo exclui a VM chamada `myVM` do grupo de recursos chamado `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Aguarde até que a VM tenha concluído a exclusão antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que o associa à VM precisa ser liberada antes da anexação do disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar um disco rígido virtual existente a outra VM
Para as próximas etapas, você pode usar outra VM para fins de solução de problemas. Você anexa o disco rígido virtual existente a essa VM de solução de problemas para procurar e editar o conteúdo do disco. Esse processo permite que você corrija os erros de configuração ou examine arquivos de aplicativo ou de log do sistema adicionais, por exemplo. Escolha ou crie outra VM a ser usada para fins de solução de problemas.

Ao anexar o disco rígido virtual existente, especifique a URL para o disco obtido no comando `Get-AzureRmVM` anterior. O seguinte exemplo anexa um disco rígido virtual existente à VM de solução de problemas chamada `myVMRecovery` no grupo de recursos chamado `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> A adição de um disco exige a especificação do tamanho do disco. Conforme anexamos um disco existente, `-DiskSizeInGB` é especificado como `$null`. Esse valor garante que o disco de dados é anexado corretamente e sem a necessidade de determinar seu tamanho real.


## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexado

1. RDP para a VM de solução de problemas usando as credenciais apropriadas. O exemplo a seguir baixa o arquivo de conexão RDP da VM denominada `myVMRecovery` do grupo de recursos denominado `myResourceGroup` e o baixa em `C:\Users\ops\Documents`.

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
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
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora você pode realizar as etapas de manutenção e solução de problemas necessárias. Depois de resolver os problemas, continue com as próximas etapas.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
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

2. Saia da sessão RDP. Na sessão do Azure PowerShell, remova o disco rígido virtual da VM de solução de problemas.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Criar a VM com base no disco rígido original
Para criar uma VM com base no disco rígido virtual original, use [esse modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). O modelo JSON real está no seguinte link:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

O modelo implanta uma VM em uma rede virtual existente, usando a URL do VHD do comando anterior. O seguinte exemplo implanta o modelo no grupo de recursos chamado `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Responda aos prompts do modelo como nome da VM, tipo de sistema operacional e tamanho da VM. O `osDiskVhdUri` é o mesmo usado anteriormente ao anexar o disco rígido virtual existente à VM de solução de problemas.


## <a name="re-enable-boot-diagnostics"></a>Habilitar o diagnóstico de inicialização novamente

Ao criar a VM com base no disco rígido virtual existente, o diagnóstico de inicialização poderá não ser habilitado automaticamente. O seguinte exemplo habilita a extensão de diagnóstico na VM chamada `myVMDeployed` no grupo de recursos chamado `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Próximas etapas
Se estiver tendo problemas para se conectar à VM, consulte [Troubleshoot RDP connections to an Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Solucionar conexões RDP a uma VM do Azure). Para problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
