---
title: Anexar um disco de dados a uma VM do Linux | Microsoft Docs
description: Utilize o portal para anexar disco de dados novo ou existente a uma VM Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 162857ed1b22edf67b44cb4648607103cf733c7d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671839"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utilize o portal para anexar um disco de dados a uma VM Linux 
Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Linux por meio do portal do Azure. Você também pode [anexar um disco de dados a uma VM do Windows no Portal do Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Antes de anexar discos à sua VM, veja estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Discos anexados às máquinas virtuais são, na verdade, os arquivos. vhd armazenados no Azure. Para obter detalhes, confira a [Introdução aos discos gerenciados](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Depois de anexar o disco, será necessário [conectar a VM Linux para montar o novo disco](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, clique em **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Na página Máquinas Virtuais, em **Essentials**, clique em **Discos**.
   
    ![Abrir configurações de disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Anexar um novo disco

1. No painel **Discos**, clique em **+ Adicionar disco de dados**.
2. Clique no menu suspenso para **Nome** e selecione **Criar disco**:

    ![Criar disco gerenciado do Azure](./media/attach-disk-portal/create-new-md.png)

3. Insira um nome para o disco gerenciado. Examine as configurações padrão, atualize conforme necessário e clique em **Criar**.
   
   ![Analisar configurações de disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Clique em **Salvar** para criar o disco gerenciado e atualizar a configuração da VM:

   ![Salvar novo Azure Managed Disk](./media/attach-disk-portal/confirm-create-new-md.png)

5. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de Dados**. Como os discos gerenciados são um recurso de nível superior, o disco aparece na raiz do grupo de recursos:

   ![Azure Managed Disk no grupo de recursos](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. No painel **Discos**, clique em **+ Adicionar disco de dados**.
2. Clique no menu suspenso para **Nome** para exibir uma lista de discos gerenciados existentes acessíveis à sua assinatura do Azure. Selecione o disco gerenciado a ser anexado:

   ![Anexar um Azure Managed Disk existente](./media/attach-disk-portal/select-existing-md.png)

3. Clique em **Salvar** para anexar o disco gerenciado existente e atualizar a configuração da VM:
   
   ![Salvar atualizações do Azure Managed Disk](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Depois que o Azure anexa o disco à máquina virtual, ele é listado nas configurações de disco da máquina virtual em **Discos de Dados**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Conectar-se à VM do Linux para montar o novo disco
Para participar, formatar e montar o novo disco para que sua VM do Linux possa usá-lo, Secure Shell em sua VM. Para saber mais, confira [Como usar o SSH com o Linux no Azure](mac-create-ssh-keys.md). O exemplo a seguir se conecta a uma VM com a entrada DNS pública de *mypublicdns.westus.cloudapp.azure.com* com o nome de usuário *azureuser*: 

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

Aqui, *sdc* é o disco que queremos. 

### <a name="partition-a-new-disk"></a>Particionar um novo disco
Se você estiver usando um disco existente que contenha dados, pule para montar o disco. Se você estiver anexando um novo disco, precisará particionar o disco.

Particione o disco com `fdisk`. Se o tamanho do disco for 2 tebibytes (TiB) ou maior, então você deve usar o particionamento GPT, você pode usar `parted` para executar o particionamento GPT. Se o tamanho do disco for menor que 2TiB, você pode usar o particionamento MBR ou GPT. Torne-o um disco primário na partição 1 e aceite os outros padrões. O exemplo a seguir inicia o processo `fdisk` em */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Use o `n` comando para adicionar uma nova partição. Neste exemplo, podemos também escolher `p` para um principal de partição e aceite o restante dos valores padrão. A saída será semelhante ao exemplo seguinte:

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

Imprima a tabela de partição digitando `p` e, em seguida, use `w` para gravar a tabela para o disco e sair. O resultado deve ser semelhante ao seguinte exemplo:

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
### <a name="mount-the-disk"></a>Monte o disco
Crie um diretório para montar o novo sistema de arquivos usando o `mkdir`. O exemplo a seguir cria um diretório em */datadrive*:

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

## <a name="next-steps"></a>Próximas etapas
Você também pode [anexar um disco de dados](add-disk.md) utilizando a CLI do Azure.
