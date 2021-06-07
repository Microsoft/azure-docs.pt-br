---
title: Como mapear discos do Azure para discos convidados da VM do Linux
description: Como determinar os discos do Azure que underlay os discos convidados de uma VM Linux.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.collection: linux
ms.openlocfilehash: bc6c6273ab3d1a4403763e4ed0a8c491995fb2df
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556716"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Como mapear discos do Azure para discos convidados da VM do Linux

Talvez seja necessário determinar os discos do Azure que retornam os discos convidados de uma VM. Em alguns cenários, você pode comparar o tamanho do disco ou do volume com o tamanho dos discos do Azure anexados. Em cenários em que há vários discos do Azure com o mesmo tamanho anexado à VM, você precisa usar o LUN (número de unidade lógica) dos discos de dados. 

## <a name="what-is-a-lun"></a>O que é um LUN?

Um LUN (número de unidade lógica) é um número usado para identificar um dispositivo de armazenamento específico. Cada dispositivo de armazenamento recebe um identificador numérico exclusivo, começando em zero. O caminho completo para um dispositivo é representado pelo número de barramento, número de ID de destino e LUN (número de unidade lógica). 

Por exemplo: ***número de barramento 0, ID de destino 0, LUN 3***

Para nosso exercício, você só precisa usar o LUN.

## <a name="finding-the-lun"></a>Encontrando o LUN

Abaixo, listamos dois métodos para localizar o LUN de um disco no Linux.

### <a name="lsscsi"></a>lsscsi

1. Conectar-se à VM
1. `sudo lsscsi`

A primeira coluna listada conterá o LUN, o formato será [host: Channel: target:**LUN**].

### <a name="listing-block-devices"></a>Listando dispositivos de bloco

1. Conectar-se à VM
1. `sudo ls -l /sys/block/*/device`

A última coluna listada conterá o LUN, o formato é [host: Channel: target:**LUN**]

## <a name="finding-the-lun-for-the-azure-disks"></a>Encontrando o LUN para os discos do Azure

Você pode localizar o LUN para um disco do Azure usando o portal do Azure, CLI do Azure.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Localizando um LUN do disco do Azure no portal do Azure

1. No portal do Azure, selecione "máquinas virtuais" para exibir uma lista de suas máquinas virtuais
1. Selecione a Máquina Virtual
1. Selecione "discos"
1. Selecione um disco de dados na lista de discos anexados.
1. O LUN do disco será exibido no painel detalhes do disco. O LUN exibido aqui está correlacionado aos LUNs que você procurou no convidado usando lsscsi ou listando os dispositivos de bloqueio.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Localizando um LUN do disco do Azure usando CLI do Azure

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
