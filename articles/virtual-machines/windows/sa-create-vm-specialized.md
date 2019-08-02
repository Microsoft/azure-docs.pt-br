---
title: Criar uma VM de um disco especializado no Azure | Microsoft Docs
description: Criar uma nova VM anexando um disco não gerenciado, no modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 8833ddf487c36446b5e5b4ce1d6cfc6363d3ceeb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710382"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Criar uma VM a partir de um VHD especializado em uma conta de armazenamento

Crie uma nova VM anexando um disco não gerenciado especializado como o disco do sistema operacional usando o Powershell. Um disco especializado é uma cópia do VHD de uma VM existente que mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. 

Você tem duas opções:
* [Carregar um VHD](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Copie o VHD de uma VM existente do Azure](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="option-1-upload-a-specialized-vhd"></a>Opção 1: carregar um VHD especializado

Você pode carregar o VHD de uma VM especializada criado com uma ferramenta de virtualização local, como o Hyper-V, ou em uma VM exportada de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Você pode carregar um VHD especializado que foi criado usando uma VM local ou um VHD exportado de outra nuvem. Um VHD especializado mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. Se você pretende usar o VHD no estado em que se encontra para criar uma nova VM, certifique-se de que as seguintes etapas sejam concluídas. 
  
  * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalizar a VM usando o Sysprep.
  * Remover quaisquer ferramentas e agentes de virtualização de convidado que estejam instalados na VM (ou seja, ferramentas do VMware).
  * Verifique se a VM está configurada para obter o endereço IP e as configurações de DNS por meio de DHCP. Isso garante que o servidor obtém um endereço IP na VNet quando ele é iniciado. 


### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Você precisa de uma conta de armazenamento no Azure para armazenar a imagem da VM carregada. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Para exibir as contas de armazenamento disponíveis, digite:

```powershell
Get-AzStorageAccount
```

Se você quiser usar uma conta de armazenamento existente, vá para a seção Carregar a imagem da VM.

Se você precisa criar uma conta de armazenamento, siga estas etapas:

1. Você precisa do nome do grupo de recursos no qual a conta de armazenamento deve ser criada. Para saber quais são todos os grupos de recursos que estão em sua assinatura, digite:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Para criar um grupo de recursos denominado **myResourceGroup** na região **Oeste dos EUA**, digite:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento com o nome **mystorageaccount** neste grupo de recursos usando o cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount):
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD na sua conta de armazenamento
Use o cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) para carregar a imagem em um contêiner na conta de armazenamento. Este exemplo carrega o arquivo **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` em uma conta de armazenamento denominada **mystorageaccount** no grupo de recursos **myResourceGroup**. O arquivo será colocado em um contêiner chamado **mycontainer** e o novo nome do arquivo será **myUploadedVHD.vhd**.

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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opção 2: copie o VHD de uma VM existente do Azure

Você pode copiar um VHD para outra conta de armazenamento para usar ao criar uma VM nova, duplicada.

### <a name="before-you-begin"></a>Antes de começar
Lembre-se de:

* Ter as informações sobre as **contas de armazenamento de origem e destino**. Para a VM de origem, você precisa ter os nomes da conta de armazenamento e do contêiner. Normalmente, o nome do contêiner será **vhds**. Você também precisa ter uma conta de armazenamento de destino. Se não tiver uma, poderá criá-la usando o portal (**Todos os serviços** > Contas de armazenamento > Adicionar) ou usando o cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount). 
* Baixar e instalar a [ferramenta AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Desalocar a VM
Desaloque a VM para liberar o VHD a ser copiado. 

* **Portal**: clique em **Máquinas virtuais** > **myVM** > Parar
* **PowerShell**: Use [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) para parar (desalocar) a VM chamada **myVM** no grupo de recursos **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

O **Status** da VM no Portal do Azure muda de **Parado** para **Parado (desalocado)** .

### <a name="get-the-storage-account-urls"></a>Obter as URLs da conta de armazenamento
Você precisará das URLs das contas de armazenamento de origem e de destino. As URLs serão semelhantes a: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se você já souber os nomes da conta de armazenamento e do contêiner, basta substituir as informações entre colchetes para criar sua URL. 

Você pode usar o Portal do Azure ou o Azure PowerShell para obter a URL:

* **Portal**: clique em **>** para **Todos os serviços** > **Contas de armazenamento** > *conta de armazenamento* > **Blobs** e seu arquivo VHD de origem provavelmente estará no contêiner **vhds**. Clique em **Propriedades** do contêiner e copie o texto rotulado como **URL**. Você precisará das URLs dos contêineres de origem e de destino. 
* **PowerShell**: Use [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) para obter as informações da VM chamada **myVM** no grupo de recursos **myResourceGroup**. Nos resultados, examine a seção **Perfil de armazenamento** para o **URI do VHD**. A primeira parte do URI é a URL do contêiner e a última parte é o nome do VHD do sistema operacional da VM.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Obter as chaves de acesso de armazenamento
Localize as chaves de acesso para as contas de armazenamento de origem e destino. Para obter mais informações sobre as chaves de acesso, consulte [Sobre as contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md).

* **Portal**: clique em **Todos os serviços** > **Contas de armazenamento** > *conta de armazenamento* > **Chaves de acesso**. Copie a chave rotulada como **key1**.
* **PowerShell**: Use [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) para obter a chave de armazenamento para a conta de armazenamento **mystorageaccount** no grupo de recursos **myResourceGroup**. Copie a chave rotulada como **key1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Copie o VHD
Você pode copiar arquivos entre as contas de armazenamento usando o AzCopy. Para o contêiner de destino, se o contêiner especificado não existir, ele será criado para você. 

Para usar o AzCopy, abra um prompt de comando no computador local e navegue até a pasta na qual o AzCopy está instalado. Ela será semelhante a *C:\Arquivos de Programas (x86)\Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos os arquivos dentro de um contêiner, você deve usar a opção **/S**. Ele pode ser usado para copiar o VHD do OS e todos os discos de dados, se eles estiverem no mesmo contêiner. Este exemplo mostra como copiar todos os arquivos no contêiner **mysourcecontainer** na conta de armazenamento **mysourcestorageaccount** para o contêiner **mydestinationcontainer** na conta de armazenamento **mydestinationstorageaccount**. Substitua os nomes das contas de armazenamento e dos contêineres por seus próprios. Substitua `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` pelas suas próprias chaves.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se você quiser copiar apenas um VHD específico em um contêiner com vários arquivos, você também poderá especificar o nome do arquivo usando a opção /Pattern. Neste exemplo, somente o arquivo chamado **myFileName.vhd** será copiado.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Quando ele for concluído, você receberá uma mensagem como a seguinte:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Solução de problemas
* Ao usar o AZCopy, se você receber o erro “Falha do servidor em autenticar a solicitação”, verifique se o valor do cabeçalho Authorization está formado corretamente, incluindo a assinatura. Se estiver usando a Chave 2 ou a chave de armazenamento secundária, tente usar a chave primária ou a primeira chave de armazenamento.

## <a name="create-the-new-vm"></a>Crie a nova VM 

Você precisa criar rede e outros recursos de máquina virtual a ser usado pela nova VM.

### <a name="create-the-subnet-and-vnet"></a>Criar a VNet e a sub-rede

Crie a rede virtual e a sub-rede da [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada **mySubNet** no grupo de recursos **myResourceGroup** e define o prefixo de endereço como **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie a VNet. Este exemplo define o nome de rede virtual para **myVnetName**, o local para **Oeste dos EUA** e o prefixo de endereço da rede virtual para **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
   Para fazer logon em sua VM usando RDP, é preciso ter uma regra de segurança que permita acesso RDP na porta 3389. Como o VHD para a nova VM foi criado com base em uma VM especializada existente, depois que a VM é criada, você pode usar uma conta existente na máquina virtual de origem que tenha permissão para fazer logon usando o RDP.
   Isso precisa ser concluído antes de criar a interface de rede à qual ele será associado.  
   Este exemplo define o nome NSG para **myNsg** e o nome da regra RDP para **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre regras de NSGs e pontos de extremidade, veja [Abrir portas para uma VM no Azure usando PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e uma NIC
Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

1. Crie o endereço IP público. Neste exemplo, o nome do endereço IP público é definido como **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie a NIC. Neste exemplo, o nome da NIC é definido como **myNicName**. Esta etapa também associa o grupo de segurança de rede criado anteriormente com esta NIC.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Como definir o nome e tamanho da VM

Este exemplo define o nome da VM para "myVM" e o tamanho da VM para "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Como adicionar o NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Como configurar o disco do sistema operacional

1. Defina o URI do VHD que você carregou ou copiou. Neste exemplo, o arquivo VHD **myOsDisk.vhd** é mantido em uma conta de armazenamento nomeada **myStorageAccount** em um contêiner nomeado **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adicione o disco do sistema operacional. Neste exemplo, quando o disco do sistema operacional é criado, o termo "osDisk" é acrescentado ao nome da VM para criar o nome do disco do sistema operacional. Este exemplo também especifica que este VHD baseado em Windows deve ser anexado à VM como o disco do sistema operacional.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: se você tiver discos de dados que precisam ser anexados à VM, adicione os discos de dados usando as URLs de VHDs de dados e o número de unidade lógica (Lun) apropriado.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Ao usar uma conta de armazenamento, as URLs do disco do sistema operacional e dos dados se parecem com esta: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Você pode encontrá-las no portal navegando até o contêiner de armazenamento de destino, clicando no VHD de sistema operacional ou nos dados que foram copiados e copiando o conteúdo da URL.


### <a name="complete-the-vm"></a>Concluir a VM 

Crie a VM usando as configurações que acabamos de criar.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se o comando for bem-sucedido, você verá uma saída como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verificar se a VM foi criada
Você deverá ver a VM recém-criada no [Portal do Azure](https://portal.azure.com) em **Todos os serviços** > **Máquinas virtuais** ou usando os seguintes comandos do PowerShell:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Próximas etapas
Logue na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md).

