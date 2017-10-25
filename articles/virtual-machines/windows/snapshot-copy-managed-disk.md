---
title: "Criar uma cópia de um Disco Gerenciado para backup | Microsoft Docs"
description: "Saiba como criar uma cópia de um Disco Gerenciado do Azure para usar no backup ou na solução de problemas de disco."
documentationcenter: 
author: cwatson-cat
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 2/9/2017
ms.author: cwatson
ms.openlocfilehash: c19b5156bc23b06de48bc7a6dc786f576f78127d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Criar uma cópia de um VHD armazenado como um Disco Gerenciado do Azure usando instantâneos gerenciados
Crie um instantâneo de um Disco Gerenciado para backup ou crie um Disco Gerenciado usando o instantâneo e anexe-o a uma máquina virtual de teste para solução de problemas. Um Instantâneo Gerenciado é uma cópia pontual completa de um Disco Gerenciado da VM. Ele cria uma cópia somente leitura do seu VHD e, por padrão, a armazena como um Disco Gerenciado Standard. Para saber mais sobre Managed Disks, confira [Visão geral dos Managed Disks](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Para saber mais sobre preços, confira [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/managed-disks/). 

## <a name="before-you-begin"></a>Antes de começar
Caso use o PowerShell, verifique se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell. Execute o comando a seguir para instalá-lo.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para saber mais, confira [Azure PowerShell Versioning](/powershell/azure/overview) (Controle de versão do Azure PowerShell).

## <a name="copy-the-vhd-with-a-snapshot"></a>Copiar o VHD com um instantâneo
Use o Portal do Azure ou o PowerShell para criar um instantâneo do Disco Gerenciado.

### <a name="use-azure-portal-to-take-a-snapshot"></a>Usar o Portal do Azure para criar um instantâneo 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Iniciando no canto superior esquerdo, clique em **Novo** e procure **instantâneo**.
3. Na folha Instantâneo, clique em **Criar**.
4. Insira um **Nome** para o instantâneo.
5. Selecione um [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente ou digite o nome de um novo. 
6. Selecione um Local do datacenter do Azure.  
7. Para **Disco de origem**, selecione o Disco Gerenciado para instantâneo.
8. Escolha o **Tipo de conta** a ser usado para armazenar o instantâneo. É recomendável **Standard_LRS**, a menos que você precise dele armazenado em um disco de alto desempenho.
9. Clique em **Criar**.

### <a name="use-powershell-to-take-a-snapshot"></a>Usar o PowerShell para criar um instantâneo
As etapas a seguir mostram como obter o disco VHD a ser copiado, criar as configurações de instantâneo e criar um instantâneo do disco usando o cmdlet New-AzureRmSnapshot<!--Add link to cmdlet when available-->. 

1. Defina alguns parâmetros. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'southeastasia' 
$dataDiskName = 'ContosoMD_datadisk1' 
$snapshotName = 'ContosoMD_datadisk1_snapshot1'  
```
  Substitua os valores de parâmetro:
  -  "myResourceGroup" pelo grupo de recursos da VM.
  -  "southeastasia" pela localização geográfica onde você quer armazenar o Instantâneo Gerenciado. <!---How do you look these up? -->
  -  "ContosoMD_datadisk1" pelo nome do disco VHD que você quer copiar.
  -  "ContosoMD_datadisk1_snapshot1" pelo nome que você quer usar para o novo instantâneo.

2. Obtenha o disco VHD a ser copiado.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Crie as configurações do instantâneo. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Crie o instantâneo.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Se você planeja usar o instantâneo para criar um Disco Gerenciado e anexá-lo a uma VM que precisa ser de alto desempenho, use o parâmetro `-AccountType Premium_LRS` com o comando New-AzureRmSnapshot. O parâmetro cria o instantâneo para que ele seja armazenado como um Disco Gerenciado Premium. Os Discos Gerenciados Premium são mais caros que os Standard. Portanto, assegure-se de que os discos Premium sejam realmente necessários antes de usar esse parâmetro.


