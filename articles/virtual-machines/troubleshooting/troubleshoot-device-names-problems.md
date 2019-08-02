---
title: Solucionar problemas de mudança de nome do dispositivo de VM Linux no Azure | Microsoft Docs
description: Explica por que os nomes de dispositivo de VM do Linux mudam e como resolver o problema.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 0350b6bdc990ed6c2de60e3e98c3768b18d0d636
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710430"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Solucionar problemas de mudança de nome do dispositivo de VM Linux no Azure

Este artigo explica por que os nomes de dispositivo mudam após a reinicialização de VM Linux ou depois de anexar novamente os discos. O artigo também fornece soluções para esse problema.

## <a name="symptoms"></a>Sintomas
Você pode enfrentar os seguintes problemas ao executar VMs Linux no Microsoft Azure:

- A VM falha em inicializar após uma reinicialização.
- Quando os discos de dados são desanexados e reanexados, os nomes de dispositivos de disco são alterados.
- Um aplicativo ou script que faz referência a um disco usando o nome do dispositivo falha devido à alteração do nome do dispositivo.

## <a name="cause"></a>Causa

Não há garantia que caminhos de dispositivo no Linux serão consistentes entre as reinicializações. Nomes de dispositivo são formados por em números principais (letra) e secundários. Quando o driver de dispositivo de armazenamento do Linux detecta um novo dispositivo, ele atribui ao dispositivo números de dispositivo principais e secundários do intervalo disponível. Quando um dispositivo é removido, os números de dispositivo são liberados para reutilização.

O problema ocorre porque a varredura de dispositivo no Linux agendada pelo subsistema SCSI ocorre de maneira assíncrona. Como resultado, um nome de caminho do dispositivo pode variar entre as reinicializações.

## <a name="solution"></a>Solução

Para resolver esse problema, use nomenclatura persistente. Há quatro maneiras de usar uma nomenclatura persistente: por rótulo de sistema de arquivos, por UUID, por ID ou por caminho. Recomendamos o uso do rótulo do sistema de arquivos ou UUID para VMs Linux do Azure.

A maioria das distribuições fornece os parâmetros `fstab`**nofail** ou **nobootwait**. Esses parâmetros permitem que um sistema inicialize quando a montagem do disco falha na inicialização. Consulte a documentação de sua distribuição para obter mais informações sobre esses parâmetros. Para saber mais sobre como configurar uma VM do Linux para usar um UUID quando você adiciona um disco de dados, consulte [Conectar-se à VM Linux para montar o novo disco](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

Quando o agente Linux do Azure é instalado em uma VM, ele usa regras Udev para construir um conjunto de links simbólicos no caminho /dev/disk/azure. Aplicativos e scripts usam regras Udev para identificar os discos anexados à VM, junto com o tipo de disco e LUNs de disco.

Se você já tiver editado o /etc/fstab de tal forma que sua VM não está sendo inicializado e não for possível SSH à sua VM, você pode usar o [Console Serial da VM](./serial-console-linux.md) inserir [modo de usuário único](./serial-console-grub-single-user-mode.md) e modificar seu fstab.

### <a name="identify-disk-luns"></a>Identificar LUNs de disco

Aplicativos usam LUNs para localizar todos os discos anexados e construir links simbólicos. O agente Linux do Azure inclui regras Udev que configuram links simbólicos de um LUN para os dispositivos:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3

As informações de LUN da conta de convidado do Linux são recuperadas usando `lsscsi` ou uma ferramenta semelhante:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

As informações de LUN de convidado são usadas com os metadados de assinatura do Azure para localizar o VHD no Armazenamento do Azure que contém os dados da partição. Por exemplo, você pode usar a CLI do `az`:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
    [
    {
    "caching": "None",
      "createOption": "empty",
    "diskSizeGb": 1023,
      "image": null,
    "lun": 0,
    "managedDisk": null,
    "name": "testVM-20170619-114353",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
    }
    },
    {
    "caching": "None",
    "createOption": "empty",
    "diskSizeGb": 512,
    "image": null,
    "lun": 1,
    "managedDisk": null,
    "name": "testVM-20170619-121516",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
      }
      }
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Descobrir UUIDs de sistema de arquivos usando o blkid

Aplicativos e scripts leem a saída de `blkid` ou fontes semelhantes de informações, para construir links simbólicos no caminho /dev. A saída mostra os UUIDs de todos os discos que estão anexados à VM, e seus arquivos de dispositivo associados:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

As regras Udev do agente Linux do Azure constroem um conjunto de links simbólicos no caminho /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Os aplicativos usam os links para identificar o dispositivo de disco de inicialização e o disco de recurso (efêmero). No Azure, os aplicativos devem consultar os caminhos /dev/disk/azure/root-part1 ou /dev/disk/azure-resource-part1 para descobrir essas partições.

Se houver partições adicionais na lista `blkid`, elas residirão em um disco de dados. Os aplicativos mantêm o UUID para essas partições e usar um caminho para descobrir o nome do dispositivo no tempo de execução:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Obter as regras mais recentes do armazenamento do Azure

Para obter as regras de Armazenamento do Azure mais recentes, execute os seguintes comandos:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Consulte também

Para obter mais informações, confira os seguintes artigos:

- [Ubuntu: Usando o UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Nomenclatura persistente](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: O que UUIDs podem fazer por você](https://www.linux.com/news/what-uuids-can-do-you)
- [UDev: Introdução ao gerenciamento de dispositivo no sistema Linux moderno](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

