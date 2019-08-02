---
title: Computação de tamanhos de VM do Linux do Azure - acelerados | Microsoft Docs
description: Lista os diferentes tamanhos otimizados para GPU disponíveis para máquinas virtuais Linux no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 64cbcd375840d78916810abf9ccb8478ef9a1359
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708840"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais com GPU otimizadas

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Distribuições e drivers com suporte

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam o Linux, instale os drivers de GPU NVIDIA. A [Extensão de Driver de GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como a CLI do Azure ou os modelos do Azure Resource Manager. Confira a [documentação da Extensão de Driver de GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) para saber quais são as distribuições com suporte e as etapas de implantação. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](../extensions/overview.md).

Se você optar por instalar os drivers de GPU NVIDIA manualmente, confira [Instalação do driver de GPU da série N para Linux](n-series-driver-setup.md) para saber quais são as distribuições e os drivers com suporte e as etapas de instalação e verificação.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Não recomendamos instalar o X server nem outros sistemas que usem o `Nouveau` driver em VMs do Ubuntu NC. Antes de instalar os drivers de GPU NVIDIA, você precisa desabilitar o driver `Nouveau`.  

## <a name="other-sizes"></a>Outros tamanhos
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.