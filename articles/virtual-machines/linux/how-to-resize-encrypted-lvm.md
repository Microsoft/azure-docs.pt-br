---
title: Como redimensionar discos criptografados usando o Azure Disk Encryption
description: Este artigo fornece instruções para redimensionar discos do ADE criptografados usando o gerenciamento de volume lógico.
author: jofrance
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 732a2ef3c6c33d0b17deaf3b9bdc5d1ac5e5934c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498907"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Como redimensionar dispositivos de gerenciamento de volume lógico que usam Azure Disk Encryption

Neste artigo, você aprenderá a redimensionar discos de dados que usam Azure Disk Encryption. Para redimensionar os discos, você usará o LVM (gerenciamento de volume lógico) no Linux. As etapas se aplicam a vários cenários.

Você pode usar esse processo de redimensionamento nos seguintes ambientes:

- Distribuições do Linux:
    - Red Hat Enterprise Linux (RHEL) 7 ou posterior
    - Ubuntu 16 ou posterior
    - SUSE 12 ou posterior
- Azure Disk Encryption versões: 
    - Extensão de passagem única
    - Extensão de passagem dupla

## <a name="prerequisites"></a>Pré-requisitos

Este artigo supõe que você:

- Uma configuração de LVM existente. Para obter mais informações, consulte [Configurar o LVM em uma VM do Linux](/previous-versions/azure/virtual-machines/linux/configure-lvm).

- Discos que já estão criptografados pelo Azure Disk Encryption. Para obter mais informações, consulte [Configurar LVM e RAID em dispositivos criptografados](how-to-configure-lvm-raid-on-crypt.md).

- Experiência com o Linux e o LVM.

- Experiência usando caminhos */dev/disk/scsi1/* para discos de dados no Azure. Para obter mais informações, consulte [solucionar problemas de nome do dispositivo VM Linux](../troubleshooting/troubleshoot-device-names-problems.md). 

## <a name="scenarios"></a>Cenários

Os procedimentos neste artigo se aplicam aos seguintes cenários:

- Configurações tradicionais de LVM e LVM-criptografadas
- Criptografia LVM tradicional 
- LVM em cript 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Configurações tradicionais de LVM e LVM-criptografadas

As configurações de LVM e LVM tradicionais estendem um volume lógico (LV) quando o grupo de volumes (VG) tem espaço disponível.

### <a name="traditional-lvm-encryption"></a>Criptografia LVM tradicional 

Na criptografia LVM tradicional, os LVs são criptografados. O disco inteiro não está criptografado.

Usando a criptografia LVM tradicional, você pode:

- Estenda o LV quando você adicionar um novo volume físico (VP).
- Estenda o LV ao redimensionar um PV existente.

### <a name="lvm-on-crypt"></a>LVM em cript 

O método recomendado para a criptografia de disco é LVM-on-Encrypt. Esse método criptografa todo o disco, não apenas o LV.

Ao usar LVM-on-cript, você pode: 

- Estenda o LV quando você adicionar um novo VP.
- Estenda o LV ao redimensionar um PV existente.

> [!NOTE]
> Não é recomendável misturar criptografia de LVM tradicional e LVM em cript na mesma VM.

As seções a seguir fornecem exemplos de como usar o LVM e o LVM em cript. Os exemplos usam valores preexistentes para discos, PVs, VGs, LVs, sistemas de arquivos, UUIDs (identificadores universalmente exclusivos) e pontos de montagem. Substitua esses valores pelos seus próprios valores para se ajustar ao seu ambiente.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Estender um LV quando o VG tiver espaço disponível

A maneira tradicional de redimensionar o LVs é estender um LV quando o VG tem espaço disponível. Você pode usar esse método para discos não criptografados, volumes tradicionais criptografados por LVM e configurações de LVM em cript.

1. Verifique o tamanho atual do sistema de arquivos que você deseja aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do sistema de arquivos. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Verifique se o VG tem espaço suficiente para aumentar o LV:

    ``` bash
    vgs
    ```

    ![Captura de tela mostrando o código que verifica o espaço no VG. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Você também pode usar `vgdisplay` :

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de tela mostrando o código de exibição V G que verifica o espaço no VG. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Identifique qual LV precisa ser redimensionado:

    ``` bash
    lsblk
    ```

    ![Captura de tela mostrando o resultado do comando l v l k. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Para LVM-on-cript, a diferença é que essa saída mostra que a camada criptografada está no nível do disco.

    ![Captura de tela mostrando o resultado do comando l v l k. A saída é realçada. Ele mostra a camada criptografada.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Verifique o tamanho do LV:

    ``` bash
    lvdisplay lvname
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do volume lógico. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Aumente o tamanho do LV usando `-r` para redimensionar o sistema de arquivos online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Captura de tela mostrando o código que aumenta o tamanho do volume lógico. O comando e os resultados são realçados.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Verifique os novos tamanhos para o LV e o sistema de arquivos:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do LV e do sistema de arquivos. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    A saída de tamanho indica que o LV e o sistema de arquivos foram redimensionados com êxito.

Você pode verificar as informações de LV novamente para confirmar as alterações no nível do LV:

``` bash
lvdisplay lvname
```

![Captura de tela mostrando o código que confirma os novos tamanhos. Os tamanhos são realçados.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Estender um volume LVM tradicional adicionando um novo VP

Quando você precisar adicionar um novo disco para aumentar o tamanho do VG, estenda o volume do LVM tradicional adicionando um novo VP.

1. Verifique o tamanho atual do sistema de arquivos que você deseja aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de tela mostrando o código que verifica o tamanho atual de um sistema de arquivos. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Verifique a configuração atual do PV:

    ``` bash
    pvs
    ```

    ![Captura de tela mostrando o código que verifica a configuração atual do PV. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Verifique as informações de VG atuais:

    ``` bash
    vgs
    ```

    ![Captura de tela mostrando o código que verifica as informações do grupo de volume atual. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Verifique a lista de discos atual. Identifique os discos de dados verificando os dispositivos em */dev/disk/Azure/scsi1/*.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Captura de tela mostrando o código que verifica a lista de discos atual. O comando e os resultados são realçados.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Verifique a saída de `lsblk` : 

    ``` bash
    lsbk
    ```

    ![Captura de tela mostrando o código que verifica a saída de l s b l k. O comando e os resultados são realçados.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Anexe o novo disco à VM seguindo as instruções em [anexar um disco de dados a uma VM do Linux](attach-disk-portal.md).

7. Verifique a lista de discos e observe o novo disco.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Captura de tela mostrando o código que verifica a lista de discos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Captura de tela mostrando o código que verifica a lista de discos usando l s b l k. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Crie um novo VP sobre o novo disco de dados:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Captura de tela mostrando o código que cria um novo VP. O resultado é realçado.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Esse método usa o disco inteiro como um PV sem uma partição. Como alternativa, você pode usar `fdisk` o para criar uma partição e usar essa partição para o `pvcreate` .

9. Verifique se o PV foi adicionado à lista de PV:

    ``` bash
    pvs
    ```

    ![Captura de tela mostrando o código que mostra a lista de volumes físicos. O resultado é realçado.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Estenda o VG adicionando o novo PV a ele:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Captura de tela mostrando o código que estende o grupo de volumes. O resultado é realçado.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Verifique o novo tamanho de VG:

    ``` bash
    vgs
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do grupo de volumes. Os resultados são realçados.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Use `lsblk` para identificar o LV que precisa ser redimensionado:

    ``` bash
    lsblk
    ```

    ![Captura de tela mostrando o código que identifica o volume local que precisa ser redimensionado. Os resultados são realçados.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Estenda o tamanho do LV usando o `-r` para aumentar o sistema de arquivos online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Captura de tela mostrando o código que aumenta o tamanho do sistema de arquivos online. Os resultados são realçados.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Verifique os novos tamanhos do LV e do sistema de arquivos:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de tela mostrando o código que verifica os tamanhos do volume local e do sistema de arquivos. O comando e o resultado são realçados.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Quando a criptografia de dados do Azure é usada em configurações de LVM tradicionais, a camada criptografada é criada no nível LV, não no nível do disco.
    >
    >Neste ponto, a camada criptografada é expandida para o novo disco. O disco de dados real não tem configurações de criptografia no nível da plataforma, portanto, seu status de criptografia não é atualizado.
    >
    >Esses são alguns dos motivos pelos quais o LVM-on-cript é a abordagem recomendada. 

15. Verifique as informações de criptografia do portal:

    ![Captura de tela mostrando informações de criptografia no Portal. O nome do disco e a criptografia são realçados.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Para atualizar as configurações de criptografia no disco, adicione um novo LV e habilite a extensão na VM.
    
16. Adicione um novo LV, crie um sistema de arquivos nele e adicione-o ao `/etc/fstab` .

17. Defina a extensão de criptografia novamente. Desta vez, você carimbará as configurações de criptografia no novo disco de dados no nível da plataforma. Aqui está um exemplo de CLI:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Verifique as informações de criptografia do portal:

    ![Captura de tela mostrando informações de criptografia no Portal. O nome do disco e as informações de criptografia são realçados.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

Depois que as configurações de criptografia forem atualizadas, você poderá excluir o novo LV. Exclua também a entrada do `/etc/fstab` e `/etc/crypttab` que você criou.

![Captura de tela mostrando o código que exclui o novo volume lógico. A guia F S excluída e a guia cript. são realçadas.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

Siga estas etapas para concluir a limpeza:

1. Desmonte o LV:

    ``` bash
    umount /mountpoint
    ```

1. Feche a camada criptografada do volume:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. Exclua o LV:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Estender um volume LVM tradicional redimensionando um PV existente

Alguns cenários de IM, suas limitações podem exigir que você redimensione um disco existente. Aqui está como:

1. Identifique os discos criptografados:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Captura de tela mostrando o código que identifica discos criptografados. Os resultados são realçados.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Captura de tela mostrando código alternativo que identifica discos criptografados. Os resultados são realçados.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Verifique as informações de PV:

    ``` bash
    pvs
    ```

    ![Captura de tela mostrando o código que verifica informações sobre o volume físico. Os resultados são realçados.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Os resultados na imagem mostram que todo o espaço em todos os PVs está sendo usado no momento.

3. Verifique as informações de VG:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Captura de tela mostrando o código que verifica informações sobre o grupo de volumes. Os resultados são realçados.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Verifique os tamanhos de disco. Você pode usar `fdisk` ou `lsblk` para listar os tamanhos de unidade.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Captura de tela mostrando o código que verifica os tamanhos de disco. Os resultados são realçados.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Aqui identificamos quais PVs estão associadas a quais LVs usando `lsblk -fs` . Você pode identificar as associações executando `lvdisplay` .

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Captura de tela mostrando uma maneira alternativa de identificar associações de volume físico com volumes locais. Os resultados são realçados.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    Nesse caso, todas as quatro unidades de dados fazem parte do mesmo VG e um único LV. Sua configuração pode ser diferente.

5. Verifique a utilização atual do sistema de arquivos:

    ``` bash
    df -h /datalvm*
    ```

    ![Captura de tela mostrando o código que verifica a utilização do sistema de arquivos. O comando e os resultados são realçados.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Redimensione os discos de dados seguindo as instruções em [expandir um disco gerenciado do Azure](expand-disks.md#expand-an-azure-managed-disk). Você pode usar o portal, a CLI ou o PowerShell.

    >[!IMPORTANT]
    >Não é possível redimensionar discos virtuais enquanto a VM está em execução. Desaloque sua VM para esta etapa.

7. Inicie a VM e verifique os novos tamanhos usando `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do disco. O resultado é realçado.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    Nesse caso, `/dev/sdd` foi redimensionado de 5 a 20 g.

8. Verifique o tamanho atual do VP:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do P V. O resultado é realçado.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Embora o disco tenha sido redimensionado, o PV ainda tem o tamanho anterior.

9. Redimensione o PV:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Captura de tela mostrando o código que redimensiona o volume físico. O resultado é realçado.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Verifique o tamanho do PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do volume físico. O resultado é realçado.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Aplique o mesmo procedimento para todos os discos que você deseja redimensionar.

11. Verifique as informações de VG.

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de tela mostrando o código que verifica as informações do grupo de volumes. O resultado é realçado.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    O VG agora tem espaço suficiente para ser alocado para o LVs.

12. Redimensione o LV:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Captura de tela mostrando o código que redimensiona o L V. Os resultados são realçados.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Verifique o tamanho do sistema de arquivos:

    ``` bash
    df -h /datalvm2
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do sistema de arquivos. O resultado é realçado.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Estender um volume LVM-on-cript adicionando um novo VP

Você também pode estender um volume LVM-on-cript adicionando um novo VP. Esse método segue com mais detalhes as etapas em [Configurar LVM e RAID em dispositivos criptografados](how-to-configure-lvm-raid-on-crypt.md#general-steps). Consulte as seções que explicam como adicionar um novo disco e configurá-lo em uma configuração LVM em criptografia.

Você pode usar esse método para adicionar espaço a um LV existente. Ou você pode criar um novo VGs ou LVs.

1. Verifique o tamanho atual de seu VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do grupo de volumes. Os resultados são realçados.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Verifique o tamanho do sistema de arquivos e o LV que você deseja expandir:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do volume local. Os resultados são realçados.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do sistema de arquivos. O resultado é realçado.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Adicione um novo disco de dados à VM e identifique-o.

    Antes de adicionar o novo disco, verifique os discos:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Captura de tela mostrando o código que verifica o tamanho dos discos. O resultado é realçado.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Esta é outra maneira de verificar os discos antes de adicionar o novo disco:

    ``` bash
    lsblk
    ```

    ![Captura de tela mostrando um código alternativo que verifica o tamanho dos discos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Para adicionar o novo disco, você pode usar o PowerShell, o CLI do Azure ou o portal do Azure. Para obter mais informações, consulte [anexar um disco de dados a uma VM do Linux](attach-disk-portal.md).

    O esquema de nome de kernel aplica-se ao dispositivo recém-adicionado. Uma nova unidade normalmente é atribuída à próxima letra disponível. Nesse caso, o disco adicionado é `sdd` .

4. Verifique os discos para certificar-se de que o novo disco foi adicionado:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Captura de tela mostrando o código que lista os discos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Captura de tela mostrando o disco recém-adicionado na saída.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Crie um sistema de arquivos na parte superior do disco adicionado recentemente. Corresponda o disco aos dispositivos vinculados `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Captura de tela mostrando o código que cria um sistema de arquivos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Captura de tela mostrando código adicional que cria um sistema de arquivos e corresponde ao disco para os dispositivos vinculados. Os resultados são realçados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Crie um ponto de montagem temporário para o novo disco adicionado:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Adicione o sistema de arquivos criado recentemente ao `/etc/fstab` .

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Monte o sistema de arquivos recém-criado:

    ``` bash
    mount -a
    ```

9. Verifique se o novo sistema de arquivos está montado:

    ``` bash
    df -h
    ```

    ![Captura de tela mostrando o código que verifica se o sistema de arquivos está montado. O resultado é realçado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Captura de tela mostrando código adicional que verifica se o sistema de arquivos está montado. O resultado é realçado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Reinicie a criptografia que você iniciou anteriormente para unidades de dados.

    >[!TIP]
    >Para LVM-on-cript, recomendamos que você use o `EncryptFormatAll` . Caso contrário, você poderá ver uma criptografia dupla enquanto configura discos adicionais.
    >
    >Para obter mais informações, consulte [Configurar LVM e RAID em dispositivos criptografados](how-to-configure-lvm-raid-on-crypt.md).

    Aqui está um exemplo:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Quando a criptografia for concluída, você verá uma camada cript no disco recém-adicionado:

    ``` bash
    lsblk
    ```

    ![Captura de tela mostrando o código que verifica a camada cript. O resultado é realçado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Desmonte a camada criptografada do novo disco:

    ``` bash
    umount ${newmount}
    ```

12. Verifique as informações atuais do PV:

    ``` bash
    pvs
    ```

    ![Captura de tela mostrando o código que verifica informações sobre o volume físico. O resultado é realçado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Crie um VP sobre a camada criptografada do disco. Pegue o nome do dispositivo do `lsblk` comando anterior. Adicione um `/dev/` mapeador na frente do nome do dispositivo para criar o PV:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Captura de tela mostrando o código que cria um volume físico na camada criptografada. Os resultados são realçados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Você verá um aviso sobre como apagar a `ext4 fs` assinatura atual. Esse aviso é esperado. Responda a essa pergunta com `y` .

14. Verifique se o novo PV foi adicionado à configuração LVM:

    ``` bash
    pvs
    ```

    ![Captura de tela mostrando o código que verifica se o volume físico foi adicionado à configuração LVM. O resultado é realçado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Adicione o novo PV ao VG que você precisa aumentar.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Captura de tela mostrando o código que adiciona um volume físico a um grupo de volumes. Os resultados são realçados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Verifique o novo tamanho e o espaço livre do VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de tela mostrando o código que verifica o tamanho e o espaço livre do grupo de volumes. Os resultados são realçados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Observe o aumento da `Total PE` contagem e o `Free PE / Size` .

17. Aumente o tamanho do LV e do sistema de arquivos. Use a `-r` opção em `lvextend` . Neste exemplo, estamos adicionando o espaço total disponível no VG para o LV especificado.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Captura de tela mostrando o código que aumenta o tamanho do volume local e do sistema de arquivos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Siga as próximas etapas para verificar as alterações.

1. Verifique o tamanho do LV:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Captura de tela mostrando o código que verifica o novo tamanho do volume local. Os resultados são realçados.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Verifique o novo tamanho do sistema de arquivos:

    ``` bash
    df -h mountpoint
    ```

    ![Captura de tela mostrando o código que verifica o novo tamanho do sistema de arquivos. O resultado é realçado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Verifique se a camada LVM está na parte superior da camada criptografada:

    ``` bash
    lsblk
    ```

    ![Captura de tela mostrando o código que verifica se a camada LVM está na parte superior da camada criptografada. O resultado é realçado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Se você usar `lsblk` sem opções, verá os pontos de montagem várias vezes. O comando classifica por dispositivo e LVs. 

    Talvez você queira usar o `lsblk -fs` . Nesse comando, `-fs` inverte a ordem de classificação para que os pontos de montagem sejam mostrados uma vez. Os discos são mostrados várias vezes.

    ``` bash
    lsblk -fs
    ```

    ![Captura de tela mostrando código alternativo que verifica se a camada LVM está na parte superior da camada criptografada. O resultado é realçado.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Estender um LVM em um volume criptografado redimensionando um PV existente

1. Identifique os discos criptografados:

    ``` bash
    lsblk
    ```

    ![Captura de tela mostrando o código que identifica os discos criptografados. Os resultados são realçados.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Captura de tela mostrando código alternativo que identifica os discos criptografados. Os resultados são realçados.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Verifique as informações de PV:

    ``` bash
    pvs
    ```

    ![Captura de tela mostrando o código que verifica as informações de volumes físicos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Verifique suas informações de VG:

    ``` bash
    vgs
    ```

    ![Captura de tela mostrando o código que verifica as informações dos grupos de volumes. Os resultados são realçados.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Verifique suas informações de LV:

    ``` bash
    lvs
    ```

    ![Captura de tela mostrando o código que verifica as informações do volume local. O resultado é realçado.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Verifique a utilização do sistema de arquivos:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Captura de tela mostrando o código que verifica quanto do sistema de arquivos está sendo usado. Os resultados são realçados.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Verifique os tamanhos dos discos:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Captura de tela mostrando o código que verifica o tamanho dos discos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Redimensione o disco de dados. Você pode usar o portal, a CLI ou o PowerShell. Para obter mais informações, consulte a seção Disk-reresize em [expandir discos rígidos virtuais em uma VM do Linux](expand-disks.md#expand-an-azure-managed-disk). 

    >[!IMPORTANT]
    >Não é possível redimensionar discos virtuais enquanto a VM está em execução. Desaloque sua VM para esta etapa.

8. Verifique os tamanhos dos discos:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Captura de tela mostrando o código que verifica os tamanhos dos discos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Nesse caso, ambos os discos foram redimensionados de 2 GB para 4 GB. Mas o tamanho do sistema de arquivos, LV e PV permanecem os mesmos.

9. Verifique o tamanho atual do VP. Lembre-se de que, em LVM-on-cript, o VP é o `/dev/mapper/` dispositivo, não o `/dev/sd*` dispositivo.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do volume físico atual. Os resultados são realçados.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Redimensione o PV:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Captura de tela mostrando o código que redimensiona o volume físico. Os resultados são realçados.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Verifique o novo tamanho VP:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Captura de tela mostrando o código que verifica o tamanho do volume físico. Os resultados são realçados.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Redimensione a camada criptografada no PV:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Aplique o mesmo procedimento para todos os discos que você deseja redimensionar.

13. Verifique suas informações de VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Captura de tela mostrando o código que verifica as informações do grupo de volumes. Os resultados são realçados.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    O VG agora tem espaço suficiente para ser alocado para o LVs.

14. Verifique as informações de LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Captura de tela mostrando o código que verifica as informações do volume local. Os resultados são realçados.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Verifique a utilização do sistema de arquivos:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de tela mostrando o código que verifica a utilização do sistema de arquivos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Redimensione o LV:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Captura de tela mostrando o código que redimensiona o volume local. Os resultados são realçados.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Aqui, usamos a `-r` opção para também redimensionar o sistema de arquivos.

17. Verifique as informações de LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Captura de tela mostrando o código que obtém informações sobre o volume local. Os resultados são realçados.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Verifique a utilização do sistema de arquivos:

    ``` bash
    df -h /mountpoint
    ```

    ![Captura de tela mostrando o código que verifica a utilização do sistema de arquivos. Os resultados são realçados.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Aplique o mesmo procedimento de redimensionamento a qualquer outro LV que o exija.

## <a name="next-steps"></a>Próximas etapas

[Solucionar problemas Azure Disk Encryption](disk-encryption-troubleshooting.md)