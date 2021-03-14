---
title: Séries Edv4 e Edsv4
description: Especificações para as VMs das séries Ev4, Edv4, Esv4 e Edsv4.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: d6edee1f349b37c274f71c9db7abac35f9c88c4a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557107"
---
# <a name="edv4-and-edsv4-series"></a>Séries Edv4 e Edsv4

As séries Edv4 e Edsv4 são executadas nos processadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) em uma configuração hyper-threaded e são ideais para vários aplicativos empresariais com uso intensivo de memória e contam com até 504 GiB de RAM, com Tecnologia [Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) Turbo Boost 2.0, Tecnologia [Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) Hyper-Threading e [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Eles também dão suporte ao [ &reg; aumento de aprendizado profundo da Intel](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Esses novos tamanhos de VM terão 50% de armazenamento local maior, bem como um IOPS de disco local melhor para leitura e gravação em comparação com os tamanhos de [Ev3/Esv3](./ev3-esv3-series.md) com [VMs Gen2](./generation-2.md). Ele apresenta uma velocidade de clock de Turbo principal de 3,4 GHz. 

## <a name="edv4-series"></a>Série Edv4

Os tamanhos da série Edv4 são executados nos processadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Os tamanhos de máquina virtual Edv4 contam com até 504 GiB de RAM, além de um armazenamento SSD local grande e rápido (de até 2.400 GiB). Essas máquinas virtuais são ideais para aplicativos empresariais com uso intensivo de memória e aplicativos que se beneficiam de armazenamento local de alta velocidade e baixa latência. Você pode anexar armazenamentos em disco SSDs Standard e HDDs Standard às VMs Edv4. 

[ACU](acu.md): 195-210<br>
[Armazenamento Premium](premium-storage-performance.md): sem suporte<br>
[Armazenamento em cache Premium](premium-storage-performance.md): sem suporte<br>
[Migração ao vivo](maintenance-and-updates.md): com suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte (*requer um mínimo de 4 vCPU*)<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): sem suporte <br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | <sup>**</sup> Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24.000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


<sup>**</sup> Esses valores de IOPs podem ser garantidos usando [VMs Gen2](generation-2.md)

## <a name="edsv4-series"></a>Série Edsv4

Os tamanhos da série Edsv4 são executados nos processadores Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Os tamanhos de máquina virtual Edsv4 contam com até 504 GiB de RAM, além de um armazenamento SSD local grande e rápido (de até 2.400 GiB). Essas máquinas virtuais são ideais para aplicativos empresariais com uso intensivo de memória e aplicativos que se beneficiam de armazenamento local de alta velocidade e baixa latência.

[ACU](acu.md): 195-210<br>
[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): com suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): com suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte (*requer um mínimo de 4 vCPU*)<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): com suporte <br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | <sup>**</sup> Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211(500)  | 32000/480  | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936(800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24.000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |
| Standard_E80ids_v4 <sup>2</sup> | 80 | 504 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [Tamanhos limitados de núcleos disponíveis)](./constrained-vcpu.md).

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos: [tipos de disco](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
