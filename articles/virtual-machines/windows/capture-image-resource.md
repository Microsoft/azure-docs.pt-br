---
title: Criação de uma imagem gerenciada no Azure
description: Crie uma imagem gerenciada de uma VM ou um VHD generalizado no Azure. Imagens podem ser usadas para criar várias VMs que usam discos gerenciados.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 9128c44b7f446ab849d2afac055005a1b5fb3fcb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562224"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Criar uma imagem gerenciada de uma VM generalizada no Azure

Um recurso de imagem gerenciada pode ser criado de uma VM (máquina virtual) generalizada que é armazenada como um disco gerenciado ou um disco não gerenciado em uma conta de armazenamento. Em seguida, a imagem pode ser usada para criar várias VMs. Para obter informações de como as imagens gerenciadas são cobradas, confira [Preços do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

Uma imagem gerenciada dá suporte a até 20 implantações simultâneas. A tentativa de criar simultaneamente mais de 20 VMs a partir da mesma imagem gerenciada pode exceder os tempos limite de provisionamento devido às limitações de desempenho de armazenamento de um único VHD. Para criar simultaneamente mais de 20 VMs, use uma imagem das [Galerias de Imagens Compartilhadas](../shared-image-galleries.md), configurada com uma réplica para cada 20 implantações simultâneas de VM.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM Windows usando Sysprep

O Sysprep remove todas as informações pessoais e de segurança da conta e prepara a máquina para ser usada como uma imagem. Para obter informações sobre o Sysprep, confira [Visão geral do Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para saber mais, confira [Suporte do Sysprep para funções de servidor](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) e [Cenários sem suporte](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> Depois que o Sysprep for executado em uma VM, essa VM será considerada *generalizada* e não poderá ser reiniciada. O processo de generalização de uma VM não é reversível. Se você precisar manter o funcionamento da VM original, crie uma [cópia da VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) e generalize a cópia. 
>
>O Sysprep requer que as unidades sejam totalmente descriptografadas. Se você habilitou a criptografia em sua VM, desabilite a criptografia antes de executar o Sysprep.
>
> Se você planeja executar o Sysprep antes de carregar o VHD (disco rígido virtual) no Azure pela primeira vez, [prepare a VM](prepare-for-upload-vhd-image.md).  
> 
> 

Para generalizar a VM do Windows, siga estas etapas:

1. Entre na VM do Windows.
   
2. Abra uma janela de Prompt de comando como administrador. 

3. Exclua o diretório Panther (C:\Windows\Panther). Em seguida, altere o diretório para%WINDIR%\system32\sysprep e, em seguida, execute `sysprep.exe` .
   
4. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na OOBE (configuração inicial pelo usuário) do sistema** e marque a caixa de seleção **Generalizar**.
   
5. Para **Opções de Desligamento**, selecione **Desligar**.
   
6. Selecione **OK**.
   
    ![Inicie o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Quando o Sysprep for concluído, ele desligará a VM. Não reinicie a VM.

> [!TIP]
> **Opcional** – use [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) para otimizar a imagem e reduzir a primeira hora de inicialização da VM.
>
> Para otimizar a imagem, monte o VHD clicando duas vezes nele no Windows Explorer e, em seguida, execute o DISM com o parâmetro `/optimize-image`.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> No qual D: é o caminho do VHD montado.
>
> A execução de `DISM /optimize-image` deve ser a última modificação feita no VHD. Se você fizer alterações no VHD antes da implantação, precisará executar `DISM /optimize-image` novamente.

## <a name="create-a-managed-image-in-the-portal"></a>Criação de uma imagem gerenciada no portal 

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar a imagem da VM. Pesquise por **Máquinas virtuais** e selecione essa opção.

2. Selecione a VM na lista.

3. Na página **Máquina virtual** da VM, no menu superior, selecione **Capturar**.

   A página **Criar imagem** será exibida.

4. Para **Nome**, aceite o nome já preenchido ou insira um nome que você deseje usar para a imagem.

5. Em **Grupo de recursos**, selecione **Criar** e insira um nome ou selecione o grupo de recursos a ser usado.

6. Se você quiser excluir a VM de origem depois que a imagem foi criada, selecione **Excluir automaticamente esta máquina virtual após criar a imagem**.

7. Se você quiser a capacidade de usar a imagem em qualquer [zona de disponibilidade](../../availability-zones/az-overview.md), selecione **Habilitado** para **Resiliência de zona**.

8. Selecione **Criar** para criar a imagem.

Depois que a imagem for criada, você poderá encontrá-la como um recurso de **imagem** na lista de recursos no grupo de recursos.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Criar uma imagem de uma VM usando o PowerShell

 

Criar uma imagem diretamente da VM garante que a imagem inclua todos os discos associados à VM, incluindo o disco do sistema operacional e os discos de dados. Este exemplo mostra como criar uma imagem gerenciada de uma VM que usa discos gerenciados.

Antes de iniciar, confira se você tem a versão mais recente do módulo do Azure PowerShell. Para localizar a versão, execute `Get-Module -ListAvailable Az` no PowerShell. Se você precisar atualizar, confira [Instalar o Azure PowerShell no Windows com o PowerShellGet](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, execute `Connect-AzAccount` para criar uma conexão com o Azure.


> [!NOTE]
> Se você quiser armazenar a imagem no armazenamento com redundância de zona, você precisará criá-la em uma região com suporte para [zonas de disponibilidade](../../availability-zones/az-overview.md) e incluir o parâmetro `-ZoneResilient` na configuração da imagem (comando `New-AzImageConfig`).

Para criar uma imagem de VM, siga estas etapas:

1. Defina algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Verifique se a VM foi desalocada.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Defina o status da máquina virtual como **Generalizado**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Obtenha a máquina virtual. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Crie a configuração de imagem.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Crie a imagem.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Crie uma imagem de um disco gerenciado usando o Powershell

Se você quiser criar uma imagem somente do disco do sistema operacional, especifique a ID do disco gerenciado como o disco do sistema operacional:

    
1. Defina algumas variáveis. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Obtenha a VM.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Obtenha a ID do disco gerenciado.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Crie a configuração de imagem.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Crie a imagem.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Criar uma imagem de um instantâneo usando o PowerShell

Você pode criar uma imagem gerenciada usando um instantâneo de uma VM generalizada seguindo estas etapas:

    
1. Defina algumas variáveis. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Crie o instantâneo.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Crie a configuração de imagem.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Crie a imagem.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Criar uma imagem de uma VM que usa uma conta de armazenamento

Para criar uma imagem gerenciada de uma VM que não usa discos gerenciados, é necessário o URI do VHD do sistema operacional na conta de armazenamento, que está no seguinte formato: https://*minhacontadearmazenamento*.blob.core.windows.net/*contêinervhd*/*nomedoarquivovhd.vhd*. Neste exemplo, o VHD está em *mystorageaccount*, em um contêiner denominado *vhdcontainer*, e o nome do arquivo do VHD é *vhdfilename.vhd*.


1.  Defina algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Pare/desaloque a VM.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque a VM como generalizada.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Crie a imagem usando o VHD do sistema operacional generalizado.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Próximas etapas
- [Criar uma VM de uma imagem gerenciada](create-vm-generalized-managed.md). 