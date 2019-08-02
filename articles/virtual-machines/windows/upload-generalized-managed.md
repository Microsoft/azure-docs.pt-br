---
title: Criar uma VM do Azure gerenciada a partir de um VHD local generalizado | Microsoft Docs
description: Carregar um VHD generalizado no Azure e usá-lo para criar novas VMs, no modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: 9846bf7b28f1205f98eb59671553d309fe754d30
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707947"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carregar um VHD generalizado e usá-lo para criar novas VMs no Azure

Este artigo orienta você a usar o PowerShell para carregar um VHD de uma máquina virtual generalizada no Microsoft Azure, crie uma imagem do VHD e crie uma nova VM dessa imagem. Você pode carregar um VHD exportado de uma ferramenta de virtualização local ou de outra nuvem. Usar [Discos Gerenciados](managed-disks-overview.md) para a nova VM simplifica o gerenciamento da VM e fornece maior disponibilidade quando a VM é colocada em um conjunto de disponibilidade. 

Para um exemplo de script, consulte [Script de exemplo para carregar um VHD no Azure e criar uma nova VM](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Antes de começar

- Antes de carregar qualquer VHD no Azure, você deve seguir as etapas em [Preparar um VHD ou VHDX do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Examine [Planejar a migração para Discos Gerenciados](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) antes de iniciar a migração para [Discos Gerenciados](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalize a VM de origem usando o Sysprep

O Sysprep remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre Sysprep, consulte a [Visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Se você planeja executar o Sysprep antes de carregar o VHD no Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Entre na máquina virtual Windows.
2. Abra uma janela de prompt de comando como administrador. Mude para o diretório para %windir%\system32\sysprep e, em seguida, execute `sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está ativada.
4. Para **Opções de Desligamento**, selecione **Desligar**.
5. Selecione **OK**.
   
    ![Inicie o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep for concluído, desligará a máquina virtual. Não reinicie a VM.


## <a name="get-a-storage-account"></a>Adicionar uma conta de armazenamento

Você precisará de uma conta de armazenamento do Azure para armazenar a imagem da VM. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Se você estiver usando o VHD para criar um disco gerenciado para uma VM, o local da conta de armazenamento deve ser o mesmo local em que você criará a VM.

Para exibir as contas de armazenamento disponíveis, insira:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD na sua conta de armazenamento

Use o cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) para carregar o VHD em um contêiner na conta de armazenamento. Este exemplo carrega o arquivo *myVHD.vhd* de *C:\Users\Public\Documents\Virtual hard disks\\* para uma conta de armazenamento denominada *mystorageaccount* no grupo de recursos *myResourceGroup*. O arquivo será colocado em um contêiner chamado *mycontainer* e o novo nome do arquivo será *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se o comando tiver êxito, você receberá uma resposta semelhante a esta:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo da conexão de rede e do tamanho do arquivo VHD, esse comando poderá demorar um pouco para concluir.

### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
 
Você também pode carregar um VHD na sua conta de armazenamento usando um dos seguintes métodos:

- [AzCopy](https://aka.ms/downloadazcopy)
- [API do Blob da Cópia de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Carregamento de Blobs do Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
- [Referência de API REST do Serviço de Importação/Exportação do Armazenamento](https://msdn.microsoft.com/library/dn529096.aspx)
-   É recomendável usar o serviço de importação/exportação se o tempo de carregamento estimado for maior do que sete dias. Você pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo com base no tamanho dos dados e na unidade de transferência. 
    A Importação/Exportação pode ser usada para copiar para a conta de armazenamento standard. Você precisará copiar do armazenamento standard para a conta de armazenamento premium usando uma ferramenta como o AzCopy.

> [!IMPORTANT]
> Se você estiver usando o AzCopy para carregar o VHD no Azure, verifique se você definiu [ **/BlobType:page** ](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs#upload-a-file) antes de executar o script de upload. Se o destino é um blob e essa opção não está especificada, por padrão, o AzCopy cria um blob de blocos.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Criar uma imagem gerenciada usando o VHD carregado 

Crie uma imagem gerenciada do seu VHD do sistema operacional generalizado. Substitua os valores a seguir com suas próprias informações.


Primeiro, defina alguns parâmetros:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Crie a imagem usando o VHD do sistema operacional generalizado.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Criar a VM

Agora que tem uma imagem, você pode criar uma ou mais VMs novas por meio da imagem. Este exemplo cria uma VM denominada *myVM* a partir de *myImage*, em *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Próximas etapas

Logue na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

