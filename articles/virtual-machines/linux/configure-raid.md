---
title: Configurar o RAID de software em uma máquina virtual que executa o Linux | Microsoft Docs
description: Saiba como usar mdadm para configurar o RAID no Linux no Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: gwallace
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: a7e6c0b2f260976842a0b3ac1f7f69fa859e2283
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671677"
---
# <a name="configure-software-raid-on-linux"></a>Configurar RAID de software no Linux
É um cenário comum usar o software RAID em máquinas virtuais Linux no Azure para apresentar vários discos de dados anexados como um único dispositivo RAID. Normalmente, isso pode ser usado para melhorar o desempenho e permitir uma taxa de transferência aprimorada em comparação com o uso de apenas um único disco.

## <a name="attaching-data-disks"></a>Anexando discos de dados
Dois ou mais discos de dados vazios são necessárias para configurar um dispositivo RAID.  O principal motivo para a criação de um dispositivo RAID é melhorar o desempenho de E/S em seu disco.  Com base em suas necessidades de E/S, você pode optar por anexar discos que serão armazenados em nosso armazenamento padrão, com até 500 E/S/s por disco, ou em nosso armazenamento Premium, com até 5.000 E/S/s por disco. Este artigo não se aprofunda em detalhes sobre como provisionar e anexar discos de dados para uma máquina virtual Linux.  Confira o artigo [anexar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do Microsoft Azure para obter instruções detalhadas sobre como anexar um disco de dados vazio a uma máquina virtual do Linux no Azure.

## <a name="install-the-mdadm-utility"></a>Instalar o utilitário mdadm
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS & Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES e openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Criar as partições de disco
Neste exemplo, criamos uma única partição de disco em /dev/sdc. A nova partição de disco será chamada /dev/sdc1.

1. Iniciar `fdisk` para começar a criar partições

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. Pressione 'n' no prompt para criar uma **n**ova partição:

    ```bash
    Command (m for help): n
    ```

1. Em seguida, pressione 'p' para criar uma partição **p**rimária:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Pressione '1' para selecionar a partição número 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Selecione o ponto de partida da nova partição ou pressione `<enter>` para aceitar o padrão para colocar a partição no início do espaço livre no disco:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Selecione o tamanho da partição, por exemplo, digite '+10 G' para criar uma partição de 10 gigabytes. Ou pressione `<enter>` para criar uma única partição que abranja toda a unidade:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Em seguida, altere a ID e o **t**ipo da partição da ID padrão '83' (Linux) para a ID 'fd' (Raid automático do Linux):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Finalmente, grave a tabela da partição na unidade e saia do fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Criar a matriz RAID
1. O seguinte exemplo "segmentará" (RAID nível 0) três partições localizadas em três discos de dados separados (sdc1, sdd1, sde1).  Depois da execução desse comando, um novo dispositivo RAID chamado **/dev/md127** é criado. Também é importante lembrar que, se anteriormente esses discos de dados faziam parte de outra matriz RAID desabilitada, talvez seja necessário adicionar o parâmetro `--force` ao comando `mdadm`:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Criar o sistema de arquivos no novo dispositivo RAID
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE e Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** – habilite boot.md e crie mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Pode ser necessária uma reinicialização depois de fazer essas alterações em sistemas SUSE. Esta etapa *não* é necessária no SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de arquivos a /etc/fstab
> [!IMPORTANT]
> Edição inadequada do arquivo /etc/fstab pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

1. Crie o ponto de montagem desejado para o novo sistema de arquivos, por exemplo:

    ```bash
    sudo mkdir /data
    ```
1. Ao editar o /etc/fstab, a **UUID** deve ser usada para fazer referência ao sistema de arquivos em vez do nome do dispositivo.  Use o utilitário `blkid` para determinar a UUID do novo sistema de arquivos:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Abra o /etc/fstab em um editor de texto e adicione uma entrada para o novo sistema de arquivos, por exemplo:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Ou no **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Em seguida, salve e feche o /etc/fstab.

1. Testar se a entrada /etc/fstab está correta:

    ```bash  
    sudo mount -a
    ```

    Se esse comando resultar em uma mensagem de erro, verifique a sintaxe no arquivo /etc/fstab.
   
    Em seguida, execute o comando `mount` para garantir que o sistema de arquivos esteja montado:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Opcional) Parâmetros de inicialização à prova de falhas
   
    **configuração fstab**
   
    Várias distribuições incluem os parâmetros de montagem `nobootwait` ou `nofail` que podem ser adicionados ao arquivo /etc/fstab. Esses parâmetros permitem falhas ao montar um sistema de arquivos específico e permitem que o sistema Linux continue a inicialização, mesmo que não seja possível montar corretamente o sistema de arquivos RAID. Veja a documentação da distribuição para obter mais informações sobre esses parâmetros.
   
    Exemplo (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parâmetros de inicialização do Linux**
   
    Além dos parâmetros acima, o parâmetro de kernel “`bootdegraded=true`” pode permitir que o sistema inicialize mesmo que seja detectado que o RAID esteja danificado ou degradado, por exemplo, se uma unidade de dados for removida inadvertidamente da máquina virtual. Por padrão, isso também pode resultar em um sistema não inicializável.
   
    Consulte a documentação da distribuição sobre como editar parâmetros de kernel corretamente. Por exemplo, em muitas distribuições (CentOS, Oracle Linux e SLES 11) esses parâmetros podem ser adicionados manualmente ao arquivo “`/boot/grub/menu.lst`”.  No Ubuntu, esse parâmetro pode ser adicionado à variável `GRUB_CMDLINE_LINUX_DEFAULT` em “/etc/default/grub”.


## <a name="trimunmap-support"></a>Suporte a TRIM/UNMAP
Alguns kernels Linux permitem operações TRIM/UNMAP para descartar os blocos não utilizados no disco. Essas operações são úteis principalmente no Armazenamento Standard, para informar o Azure de que as páginas excluídas não são mais válidas e podem ser descartadas. Descartar páginas poderá representar uma economia de dinheiro se você criar arquivos grandes e, em seguida, excluí-los.

> [!NOTE]
> O RAID não poderá emitir comandos de descarte se o tamanho da parte da matriz for definido como menor que o padrão (512 KB). Isso ocorre porque a granularidade de desmapeamento no Host também é 512KB. Se você modificou o tamanho da parte da matriz por meio do parâmetro `--chunk=` de mdadm, então, as solicitações TRIM/desmapear podem ser ignorados pelo kernel.

Há duas maneiras de habilitar o suporte a TRIM em sua VM do Linux. Como de costume, consulte sua distribuição para obter a abordagem recomendada:

- Use a opção de montagem `discard` em `/etc/fstab`, por exemplo:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos a opção `discard` pode afetar o desempenho. Como alternativa, você pode executar o comando `fstrim` manualmente na linha de comando ou adicioná-lo a crontab para ser executado normalmente:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
