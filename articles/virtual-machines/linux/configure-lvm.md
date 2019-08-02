---
title: Configurar o LVM de software em uma máquina virtual que executa o Linux | Microsoft Docs
description: Saiba como configurar o LVM no Linux no Azure.
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: gwallace
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: szark
ms.subservice: disks
ms.openlocfilehash: 0feac4844f5d65119e74713c21f58fb29e3a7956
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671687"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurar o LVM em uma VM Linux no Azure
Este documento discutirá como configurar o LVM (Gerenciador de Volume Lógico) em sua máquina virtual do Azure. O LVM pode ser usado no disco do sistema operacional ou em discos de dados nas VMs do Azure; no entanto, por padrão, a maioria das imagens da nuvem não terá o LVM configurado no disco do sistema operacional. As etapas abaixo se concentrarão na configuração do LVM para seus discos de dados.

## <a name="linear-vs-striped-logical-volumes"></a>Volumes lógicos lineares versus lógicos distribuídos
O LVM pode ser usado para combinar um número de discos físicos em um único volume de armazenamento. Por padrão, geralmente o LVM cria volumes lógicos lineares, o que significa que o armazenamento físico é concatenado. Nesse caso, as operações de leitura/gravação serão normalmente enviadas para um único disco. Por outro lado, também podemos criar volumes lógicos distribuídos em que as leituras e gravações são distribuídas para vários discos contidos no grupo de volumes (semelhante a RAID0). Por motivos de desempenho, é provável que você queira distribuir seus volumes lógicos para que as leituras e gravações utilizem todos os seus discos de dados conectados.

Este documento descreve como combinar diversos discos de dados em um único grupo de volumes e, em seguida, criar um volume lógico distribuído. As etapas abaixo são generalizadas para trabalhar com a maioria das distribuições. Na maioria dos casos, os utilitários e os fluxos de trabalho para o gerenciamento de LVM no Azure não são fundamentalmente diferentes de outros ambientes. Como de costume, consulte também o seu fornecedor de Linux para documentação e melhores práticas para usar LVM com sua distribuição particular.

## <a name="attaching-data-disks"></a>Anexando discos de dados
Geralmente, as pessoas desejam começar com dois ou mais discos de dados vazios ao usar LVM. Com base em suas necessidades de E/S, você pode optar por anexar discos que serão armazenados em nosso armazenamento padrão, com até 500 E/S/s por disco, ou em nosso armazenamento Premium, com até 5.000 E/S/s por disco. Este artigo não se aprofundará em detalhes sobre como provisionar e anexar discos de dados para uma máquina virtual Linux. Confira o artigo [anexar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do Microsoft Azure para obter instruções detalhadas sobre como anexar um disco de dados vazio a uma máquina virtual do Linux no Azure.

## <a name="install-the-lvm-utilities"></a>Instalar os utilitários do LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS e Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 e openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    No SLES11, você também deve editar `/etc/sysconfig/lvm` e definir `LVM_ACTIVATED_ON_DISCOVERED` para "ativar":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Configurar o LVM
Neste guia, supomos que você tem três discos de dados anexados, que chamaremos de `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Esses caminhos podem não corresponder aos nomes do caminho do disco na sua VM. Você pode executar o '`sudo fdisk -l`' ou um comando semelhante para listar os discos disponíveis.

1. Prepare os volumes físicos:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Crie um grupo de volumes. Neste exemplo, chamamos o grupo de volumes `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Crie os volumes lógicos. O comando abaixo criará um único volume lógico chamado `data-lv01` para abranger o grupo de volumes completo, mas observe que também é possível criar vários volumes lógicos no grupo de volumes.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatar o volume lógico

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Com o SLES11, use `-t ext3` em vez de ext4. O SLES11 só oferece suporte a acesso somente leitura para sistemas de arquivos ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de arquivos a /etc/fstab
> [!IMPORTANT]
> A edição inadequada do arquivo `/etc/fstab` pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo `/etc/fstab` seja criado antes da edição.

1. Crie o ponto de montagem desejado para o novo sistema de arquivos, por exemplo:

    ```bash  
    sudo mkdir /data
    ```

2. Localizar o caminho do volume lógico

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Abra o `/etc/fstab` em um editor de texto e adicione uma entrada para o novo sistema de arquivos, por exemplo:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Em seguida, salve e feche o `/etc/fstab`.

4. Testar se a entrada `/etc/fstab` está correta:

    ```bash    
    sudo mount -a
    ```

    Se este comando resultar em uma mensagem de erro, verifique a sintaxe no arquivo `/etc/fstab`.
   
    Em seguida, execute o comando `mount` para garantir que o sistema de arquivos esteja montado:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Opcional) Parâmetros de inicialização à prova de falhas em `/etc/fstab`
   
    Várias distribuições incluem os parâmetros de montagem `nobootwait` ou `nofail` que podem ser adicionados ao arquivo `/etc/fstab`. Esses parâmetros permitem falhas ao montar um sistema de arquivos específico e permitem que o sistema Linux continue a inicialização, mesmo que não seja possível montar corretamente o sistema de arquivos RAID. Veja a documentação da distribuição para obter mais informações sobre esses parâmetros.
   
    Exemplo (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Suporte a TRIM/UNMAP
Alguns kernels Linux permitem operações TRIM/UNMAP para descartar os blocos não utilizados no disco. Essas operações são úteis principalmente no Armazenamento Standard, para informar o Azure de que as páginas excluídas não são mais válidas e podem ser descartadas. Descartar páginas poderá representar uma economia de dinheiro se você criar arquivos grandes e, em seguida, excluí-los.

Há duas maneiras de habilitar o suporte a TRIM em sua VM do Linux. Como de costume, consulte sua distribuição para obter a abordagem recomendada:

- Use a opção de montagem `discard` em `/etc/fstab`, por exemplo:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos a opção `discard` pode afetar o desempenho. Como alternativa, você pode executar o comando `fstrim` manualmente na linha de comando ou adicioná-lo a crontab para ser executado normalmente:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
