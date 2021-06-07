---
title: Expandir a unidade do sistema operacional de uma VM Windows em um Azure
description: Expanda o tamanho da unidade do sistema operacional de uma máquina virtual usando Azure PowerShell no modelo de implantação do Gerenciador de recursos.
services: virtual-machines
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: af57bc396349286c3edcaefc8385ddbaec066226
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199443"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Como expandir a unidade do sistema operacional de uma máquina virtual

Quando você cria uma nova VM (máquina virtual) em um grupo de recursos implantando uma imagem do [Azure Marketplace](https://azure.microsoft.com/marketplace/), a unidade do sistema operacional padrão geralmente é de 127 GB (algumas imagens têm tamanhos de disco do sistema operacional menores por padrão). Embora seja possível adicionar discos de dados à VM (o número depende do SKU escolhido) e recomendamos a instalação de aplicativos e cargas de trabalho com uso intensivo de CPU nesses discos de adendo, muitas vezes, os clientes precisam expandir a unidade do sistema operacional para oferecer suporte a cenários específicos:

- Para dar suporte a aplicativos herdados que instalam componentes na unidade do sistema operacional.
- Para migrar um PC físico ou VM do local com uma unidade de sistema operacional maior.

> [!IMPORTANT]
> O redimensionamento de um sistema operacional ou de um disco de dados de uma máquina virtual do Azure requer que a máquina virtual seja desalocada.
>
> A redução de um disco existente não tem suporte e pode resultar em perda de dados.
> 
> Depois de expandir os discos, você precisará [expandir o volume no sistema operacional](#expand-the-volume-within-the-os) para aproveitar o disco maior.

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Redimensionar um disco gerenciado no portal do Azure

1. Na [portal do Azure](https://portal.azure.com), vá para a máquina virtual na qual você deseja expandir o disco. Selecione **parar** para desalocar a VM.
2. Quando a VM for interrompida, no menu à esquerda em **configurações**, selecione **discos**.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Captura de tela que mostra a opção discos selecionada na seção Configurações do menu.":::

 
3. Em **nome do disco**, selecione o disco que você deseja redimensionar.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Captura de tela que mostra o painel discos com um nome de disco selecionado.":::

4. No menu à esquerda em **configurações**, selecione **tamanho + desempenho**.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Captura de tela que mostra a opção de tamanho e desempenho selecionada na seção Configurações do menu.":::

5. Em **tamanho + desempenho**, selecione o tamanho do disco desejado.
   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2.048 GB para discos do sistema operacional. (É possível expandir o blob VHD para além desse tamanho, mas o sistema operacional funciona apenas com os primeiros 2.048 GB de espaço.)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Captura de tela que mostra o tamanho e o painel de desempenho com o tamanho do disco selecionado.":::

6. Selecione **redimensionar** na parte inferior da página.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Captura de tela que mostra o painel de tamanho e desempenho com o botão redimensionar selecionado.":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Redimensionar um disco gerenciado usando o PowerShell

Abra o ISE do PowerShell ou a janela do PowerShell no modo administrativo e siga as etapas abaixo:

1. Entre em sua conta do Microsoft Azure no modo de gerenciamento de recursos e selecione sua assinatura:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Defina o nome do grupo de recursos e o nome da VM:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Obtenha uma referência à sua VM:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Pare a VM antes de redimensionar o disco:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Obtenha uma referência ao disco do sistema operacional gerenciado. Defina o tamanho do disco do sistema operacional gerenciado para o valor desejado e atualize o disco:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2.048 GB para discos do sistema operacional. (É possível expandir o blob VHD para além desse tamanho, mas o sistema operacional funciona apenas com os primeiros 2.048 GB de espaço.)
    > 
         
6. A atualização da VM pode levar alguns segundos. Quando o comando terminar a execução, reinicie a VM:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

É isso. Agora, com o RDP na VM, abra Gerenciamento do Computador (ou Gerenciamento de Disco) e expanda a unidade usando o espaço recentemente alocado.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Redimensionar um disco não gerenciado usando o PowerShell

Abra o ISE do PowerShell ou a janela do PowerShell no modo administrativo e siga as etapas abaixo:

1. Entre em sua conta do Microsoft Azure no modo de gerenciamento de recursos e selecione sua assinatura:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Defina o nome do grupo de recursos e os nomes da VM:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Obtenha uma referência à sua VM:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Pare a VM antes de redimensionar o disco:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Defina o tamanho do disco do sistema operacional não gerenciado para o valor desejado e atualize a VM:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2.048 GB para discos do sistema operacional. (É possível expandir o blob VHD para além desse tamanho, mas o sistema operacional só poderá trabalhar com os primeiros 2.048 GB de espaço).
    > 
    > 
   
6. A atualização da VM pode levar alguns segundos. Quando o comando terminar a execução, reinicie a VM:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>Scripts para o disco do sistema operacional

Abaixo está o script completo para sua referência para discos gerenciados e não gerenciados:


**Discos gerenciados**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Discos não gerenciados**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Redimensionando discos de dados

Este artigo se concentra principalmente em expandir o disco do SO da VM, mas o script também pode ser usado para expandir os discos de dados conectados à VM. Por exemplo, para expandir o primeiro disco de dados conectado à VM, substitua o objeto `OSDisk` de `StorageProfile` pela matriz `DataDisks` e use um índice numérico para obter uma referência para o primeiro disco de dados conectado, como mostrado abaixo:

**Disco gerenciado**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Disco não gerenciado**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Da mesma forma, você pode fazer referência a outros discos de dados anexados à VM, seja usando um índice, conforme mostrado acima ou a propriedade **Name** do disco:


**Disco gerenciado**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Disco não gerenciado**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Expandir o volume no sistema operacional

Quando você tiver expandido o disco para a VM, precisará entrar no sistema operacional e expandir o volume para abranger o novo espaço. Há vários métodos para expandir uma partição. Esta seção aborda como conectar a VM usando uma conexão RDP para expandir a partição usando **DiskPart**.

1. Abra uma conexão RDP com a VM.

2. Abra um prompt de comando e digite **diskpart**.

3. No prompt **DISKPART**, digite `list volume`. Anote o volume que você deseja estender.

4. No prompt **DISKPART**, digite `select volume <volumenumber>`. Isso seleciona o volume *volumenumber* que você deseja estender no espaço vazio contíguo no mesmo disco.

5. No prompt **DISKPART**, digite `extend [size=<size>]`. Isso estende o volume selecionado pelo *tamanho* em megabytes (MB).


## <a name="next-steps"></a>Próximas etapas

Você também pode conectar discos usando o [portal do Azure](attach-managed-disk-portal.md).
