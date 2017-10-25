---
title: Adicionar uma VM Linux usando a CLI do Azure | Microsoft Docs
description: "Saiba como adicionar um disco persistente à sua VM Linux com a CLI do Azure 1.0 e 2.0."
keywords: "máquina virtual do Linux, adicionar disco de recurso"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 3005a066-7a84-4dc5-bdaa-574c75e6e411
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 02/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9db7d300b745001906bdc38769dcbe6e4d7c7b83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-disk-to-a-linux-vm"></a>Adicionar um disco a uma VM do Linux
Este artigo mostra a você como anexar um disco persistente à sua VM para que você possa preservar dados, mesmo que sua VM seja provisionada novamente devido à manutenção ou ao redimensionamento. 


## <a name="use-managed-disks"></a>Usar discos gerenciados
O Azure Managed Disks simplifica o gerenciamento de discos para VMs do Azure por meio do gerenciamento de contas de armazenamento associadas aos discos de VM. Você só precisa especificar o tipo (Premium ou Standard) e o tamanho do disco que você precisa e o Azure criará e gerenciará o disco para você. Para obter mais informações, consulte [Visão geral do Managed Disks](managed-disks-overview.md).


### <a name="attach-a-new-disk-to-a-vm"></a>Anexar um novo disco a uma VM
Se você apenas precisar de um novo disco na sua VM, use o comando [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) com o parâmetro `--new`. Se a VM estiver em uma zona de disponibilidade, o disco será criado automaticamente na mesma zona que a VM. Para obter mais informações, consulte [Visão geral de zonas de disponibilidade](../../availability-zones/az-overview.md). O exemplo a seguir cria um disco chamado *myDataDisk* que tem tamanho de *50* GB:

```azurecli
az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk \
  --new --size-gb 50
```

### <a name="attach-an-existing-disk"></a>Anexar um disco existente 
Em muitos casos, você anexa discos que já foram criados. Para anexar um disco existente, localize a ID do disco e passe-a para o comando [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach). A exemplo a seguir consulta em busca de um disco chamado *myDataDisk* em *myResourceGroup*, em seguida, anexa-o à VM denominada *myVM*:

```azurecli
# find the disk id
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)
az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

A saída é algo semelhante ao seguinte (você pode usar a opção `-o table` para qualquer comando no qual formatar a saída):

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Empty",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 50,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/rasquill-script/providers/Microsoft.Compute/disks/myDataDisk",
  "location": "westus",
  "name": "myDataDisk",
  "osType": null,
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-02T23:35:47.708082+00:00",
  "type": "Microsoft.Compute/disks"
}
```


## <a name="use-unmanaged-disks"></a>Usar discos não gerenciados
Os discos não gerenciados requerem uma sobrecarga adicional para criar e gerenciar as contas de armazenamento subjacentes. Os discos não gerenciados são criados na mesma conta de armazenamento que o disco do SO. Para criar e anexar um disco não gerenciado, use o comando [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk?view=azure-cli-latest#az_vm_unmanaged_disk_attach). O exemplo a seguir anexa um disco não gerenciado de *50* GB à VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Conectar-se à VM do Linux para montar o novo disco
Para participar, formatar e montar o novo disco para que sua VM do Linux possa usá-lo, acesse sua VM do Azure via SSH. Para saber mais, confira [Como usar o SSH com o Linux no Azure](mac-create-ssh-keys.md). O exemplo a seguir se conecta a uma VM com a entrada DNS pública de *mypublicdns.westus.cloudapp.azure.com* com o nome de usuário *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Uma vez conectado a uma VM, você está pronto para anexar um disco. Primeiro, localize o disco usando `dmesg` (o método usado para descobrir o novo disco pode variar). O exemplo a seguir usa dmesg para filtragem em discos *SCSI*:

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

Aqui, *sdc* é o disco que queremos. Particione o disco com `fdisk`, torne-o um disco primário na partição 1 e aceite os outros padrões. O exemplo a seguir inicia o processo `fdisk` em */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

A saída deverá ser semelhante ao seguinte exemplo:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Para criar a partição, digite `p` no prompt, conforme descrito a seguir:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Agora, grave um sistema de arquivos na partição com o comando `mkfs`. Especifique o tipo de sistema de arquivos e o nome do dispositivo. O exemplo a seguir cria um sistema de arquivos *ext4* na partição */dev/sdc1* que foi criada nas etapas anteriores:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

A saída deverá ser semelhante ao seguinte exemplo:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Agora, crie um diretório para montar o novo sistema de arquivos usando o `mkdir`. O exemplo a seguir cria um diretório em */datadrive*:

```bash
sudo mkdir /datadrive
```

Use `mount` para montar então o sistema de arquivos. O exemplo a seguir monta a partição */dev/sdc1* para o ponto de montagem */datadrive*:

```bash
sudo mount /dev/sdc1 /datadrive
```

Para garantir que a unidade seja remontada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo */etc/fstab*. Além disso, é altamente recomendável que o UUID (Identificador Universal Exclusivo) seja usado no */etc/fstab* para fazer referência à unidade e não apenas ao nome do dispositivo (por exemplo, */dev/sdc1*). Se o sistema operacional detectar um erro de disco durante a inicialização, usar o UUID evita que o disco incorreto seja montado em um determinado local. Os discos de dados restantes seriam então atribuídos a essas mesmas IDs de dispositivo. Para localizar o UUID da nova unidade, use o utilitário `blkid`:

```bash
sudo -i blkid
```

A saída deve ser semelhante ao seguinte exemplo:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

Em seguida, abra o arquivo */etc/fstab* em um editor de texto, conforme descrito a seguir:

```bash
sudo vi /etc/fstab
```

Neste exemplo, usamos o valor UUID para o novo dispositivo */dev/sdc1* criado nas etapas anteriores e o ponto de montagem */datadrive*. Adicione a seguinte linha no final do arquivo */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Remover um disco de dados posteriormente sem editar fstab pode fazer com que a VM falhe ao ser inicializada. A maioria das distribuições fornecem as opções de fstab *nofail* e/ou *nobootwait*. Essas opções permitem que um sistema inicialize mesmo se o disco não for montado no momento da inicialização. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.
> 
> A opção *nofail* garante que a VM inicie mesmo que o sistema de arquivos esteja corrompido ou que o disco não exista no momento da inicialização. Sem essa opção, você poderá encontrar um comportamento conforme descrito em [Não é possível conectar-se a uma VM Linux via SSH devido a erros no FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte a TRIM/UNMAP para Linux no Azure
Alguns kernels Linux permitem operações TRIM/UNMAP para descartar os blocos não utilizados no disco. Esse recurso é útil principalmente no Armazenamento Standard, para informar o Azure de que as páginas excluídas não são mais válidas e podem ser descartadas, podendo também economizar dinheiro se você criar arquivos grandes e depois excluí-los.

Há duas maneiras de habilitar o suporte a TRIM em sua VM do Linux. Como de costume, consulte sua distribuição para obter a abordagem recomendada:

* Use a opção de montagem `discard` em */etc/fstab*, por exemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Em alguns casos, a opção `discard` pode afetar o desempenho. Como alternativa, você pode executar o comando `fstrim` manualmente na linha de comando ou adicioná-lo a crontab para ser executado normalmente:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Solucionar problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Próximas etapas
* Lembre-se de que o novo disco não está disponível para a VM, caso seja reinicializado, a menos que você grave essas informações no seu arquivo [fstab](http://en.wikipedia.org/wiki/Fstab) .
* Para garantir que a VM Linux seja configurada corretamente, leia as recomendações em [Otimizar sua VM do Linux no Azure](optimization.md) .
* Expanda a capacidade de armazenamento adicionando mais discos e [configure o RAID](configure-raid.md) para obter desempenho adicional.

