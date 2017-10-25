---
title: "Desanexar um disco de dados de uma VM Linux – Azure | Microsoft Docs"
description: "Saiba como desanexar um disco de dados de uma máquina virtual no Azure usando a CLI 2.0 ou o portal do Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: 3f29547e1da6028b1e4b91d9e29fd3bcdfe08d50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual Linux

Quando não precisar mais de um disco de dados conectado a uma máquina virtual, você poderá desanexá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento. 

> [!WARNING]
> Se você desanexar um disco, ele não será excluído automaticamente. Se você se inscreveu para o armazenamento Premium, você continuará incorrendo em encargos de armazenamento para o disco. Para obter mais informações, consulte [Preços e cobrança ao usar o Armazenamento Premium](../../storage/common/storage-premium-storage.md#pricing-and-billing). 
> 
> 

Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.  

## <a name="detach-a-data-disk-using-cli-20"></a>Desanexar um disco de dados usando a CLI 2.0

```azurecli
az vm disk detach -g myResourceGroup --vm-name myVm -n myDataDisk
```

O disco permanece no armazenamento mas não esteja conectado a uma máquina virtual.


## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados usando o portal
1. No hub do portal, selecione **máquinas virtuais**.
2. Selecione a máquina virtual que tem o disco de dados que você deseja desanexar e clique em **Parar** para desalocar a VM.
3. Na folha da máquina virtual, selecione **Discos**.
4. Na parte superior da folha **Discos**, selecione **Editar**.
5. Na folha **Discos**, mais à direita do disco de dados que você deseja desanexar, clique no botão Desanexar ![Imagem do botão Desanexar](./media/detach-disk/detach.png).
5. Depois que o disco for removido, clique em Salvar na parte superior da folha.
6. Na folha da máquina virtual, clique em **Visão Geral** e, em seguida, clique no botão **Iniciar** na parte superior da folha para reiniciar a VM.

O disco permanece no armazenamento mas não esteja conectado a uma máquina virtual.








## <a name="next-steps"></a>Próximas etapas
Se deseja reutilizar o disco de dados, basta [anexá-lo a outra VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

