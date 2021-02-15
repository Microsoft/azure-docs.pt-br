---
title: Alternar entre discos do sistema operacional usando a CLI '
description: Mude o disco do sistema operacional usado por uma máquina virtual do Azure usando a CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: adcbc5b0ba0056c79576e8d4c8ded2150b206513
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371865"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Mudar o disco do sistema operacional usado por uma VM do Azure usando a CLI


Se você tiver uma VM já existente, mas quiser trocar o disco por um disco de backup ou outro disco do sistema operacional, você pode usar a CLI do Azure para trocar os discos do SO. Você não precisa excluir e recriar a VM. Você pode até usar um disco gerenciado em outro grupo de recursos, desde que ele não esteja em uso.

A VM precisa ser interrompida\desalocada e, em seguida, a ID de recurso do disco gerenciado pode ser substituída pela ID de recurso de um disco gerenciado diferente. 

Certifique-se de que o tipo de armazenamento e o tamanho da VM sejam compatíveis com o disco que você deseja anexar. Por exemplo, se o disco que você deseja usar estiver no armazenamento Premium, a VM precisa ter capacidade de armazenamento Premium (como um tamanho da série DS).

Este tutorial requer a CLI do Azure, versão 2.0.25 ou superior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 


Use [az disk list](/cli/azure/disk) para obter uma lista dos discos no grupo de recursos.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Use [az vm stop](/cli/azure/vm) para interromper\desalocar a VM antes de trocar os discos.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Use [az vm update](/cli/azure/vm#az-vm-update) com a ID de recurso completo do novo disco para o parâmetro `--osdisk` 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Reinicie a VM usando [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Próximas etapas**

Para criar uma cópia de um disco, consulte [Instantâneo de um disco](snapshot-copy-managed-disk.md).
