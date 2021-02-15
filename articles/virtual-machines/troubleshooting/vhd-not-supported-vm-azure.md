---
title: Não há suporte para VHD quando você cria uma máquina virtual no Azure | Microsoft Docs
description: Este artigo ajuda a corrigir erros de VHD ao executar uma máquina virtual no Microsoft Azure.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: 23f31e8d36d6ba615b21a4043c7d39716fc6d89a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653487"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>Não há suporte para VHD quando você cria uma máquina virtual no Azure

Este artigo ajuda a corrigir erros do VHD ao executar a máquina virtual no Windows ou no Linux.

## <a name="symptoms"></a>Sintomas

Quando você cria uma máquina virtual no Microsoft Azure usando um VHD carregado, a implantação falha e retorna a seguinte mensagem de erro: 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>Causa

Esse problema ocorre por um dos seguintes motivos:

- O VHD não é compatível com o alinhamento de 1 MB (deslocamento). O tamanho de disco com suporte deve ser de 1 MB * N. Por exemplo, o disco deve ter 102.401 MB.
- O VHD está corrompido ou não tem suporte. 

## <a name="resolution"></a>Resolução

> [!NOTE]
> Para executar a correção a seguir, o cliente precisará executar essas etapas antes de carregar o VHD no Azure.

Para resolver esse problema, redimensione o disco para estar em conformidade com o alinhamento de 1 MB:

- Para resolver o problema no Windows, use o [cmdlet redimensionar-VHD do PowerShell](/powershell/module/hyper-v/resize-vhd). Observe que **redimension-VHD** não é um cmdlet Azure PowerShell.

  1. [Instalar a função Hyper-V no Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [Converter o disco virtual em um VHD de tamanho fixo](../windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd)

- Para resolver o problema no Linux, use o [comando QEMU-img](../linux/create-upload-generic.md).

Para obter mais informações sobre como criar e carregar um VHD para criar uma VM do Azure, consulte os seguintes artigos:

- [Carregar e criar uma VM Linux com base em uma imagem de disco personalizada usando a CLI 1.0 do Azure](../linux/upload-vhd.md)
- [Criar e carregar um VHD do Windows Server no Azure](../windows/upload-generalized-managed.md)

A continuação de problemas pode indicar um VHD corrompido. Nessa situação, recomendamos que você reconstrua o VHD do zero.

Para obter mais informações, consulte o seguinte artigo:

- [Sobre o VHD](../managed-disks-overview.md)
