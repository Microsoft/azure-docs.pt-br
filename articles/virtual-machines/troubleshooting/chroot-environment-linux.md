---
title: Ambiente chroot em uma VM de resgate do Linux.
description: Este artigo descreve como solucionar problemas do ambiente chroot na VM (máquina virtual de resgate) no Linux.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: 0d8a030061ef6aa848344152edaa3267ad916e2a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377931"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Ambiente chroot em uma VM de resgate do Linux

Este artigo descreve como solucionar problemas do ambiente chroot na VM (máquina virtual de resgate) no Linux.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16. x && Ubuntu 18. x

1. Pare ou Desaloque a VM afetada.
1. Crie uma imagem de VM de resgate da mesma versão do sistema operacional, no mesmo grupo de recursos (RSG) e no local usando o disco gerenciado.
1. Use o portal do Azure para tirar um instantâneo do disco do sistema operacional da máquina virtual afetada.
1. Crie um disco fora do instantâneo do disco do sistema operacional e anexe-o à VM de resgate.
1. Depois que o disco tiver sido criado, solucione o problema do ambiente chroot na VM de resgate.

   1. Acesse sua VM como o usuário raiz usando o seguinte comando:

      `sudo su -`

   1. Localize o disco usando `dmesg` (o método usado para descobrir o novo disco pode variar). O exemplo a seguir usa **dmesg** para filtrar em discos **SCSI** :

      `dmesg | grep SCSI`

      A saída será semelhante ao exemplo a seguir. Neste exemplo, queremos o disco **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Use os seguintes comandos para acessar o ambiente chroot:

      ```
      mkdir /rescue
      mount /dev/sdc1 /rescue
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Solucionar problemas do ambiente chroot.

   1. Use os seguintes comandos para sair do ambiente do chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue
      ```

      > [!NOTE]
      > Se você receber o erro `unable to unmount /rescue` , adicione a opção-l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Desanexe o disco da VM de resgate e execute uma troca de disco com a VM original.
1. Inicie a VM original e verifique sua conectividade.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/CentOS/Oracle 6. x && Oracle 8. x && RHEL/CentOS 7. x com partições BRUTAs

1. Pare ou Desaloque a VM afetada.
1. Crie uma imagem de VM de resgate da mesma versão do sistema operacional, no mesmo grupo de recursos (RSG) e no local usando o disco gerenciado.
1. Use o portal do Azure para tirar um instantâneo do disco do sistema operacional da máquina virtual afetada.
1. Crie um disco fora do instantâneo do disco do sistema operacional e anexe-o à VM de resgate.
1. Depois que o disco tiver sido criado, solucione o problema do ambiente chroot na VM de resgate.

   1. Acesse sua VM como o usuário raiz usando o seguinte comando:

      `sudo su -`

   1. Localize o disco usando `dmesg` (o método usado para descobrir o novo disco pode variar). O exemplo a seguir usa **dmesg** para filtrar em discos **SCSI** :

      `dmesg | grep SCSI`

      A saída será semelhante ao exemplo a seguir. Neste exemplo, queremos o disco **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Use os seguintes comandos para acessar o ambiente chroot:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Solucionar problemas do ambiente chroot.

   1. Use os seguintes comandos para sair do ambiente do chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/
      umount /rescue
      ```

      > [!NOTE]
      > Se você receber o erro `unable to unmount /rescue` , adicione a opção-l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Desanexe o disco da VM de resgate e execute uma troca de disco com a VM original.
1. Inicie a VM original e verifique sua conectividade.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/CentOS 7. x com LVM

   > [!NOTE]
   > Se a VM original incluir o LVM (Gerenciador de volumes lógicos) no disco do sistema operacional, crie a VM de resgate usando a imagem com partições brutas no disco do sistema operacional.

1. Pare ou Desaloque a VM afetada.
1. Crie uma imagem de VM de resgate da mesma versão do sistema operacional, no mesmo grupo de recursos (RSG) e no local usando o disco gerenciado.
1. Use o portal do Azure para tirar um instantâneo do disco do sistema operacional da máquina virtual afetada.
1. Crie um disco fora do instantâneo do disco do sistema operacional e anexe-o à VM de resgate.
1. Depois que o disco tiver sido criado, solucione o problema do ambiente chroot na VM de resgate.

   1. Acesse sua VM como o usuário raiz usando o seguinte comando:

      `sudo su -`

   1. Localize o disco usando `dmesg` (o método usado para descobrir o novo disco pode variar). O exemplo a seguir usa **dmesg** para filtrar em discos **SCSI** :

      `dmesg | grep SCSI`

      A saída será semelhante ao exemplo a seguir. Neste exemplo, queremos o disco **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Use o seguinte comando para ativar o grupo de volumes lógicos:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Use o `lsblk` comando para recuperar os nomes LVM:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. Use os seguintes comandos para acessar o ambiente chroot:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/mapper/rootvg-optlv /rescue/opt
      mount /dev/sdc2 /rescue/boot/
      mount /dev/sdc1 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Solucionar problemas do ambiente chroot.

   1. Use os seguintes comandos para sair do ambiente do chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue/opt
      umount /rescue
      ```

      > [!NOTE]
      > Se você receber o erro `unable to unmount /rescue` , adicione a opção-l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Desanexe o disco da VM de resgate e execute uma troca de disco com a VM original.
1. Inicie a VM original e verifique sua conectividade.

## <a name="rhel-8x-with-lvm"></a>RHEL 8. x com LVM

   > [!NOTE]
   > Se a VM original incluir o LVM (Gerenciador de volumes lógicos) no disco do sistema operacional, crie a VM de resgate usando a imagem com partições brutas no disco do sistema operacional.

1. Pare ou Desaloque a VM afetada.
1. Crie uma imagem de VM de resgate da mesma versão do sistema operacional, no mesmo grupo de recursos (RSG) e no local usando o disco gerenciado.
1. Use o portal do Azure para tirar um instantâneo do disco do sistema operacional da máquina virtual afetada.
1. Crie um disco fora do instantâneo do disco do sistema operacional e anexe-o à VM de resgate.
1. Depois que o disco tiver sido criado, solucione o problema do ambiente chroot na VM de resgate.

   1. Acesse sua VM como o usuário raiz usando o seguinte comando:

      `sudo su -`

   1. Localize o disco usando `dmesg` (o método usado para descobrir o novo disco pode variar). O exemplo a seguir usa **dmesg** para filtrar em discos **SCSI** :

      `dmesg | grep SCSI`

      A saída será semelhante ao exemplo a seguir. Neste exemplo, queremos o disco **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Use o seguinte comando para ativar o grupo de volumes lógicos:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Use o `lsblk` comando para recuperar os nomes LVM:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. Use os seguintes comandos para acessar o ambiente chroot:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Solucionar problemas do ambiente chroot.

   1. Use os seguintes comandos para sair do ambiente do chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue
      ```

      > [!NOTE]
      > Se você receber o erro `unable to unmount /rescue` , adicione a opção-l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Desanexe o disco da VM de resgate e execute uma troca de disco com a VM original.
1. Inicie a VM original e verifique sua conectividade.

## <a name="oracle-7x"></a>Oracle 7. x

1. Pare ou Desaloque a VM afetada.
1. Crie uma imagem de VM de resgate da mesma versão do sistema operacional, no mesmo grupo de recursos (RSG) e no local usando o disco gerenciado.
1. Use o portal do Azure para tirar um instantâneo do disco do sistema operacional da máquina virtual afetada.
1. Crie um disco fora do instantâneo do disco do sistema operacional e anexe-o à VM de resgate.
1. Depois que o disco tiver sido criado, solucione o problema do ambiente chroot na VM de resgate.

   1. Acesse sua VM como o usuário raiz usando o seguinte comando:

      `sudo su -`

   1. Localize o disco usando `dmesg` (o método usado para descobrir o novo disco pode variar). O exemplo a seguir usa **dmesg** para filtrar em discos **SCSI** :

      `dmesg | grep SCSI`

      A saída será semelhante ao exemplo a seguir. Neste exemplo, queremos o disco **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Use os seguintes comandos para acessar o ambiente chroot:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Solucionar problemas do ambiente chroot.

   1. Use os seguintes comandos para sair do ambiente do chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Se você receber o erro `unable to unmount /rescue` , adicione a opção-l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Desanexe o disco da VM de resgate e execute uma troca de disco com a VM original.
1. Inicie a VM original e verifique sua conectividade.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 para SAP &&  # # SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 para SAP

1. Pare ou Desaloque a VM afetada.
1. Crie uma imagem de VM de resgate da mesma versão do sistema operacional, no mesmo grupo de recursos (RSG) e no local usando o disco gerenciado.
1. Use o portal do Azure para tirar um instantâneo do disco do sistema operacional da máquina virtual afetada.
1. Crie um disco fora do instantâneo do disco do sistema operacional e anexe-o à VM de resgate.
1. Depois que o disco tiver sido criado, solucione o problema do ambiente chroot na VM de resgate.

   1. Acesse sua VM como o usuário raiz usando o seguinte comando:

      `sudo su -`

   1. Localize o disco usando `dmesg` (o método usado para descobrir o novo disco pode variar). O exemplo a seguir usa **dmesg** para filtrar em discos **SCSI** :

      `dmesg | grep SCSI`

      A saída será semelhante ao exemplo a seguir. Neste exemplo, queremos o disco **SDC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Use os seguintes comandos para acessar o ambiente chroot:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc4 /rescue
      mount -o nouuid /dev/sdc3 /rescue/boot/
      mount /dev/sdc2 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Solucionar problemas do ambiente chroot.

   1. Use os seguintes comandos para sair do ambiente do chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Se você receber o erro `unable to unmount /rescue` , adicione a opção-l ao comando umount.
      >
      > Exemplo: `umount -l /rescue`

1. Desanexe o disco da VM de resgate e execute uma troca de disco com a VM original.
1. Inicie a VM original e verifique sua conectividade.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de conexão SSH](troubleshoot-ssh-connection.md)
