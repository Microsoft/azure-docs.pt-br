---
title: Desanexar um disco de dados de uma VM Linux – Azure
description: Saiba como desanexar um disco de dados de uma máquina virtual no Azure usando a CLI do Azure ou o portal do Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7e618475d7d0160bce5aee39c267bb191a3a9f0a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897132"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual Linux

Quando não precisar mais de um disco de dados conectado a uma máquina virtual, você poderá desanexá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento. Neste artigo, estamos trabalhando com uma distribuição do Ubuntu LTS 16.04. Se estiver usando uma distribuição diferente, as instruções para desmontar o disco poderão ser diferentes.

> [!WARNING]
> Se você desanexar um disco, ele não será excluído automaticamente. Se você se inscreveu para o armazenamento Premium, você continuará incorrendo em encargos de armazenamento para o disco. Para obter mais informações, consulte [Preços e cobrança ao usar o Armazenamento Premium](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Conectar a VM para desmontar o disco

Antes de poder desanexar o disco usando a CLI ou o portal, será necessário desmontar o disco e remover as referências para if do arquivo fstab.

Conecte-se à VM. Neste exemplo, o endereço IP público da VM é *10.0.1.4* com o nome de usuário *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Primeiro, localize o disco de dados que você quer desanexar. O exemplo a seguir usa o dmesg para filtrar em discos SCSI:

```bash
dmesg | grep SCSI
```

A saída deverá ser semelhante ao seguinte exemplo:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Aqui, *sdc* é o disco que queremos destacar. Também é necessário capturar o UUID do disco.

```bash
sudo -i blkid
```

A saída deve ser semelhante ao seguinte exemplo:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Edite o arquivo */etc/fstab* para remover referências ao disco. 

> [!NOTE]
> A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

Abra o arquivo */etc/fstab* em um editor de texto conforme a seguir:

```bash
sudo vi /etc/fstab
```

Neste exemplo, a linha a seguir deve ser excluída do arquivo */etc/fstab*:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Use `umount` para desmontar o disco. O exemplo a seguir desmonta a partição */dev/sdc1* do ponto de montagem */datadrive*:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Desanexar um disco de dados usando a CLI do Azure 

Este exemplo desanexa o disco *myDataDisk* da VM nomeada *myVM* em *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

O disco permanecerá no armazenamento, mas não estará mais conectado a uma máquina virtual.


## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados usando o portal

1. No menu à esquerda, selecione **Máquinas Virtuais**.
1. Na folha da máquina virtual, selecione **Discos**.
1. Na parte superior da folha **Discos**, selecione **Editar**.
1. Na folha **Discos**, mais à direita do disco de dados que você deseja desanexar, clique no botão Desanexar ![Imagem do botão Desanexar](./media/detach-disk/detach.png).
1. Depois que o disco for removido, clique em **Salvar** na parte superior da folha.

O disco permanecerá no armazenamento, mas não estará mais conectado a uma máquina virtual.



## <a name="next-steps"></a>Próximas etapas
Se deseja reutilizar o disco de dados, basta [anexá-lo a outra VM](add-disk.md).

Se você quiser excluir o disco, para que não incorra mais nos custos de armazenamento, consulte [Localizar e excluir discos gerenciados e não-portal do Azure do Azure desconectados](../disks-find-unattached-portal.md).