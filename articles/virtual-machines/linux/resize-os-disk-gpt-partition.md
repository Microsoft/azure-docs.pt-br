---
title: Redimensionar um disco do sistema operacional com partição GPT
description: Este artigo fornece instruções sobre como redimensionar um disco do sistema operacional que tenha uma partição GPT (tabela de partição GUID) no Linux.
services: virtual-machines
ms.topic: article
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 46b6ceff74dd3a296d26cc018b380c1c3f76664c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552942"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Redimensionar um disco do sistema operacional com partição GPT

> [!NOTE]
> Este artigo se aplica somente a discos do sistema operacional que tenham uma partição GPT (tabela de partição GUID).

Este artigo descreve como aumentar o tamanho de um disco do sistema operacional com GPT no Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Identificar se o disco do sistema operacional tem uma partição MBR ou GPT

Use o `parted` comando para identificar se a partição de disco foi criada com uma partição MBR (registro mestre de inicialização) ou uma partição GPT.

### <a name="mbr-partition"></a>Partição MBR

Na saída a seguir, a **tabela de partição** mostra um valor de **msdos**. Esse valor identifica uma partição MBR.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Partição GPT

Na saída a seguir, a **tabela de partição** mostra um valor de **gpt**. Esse valor identifica uma partição GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Se sua VM (máquina virtual) tiver uma partição GPT no disco do sistema operacional, aumente o tamanho do disco do sistema operacional.

## <a name="increase-the-size-of-the-os-disk"></a>Aumentar o tamanho do disco do sistema operacional

As instruções a seguir se aplicam a distribuições endossadas pelo Linux.

> [!NOTE]
> Antes de prosseguir, faça uma cópia de backup de sua VM ou tire um instantâneo do disco do sistema operacional.

### <a name="ubuntu"></a>Ubuntu

Para aumentar o tamanho do disco do sistema operacional no Ubuntu 16. *x* e 18. *x*:

1. Pare a VM.
1. Aumente o tamanho do disco do sistema operacional no portal.
1. Reinicie a VM e entre na VM como um usuário **raiz** .
1. Verifique se o disco do sistema operacional agora exibe um tamanho maior do sistema de arquivos.

No exemplo a seguir, o disco do sistema operacional foi redimensionado do portal para 100 GB. O sistema de arquivos **/dev/sda1** montado em **/** mostra agora 97 GB.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Para aumentar o tamanho do disco do sistema operacional no SUSE 12 SP4, SUSE SLES 12 para SAP, SUSE SLES 15 e SUSE SLES 15 para SAP:

1. Pare a VM.
1. Aumente o tamanho do disco do sistema operacional no portal.
1. Reinicie a VM.

Quando a VM for reiniciada, conclua estas etapas:

1. Acesse sua VM como um usuário **raiz** usando este comando:

   ```
   # sudo -i
   ```

1. Use o comando a seguir para instalar o pacote **growpart** , que você usará para redimensionar a partição:

   ```
   # zypper install growpart
   ```

1. Use o `lsblk` comando para localizar a partição montada na raiz do sistema de arquivos ( **/** ). Nesse caso, vemos que a partição 4 do dispositivo **SDA** está montada em **/** :

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Redimensione a partição necessária usando o `growpart` comando e o número da partição determinados na etapa anterior:

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Execute o `lsblk` comando novamente para verificar se a partição foi aumentada.

   A saída a seguir mostra que a partição **/dev/sda4** foi redimensionada para 46,5 GB:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifique o tipo de sistema de arquivos no disco do sistema operacional usando o `lsblk` comando com o `-f` sinalizador:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Com base no tipo de sistema de arquivos, use os comandos apropriados para redimensionar o sistema de arquivos.
   
   Para **xfs**, use este comando:
   
   ```
   #xfs_growfs /
   ```
   
   Saída de exemplo:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Para **ext4**, use este comando:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Verifique o maior tamanho do sistema de arquivos para o **DF-ésimo** usando este comando:
   
   ```
   #df -Thl
   ```
   
   Saída de exemplo:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   No exemplo anterior, podemos ver que o tamanho do sistema de arquivos para o disco do sistema operacional aumentou.

### <a name="rhel-with-lvm"></a>RHEL com LVM

1. Acesse sua VM como um usuário **raiz** usando este comando:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Use o `lsblk` comando para determinar qual volume lógico (LV) está montado na raiz do sistema de arquivos ( **/** ). Nesse caso, vemos que o **rootvg-rootlv** está montado em **/** . Se você quiser outro sistema de arquivos, substitua o LV e o ponto de montagem ao longo deste artigo.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Verifique se há espaço livre no grupo de volumes LVM (VG) que contém a partição raiz. Se houver espaço livre, pule para a etapa 12.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   Neste exemplo, o **PE/tamanho** de linha livre mostra que há 38, 2 GB livres no grupo de volumes. Você não precisa redimensionar o disco antes de adicionar espaço ao grupo de volumes.

1. Para aumentar o tamanho do disco do sistema operacional no RHEL 7. *x* com LVM:

   1. Pare a VM.
   1. Aumente o tamanho do disco do sistema operacional no portal.
   1. Inicie a VM.

1. Quando a VM for reiniciada, conclua a seguinte etapa:

   - Instale o pacote **Cloud-utils-growpart** para fornecer o comando **growpart** , que é necessário para aumentar o tamanho do disco do sistema operacional e o manipulador GDisk para layouts de disco GPT. Esses pacotes são pré-instalados na maioria das imagens do Marketplace.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Determine qual disco e partição contém o volume físico LVM ou volumes (VP) no grupo de volumes denominado **rootvg** usando o `pvscan` comando. Observe o tamanho e o espaço livre listados entre os colchetes (**[** e **]**).

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Verifique o tamanho da partição usando `lsblk` . 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Expanda a partição que contém esse VP usando `growpart` o, o nome do dispositivo e o número da partição. Isso expandirá a partição especificada para usar todo o espaço contíguo livre no dispositivo.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Verifique se a partição foi redimensionada para o tamanho esperado usando o `lsblk` comando novamente. Observe que, no exemplo **sda4** foi alterado de 63 gb para 95 GB.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Expanda o VP para usar o restante da partição recentemente expandida:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Verifique se o novo tamanho de VP é o tamanho esperado, comparando-o aos valores originais **[tamanho/livre]** :

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Expanda o volume lógico desejado (LV) pelo valor desejado. O valor não precisa ser todo o espaço livre no grupo de volumes. No exemplo a seguir, **/dev/mapper/rootvg-rootlv** é redimensionado de 2 GB para 12 GB (um aumento de 10 GB). Este comando também redimensionará o sistema de arquivos.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Saída de exemplo:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. O `lvresize` comando chama automaticamente o comando de redimensionamento apropriado para o sistema de arquivos no LV. Verifique se **/dev/mapper/rootvg-rootlv**, que está montado em **/** , tem um tamanho de sistema de arquivos maior usando este comando:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Saída de exemplo:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Para usar o mesmo procedimento para redimensionar qualquer outro volume lógico, altere o nome LV na etapa 12.

### <a name="rhel-raw"></a>RHEL BRUTO
>[!NOTE] 
>Sempre tire um instantâneo da VM antes de aumentar o tamanho do disco do sistema operacional.

Para aumentar o tamanho do disco do sistema operacional em uma partição do RHEL RAW:

1. Pare a VM.
1. Aumente o tamanho do disco do sistema operacional no portal.
1. Inicie a VM.

Quando a VM reiniciar, execute as etapas a seguir:

1. Acesse sua VM como usuário **raiz** usando o comando a seguir:
 
   ```
   sudo su
   ```

1. Instale o pacote **gptfdisk** necessário para aumentar o tamanho do disco do sistema operacional.

   ```
   yum install gdisk -y
   ```

1.  Para ver todos os setores disponíveis no disco, execute o seguinte comando:
    ```
    gdisk -l /dev/sda
    ```

1. Você verá os detalhes informando o tipo de partição. Verifique se ele é GPT. Identifique a partição raiz. Não altere nem exclua a partição de inicialização (partição de inicialização do BIOS) e a partição do sistema (' partição do sistema EFI ')

1. Use o comando a seguir para iniciar o particionamento pela primeira vez. 
    ```
    gdisk /dev/sda
    ```

1. Agora, você verá uma mensagem solicitando o próximo comando (' Command:? para obter ajuda '). 

   ```
   w
   ```

1. Você receberá um aviso informando "aviso! O cabeçalho secundário é colocado muito cedo no disco! Deseja corrigir esse problema? (S/N): ". Você precisa pressionar ' Y '

   ```
   Y
   ```

1. Você deverá ver uma mensagem informando que as verificações finais foram concluídas e solicitando confirmação. Pressione ' Y '

   ```
   Y
   ```

1. Verificar se tudo aconteceu corretamente usando o comando partprobe

   ```
   partprobe
   ```

1. As etapas acima garantiram que o cabeçalho GPT secundário seja colocado no final. A próxima etapa é iniciar o processo de redimensionamento usando a ferramenta GDisk novamente. Use o comando a seguir.

   ```
   gdisk /dev/sda
   ```
1. No menu comando, pressione ' p ' para ver a lista de partições. Identificar a partição raiz (nas etapas, sda2 é considerado como a partição raiz) e a partição de inicialização (nas etapas, sda3 é considerado como a partição de inicialização) 

   ```
   p
   ```
    ![Partição raiz e partição de inicialização](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Pressione ' d' para excluir a partição e selecione o número da partição atribuído para inicialização (neste exemplo, é ' 3 ')
   ```
   d
   3
   ```
1. Pressione ' d' para excluir a partição e selecione o número da partição atribuído para inicialização (neste exemplo, é ' 2 ')
   ```
   d
   2
   ```
    ![Excluir partição raiz e partição de inicialização](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Para recriar a partição raiz com um tamanho maior, pressione ' n', insira o número da partição que você excluiu anteriormente para a raiz (' 2 ' para este exemplo) e escolha o primeiro setor como ' valor padrão ', último setor como ' último valor do setor-tamanho da inicialização ' (' 4096 neste caso ' correspondente à inicialização de 2MB) e código hexadecimal como ' 8300 '
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. Para recriar a partição de inicialização, pressione ' n', insira o número da partição que você excluiu anteriormente para a inicialização (' 3 ' para este exemplo) e escolha o primeiro setor como ' valor padrão ', último setor como ' valor padrão ' e código hex como ' EF02 '
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Grave as alterações com o comando ' w ' e pressione ' Y ' para confirmar
   ```
   w
   Y
   ```
1. Execute o comando ' partprobe ' para verificar a estabilidade do disco
   ```
   partprobe
   ```
1. A reinicialização da VM e o tamanho da partição raiz teria sido aumentado
   ```
   reboot
   ```

   ![Nova partição raiz e partição de inicialização](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Execute o comando xfs_growfs na partição para redimensioná-lo
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS crescimento FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)


1. Verifique se o novo tamanho é refletido usando o comando **DF** :

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
