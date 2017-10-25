---
title: Expandir o disco do sistema operacional em uma VM Linux com a CLI do Azure 1.0 | Microsoft Docs
description: "Saiba como expandir o disco virtual do sistema operacional em uma VM Linux usando a CLI do Azure 1.0 e o modelo de implantação do Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 0aedcd70b54c2ed47ec327ccf0529a48351353c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Expandir o disco do sistema operacional em uma VM Linux usando a CLI do Azure 1.0
Normalmente, o tamanho do disco rígido virtual padrão do sistema operacional é de 30 GB em uma VM (máquina virtual) do Linux no Azure. É possível [adicionar discos de dados](add-disk.md) para fornecer espaço de armazenamento adicional, mas você também pode desejar expandir o disco do sistema operacional. Este artigo fornece detalhes sobre como expandir o disco do sistema operacional de uma VM Linux usando discos não gerenciados com a CLI do Azure 1.0.

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#prerequisites) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](expand-disks.md) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos

## <a name="prerequisites"></a>Pré-requisitos
Você precisa da [última CLI do Azure 1.0](../../cli-install-nodejs.md) instalada e conectada a uma [conta do Azure](https://azure.microsoft.com/pricing/free-trial/) usando o modo do Resource Manager, da seguinte maneira:

```azurecli
azure config mode arm
```

Nas amostras a seguir, substitua os nomes de parâmetro de exemplo por seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup* e *myVM*.

## <a name="expand-os-disk"></a>Expandir o disco do sistema operacional

1. As operações em discos rígidos virtuais não podem ser executadas com a VM em execução. O exemplo a seguir interrompe e desaloca a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` não libera os recursos de computação. Para liberar os recursos de computação, use `azure vm deallocate`. A VM deve ser desalocada para que o disco rígido virtual seja expandido.

2. Atualize o tamanho do disco não gerenciado do sistema operacional usando o comando `azure vm set`. O exemplo a seguir atualiza a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup* para ser *50* GB:

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. Inicie a VM da seguinte maneira:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH da VM com as credenciais apropriadas. Para verificar se o disco do sistema operacional foi redimensionado, use `df -h`. A saída de exemplo a seguir mostra que a partição primária (*/dev/sda1*) agora tem 50 GB:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Próximas etapas
Se precisar de armazenamento adicional, também [adicione discos de dados a uma VM do Linux](add-disk.md). Para obter mais informações sobre a criptografia de disco, consulte [Criptografar discos em uma VM do Linux usando a CLI do Azure](encrypt-disks.md).
