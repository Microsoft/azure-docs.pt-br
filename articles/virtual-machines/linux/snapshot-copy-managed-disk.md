---
title: Copiar um Disco Gerenciado do Azure para backup | Microsoft Docs
description: "Saiba como criar uma cópia de um Disco Gerenciado do Azure para usar no backup ou na solução de problemas de disco."
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
ms.openlocfilehash: c91367ef11c9d531bebac7c069d2df586607ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Criar uma cópia de um VHD armazenado como um Disco Gerenciado do Azure usando instantâneos gerenciados
Crie um instantâneo de um Disco Gerenciado para backup ou crie um Disco Gerenciado usando o instantâneo e anexe-o a uma máquina virtual de teste para solução de problemas. Um Instantâneo Gerenciado é uma cópia pontual completa de um Disco Gerenciado da VM. Ele cria uma cópia somente leitura do seu VHD e, por padrão, a armazena como um Disco Gerenciado Standard. 

Para saber mais sobre preços, confira [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/managed-disks/). <!--Add link to topic or blog post that explains managed disks. -->

Use o portal do Azure ou a CLI 2.0 do Azure para criar um instantâneo do Disco Gerenciado.

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Usar a CLI 2.0 do Azure para criar um instantâneo

> [!NOTE] 
> O exemplo a seguir exige a CLI 2.0 do Azure instalada e conectada à sua conta do Azure.

As etapas a seguir mostram como obter e criar um instantâneo de um disco de SO gerenciado usando o comando `az snapshot create` com o parâmetro `--source-disk`. O exemplo a seguir pressupõe que exista uma VM chamada `myVM` criada com um disco de SO gerenciado no grupo de recursos `myResourceGroup`.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

A saída deve ser semelhante a esta:

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Usar o Portal do Azure para criar um instantâneo 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Iniciando no canto superior esquerdo, clique em **Novo** e procure **instantâneo**.
3. Na folha Instantâneo, clique em **Criar**.
4. Insira um **Nome** para o instantâneo.
5. Selecione um [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente ou digite o nome de um novo. 
6. Selecione um Local do datacenter do Azure.  
7. Para **Disco de origem**, selecione o Disco Gerenciado para instantâneo.
8. Escolha o **Tipo de conta** a ser usado para armazenar o instantâneo. É recomendável **Standard_LRS**, a menos que você precise dele armazenado em um disco de alto desempenho.
9. Clique em **Criar**.

Se você planeja usar o instantâneo para criar um Disco Gerenciado e anexá-lo a uma VM que precisa ser de alto desempenho, use o parâmetro `--sku Premium_LRS` com o comando `az snapshot create`. Isso cria o instantâneo para que ele seja armazenado como um Disco Gerenciado Premium. Os Discos Gerenciados Premium têm melhor desempenho porque são SSDs (unidades de estado sólido), mas são mais caros que os HDDs (discos rígidos) Standard.


