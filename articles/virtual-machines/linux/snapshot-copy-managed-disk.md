---
title: Crie um instantâneo de um VHD no Azure | Microsoft Docs
description: Saiba como criar uma cópia de um VHD no Azure como um backup ou para solução de problemas.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9f2f3ac3668f0e48716fc30fb69cd1782dbd4e56
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765672"
---
# <a name="create-a-snapshot"></a>Criar um instantâneo 

Capture um instantâneo de um SO ou disco de dados para backup ou para solucionar problemas de VM. Um instantâneo é uma cópia completa somente leitura de um VHD. 

## <a name="use-azure-cli"></a>Usar a CLI do Azure 

O exemplo a seguir exige que você use o [Cloud Shell](https://shell.azure.com/bash) ou tenha a CLI do Azure instalada.

As etapas a seguir mostram como capturar um instantâneo usando o comando **az snapshot create** com o parâmetro **--source-disk**. O exemplo a seguir assume que há uma VM chamada *myVM* no grupo de recursos *myResourceGroup*.

Obtenha a ID do disco usando [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Tire um instantâneo chamado *osDisk-backup* usando [az snapshot create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Se você quiser armazenar o instantâneo no armazenamento com resiliência de zona, será necessário criá-lo em uma região compatível com [zonas de disponibilidade](../../availability-zones/az-overview.md) e inclua o parâmetro **--sku Standard_ZRS**.

Você pode ver uma lista de instantâneos usando [az snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Usar o portal do Azure 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Iniciando no canto superior esquerdo, clique em **Criar um recurso** e procure **instantâneo**. Selecione **Instantâneo** nos resultados da pesquisa.
3. Na folha **Instantâneo**, clique em **Criar**.
4. Insira um **Nome** para o instantâneo.
5. Selecione um grupo de recursos existente ou digite o nome de um novo. 
7. Para **Disco de origem**, selecione o disco gerenciado para instantâneo.
8. Escolha o **Tipo de conta** a ser usado para armazenar o instantâneo. Use **HD Standard**, a menos que você precise dele armazenado em um SSD de alto desempenho.
9. Clique em **Criar**.


## <a name="next-steps"></a>Próximos passos

 Cria uma máquina virtual de um instantâneo criando um disco gerenciado do instantâneo e, em seguida, anexando o novo disco gerenciado como disco do SO. Para obter mais informações, consulte o script [Criar uma VM com base em um instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

