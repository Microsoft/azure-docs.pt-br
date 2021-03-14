---
title: Como mapear discos do Azure para discos convidados da VM do Windows
description: Como determinar os discos do Azure que underlaym os discos convidados de uma VM do Windows.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 7a4fad066af37217eb42060d5fc5a7ef716770c6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560983"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Como mapear discos do Azure para discos convidados da VM do Windows

Talvez seja necessário determinar os discos do Azure que retornam os discos convidados de uma VM. Em alguns cenários, você pode comparar o tamanho do disco ou do volume com o tamanho dos discos do Azure anexados. Em cenários em que há vários discos do Azure com o mesmo tamanho anexado à VM, você precisa usar o LUN (número de unidade lógica) dos discos de dados. 

## <a name="what-is-a-lun"></a>O que é um LUN?

Um LUN (número de unidade lógica) é um número usado para identificar um dispositivo de armazenamento específico. Cada dispositivo de armazenamento recebe um identificador numérico exclusivo, começando em zero. O caminho completo para um dispositivo é representado pelo número de barramento, número de ID de destino e LUN (número de unidade lógica). 

Por exemplo: ***número de barramento 0, ID de destino 0, LUN 3***

Para nosso exercício, você só precisa usar o LUN.

## <a name="finding-the-lun"></a>Encontrando o LUN

Há dois métodos para localizar o LUN, que você escolher dependerá se você estiver usando [espaços de armazenamento](/windows-server/storage/storage-spaces/overview) ou não.

### <a name="disk-management"></a>Gerenciamento de disco

Se você não estiver usando pools de armazenamento, poderá usar o [Gerenciamento de disco](/windows-server/storage/disk-management/overview-of-disk-management) para localizar o LUN.

1. Conecte-se à VM e abra o gerenciamento de disco a. Clique com o botão direito do mouse no botão Iniciar e escolha "gerenciamento de disco" a. Você também pode digitar `diskmgmt.msc` na caixa Iniciar pesquisa
1. No painel inferior, clique com o botão direito do mouse em qualquer um dos discos e escolha "Propriedades"
1. O LUN será listado na propriedade "Location" na guia "General"

### <a name="storage-pools"></a>Pools de armazenamento

1. Conecte-se à VM e abra Gerenciador do Servidor
1. Selecione "serviços de arquivo e armazenamento", "volumes", "pools de armazenamento"
1. No canto inferior direito de Gerenciador do Servidor, haverá uma seção "discos físicos". Os discos que compõem o pool de armazenamento estão listados aqui, bem como o LUN para cada disco.

## <a name="finding-the-lun-for-the-azure-disks"></a>Encontrando o LUN para os discos do Azure

Você pode localizar o LUN para um disco do Azure usando o portal do Azure, CLI do Azure ou Azure PowerShell

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Localizando um LUN do disco do Azure no portal do Azure

1. No portal do Azure, selecione "máquinas virtuais" para exibir uma lista de suas máquinas virtuais
1. Selecione a Máquina Virtual
1. Selecione "discos"
1. Selecione um disco de dados na lista de discos anexados.
1. O LUN do disco será exibido no painel detalhes do disco. O LUN exibido aqui se correlaciona com os LUNs que foram pesquisados no convidado usando Gerenciador de Dispositivos ou Gerenciador do Servidor.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Localizando um LUN do disco do Azure usando CLI do Azure ou Azure PowerShell

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---