---
title: Tamanhos de vCPU restritos
description: Lista dos tamanhos de Vm que são compatíveis com uma contagem de vCPU restrita.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/09/2018
ms.author: mimckitt
ms.openlocfilehash: 7faeec8494a908b9aab00be9b63904354b5e0994
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557073"
---
# <a name="constrained-vcpu-capable-vm-sizes"></a>Tamanhos de VM compatíveis com vCPU restrita

Algumas cargas de trabalho de banco de dados como o SQL Server ou Oracle exigem alto de memória, armazenamento e largura de banda I/O, mas não número alto de núcleos. Muitas cargas de trabalho do banco de dados não são de uso intensivo de CPU. O Azure oferece determinados tamanhos de VM, onde você pode restringir a contagem de vCPU VM para reduzir o custo de licenciamento de software, mantendo a mesma memória, armazenamento e largura de banda I/O.

A contagem de vCPU pode ser restrita para a metade ou um quarto do tamanho da VM original. Esses novos tamanhos VM têm um sufixo que especifica o número de vCPUs ativos para facilitar a identificação.

Por exemplo, o tamanho da VM atual Standard_GS5 acompanha 32 vCPUs, 448 GB de RAM, 64 discos (até 256 TB) e 80.000 IOPs ou 2 GB/s de largura de banda I/O. Os novos tamanhos de VM Standard_GS5-16 e 8 Standard_GS5 vêm com 8 e 16 vCPUs activas respectivamente, mantendo o restante das especificações de Standard_GS5 para memória, armazenamento e largura de banda I/O.

Os valores de licenciamento cobrados para SQL Server ou Oracle são restritos à nova contagem de vCPU e outros produtos devem ser cobrados com base na contagem de vCPU nova. Isso resulta em um aumento de 50% a 75% na razão entre as especificações VM para vCPUs ativas (Faturável). Esses novos tamanhos de VM permitem que as cargas de trabalho dos clientes usem a mesma memória, armazenamento e largura de banda de e/s ao mesmo tempo em que otimizam seu custo de licenciamento de software. Neste momento, o custo de computação, que inclui o licenciamento do sistema operacional, permanece o mesmo com o tamanho original. Para obter mais informações, consulte [tamanhos de VM do Azure para cargas de trabalho do banco de dados mais econômicos](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Nome                | vCPU | Especificações           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Mesmo que M8ms    |
| Standard_M8-4ms     | 4    | Mesmo que M8ms    |
| Standard_M16-4ms    | 4    | Mesmo que M16ms   |
| Standard_M16-8ms    | 8    | Mesmo que M16ms   |
| Standard_M32-8ms    | 8    | Mesmo que M32ms   |
| Standard_M32-16ms   | 16   | Mesmo que M32ms   |
| Standard_M64 32ms   | 32   | O mesmo que M64ms   |
| Standard_M64-16ms   | 16   | O mesmo que M64ms   |
| Standard_M128-64ms  | 64   | O mesmo que M128ms  |
| Standard_M128-32ms  | 32   | O mesmo que M128ms  |
| Standard_E4-2s_v3   | 2    | O mesmo que E4s_v3  |
| Standard_E8-4s_v3   | 4    | O mesmo que E8s_v3  |
| Standard_E8-2s_v3   | 2    | O mesmo que E8s_v3  |
| Standard_E16-8s_v3  | 8    | O mesmo que E16s_v3 |
| Standard_E16-4s_v3  | 4    | O mesmo que E16s_v3 |
| Standard_E32 16s_v3 | 16   | O mesmo que E32s_v3 |
| Standard_E32-8s_v3  | 8    | O mesmo que E32s_v3 |
| Standard_E64-32s_v3 | 32   | O mesmo que E64s_v3 |
| Standard_E64-16s_v3 | 16   | O mesmo que E64s_v3 |
| Standard_E4-2s_v4   | 2    | O mesmo que E4s_v4  |
| Standard_E8-4s_v4   | 4    | O mesmo que E8s_v4  |
| Standard_E8-2s_v4   | 2    | O mesmo que E8s_v4  |
| Standard_E16-8s_v4  | 8    | O mesmo que E16s_v4 |
| Standard_E16-4s_v4  | 4    | O mesmo que E16s_v4 |
| Standard_E32-16s_v4 | 16   | O mesmo que E32s_v4 |
| Standard_E32-8s_v4  | 8    | O mesmo que E32s_v4 |
| Standard_E64-32s_v4 | 32   | O mesmo que E64s_v4 |
| Standard_E64-16s_v4 | 16   | O mesmo que E64s_v4 |
| Standard_E4-2ds_v4  | 2    | O mesmo que E4ds_v4 |
| Standard_E8-4ds_v4  | 4    | O mesmo que E8ds_v4 |
| Standard_E8-2ds_v4  | 2    | O mesmo que E8ds_v4 |
| Standard_E16-8ds_v4 | 8    | O mesmo que E16ds_v4|
| Standard_E16-4ds_v4 | 4    | O mesmo que E16ds_v4|
| Standard_E32-16ds_v4| 16   | O mesmo que E32ds_v4|
| Standard_E32-8ds_v4 | 8    | O mesmo que E32ds_v4|
| Standard_E64-32ds_v4| 32   | O mesmo que E64ds_v4|
| Standard_E64-16ds_v4| 16   | O mesmo que E64ds_v4|
| Standard_E4-2as_v4  | 2    | O mesmo que E4as_v4 |
| Standard_E8-4as_v4  | 4    | O mesmo que E8as_v4 |
| Standard_E8-2as_v4  | 2    | O mesmo que E8as_v4 |
| Standard_E16-8as_v4 | 8    | O mesmo que E16as_v4|
| Standard_E16-4as_v4 | 4    | O mesmo que E16as_v4|
| Standard_E32-16as_v4| 16   | O mesmo que E32as_v4|
| Standard_E32-8as_v4 | 8    | O mesmo que E32as_v4|
| Standard_E64-32as_v4| 32   | O mesmo que E64as_v4|
| Standard_E64-16as_v4| 16   | O mesmo que E64as_v4|
| Standard_E96-48as_v4| 48   | O mesmo que E96as_v4|
| Standard_E96-24as_v4| 24   | O mesmo que E96as_v4|
| Standard_GS4-8      | 8    | O mesmo que GS4     |
| Standard_GS4-4      | 4    | O mesmo que GS4     |
| Standard_GS5-16     | 16   | O mesmo que GS5     |
| Standard_GS5-8      | 8    | O mesmo que GS5     |
| Standard_DS11-1_v2  | 1    | O mesmo que DS11_v2 |
| Standard_DS12-2_v2  | 2    | O mesmo que DS12_v2 |
| Standard_DS12-1_v2  | 1    | O mesmo que DS12_v2 |
| Standard_DS13-4_v2  | 4    | O mesmo que DS13_v2 |
| Standard_DS13-2_v2  | 2    | O mesmo que DS13_v2 |
| Standard_DS14-8_v2  | 8    | O mesmo que DS14_v2 |
| Standard_DS14-4_v2  | 4    | O mesmo que DS14_v2 |
| Standard_M416-208s_v2 | 208    | O mesmo que M416s_v2|
| Standard_M416-208ms_v2 | 208    | O mesmo que M416ms_v2 |

## <a name="other-sizes"></a>Outros tamanhos
- [Computação otimizada](./sizes-compute.md)
- [Memória otimizada](./sizes-memory.md)
- [Armazenamento otimizado](./sizes-storage.md)
- [GPU](./sizes-gpu.md)
- [Computação de alto desempenho](./sizes-hpc.md)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como as [ACUs (unidade de computação do Azure)](./acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
