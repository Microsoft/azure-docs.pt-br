---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 82cbffb257d85197848b8bca14231e5363d6d45c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729828"
---
Algumas cargas de trabalho de banco de dados como o SQL Server ou Oracle exigem alto de memória, armazenamento e largura de banda I/O, mas não número alto de núcleos. Muitas cargas de trabalho do banco de dados não são de uso intensivo de CPU. O Azure oferece determinados tamanhos de VM, onde você pode restringir a contagem de vCPU VM para reduzir o custo de licenciamento de software, mantendo a mesma memória, armazenamento e largura de banda I/O.

A contagem de vCPU pode ser restrita para a metade ou um quarto do tamanho da VM original. Esses novos tamanhos VM têm um sufixo que especifica o número de vCPUs ativos para facilitar a identificação.

Por exemplo, o tamanho da VM atual Standard_GS5 acompanha 32 vCPUs, 448 GB de RAM, 64 discos (até 256 TB) e 80.000 IOPs ou 2 GB/s de largura de banda I/O. Os novos tamanhos de VM Standard_GS5-16 e 8 Standard_GS5 vêm com 8 e 16 vCPUs activas respectivamente, mantendo o restante das especificações de Standard_GS5 para memória, armazenamento e largura de banda I/O.

Os valores de licenciamento cobrados para SQL Server ou Oracle são restritos à nova contagem de vCPU e outros produtos devem ser cobrados com base na contagem de vCPU nova. Isso resulta em um aumento de 50% a 75% na razão entre as especificações VM para vCPUs ativas (Faturável). Esses novos tamanhos VM que só estão disponíveis no Azure, permitindo que as cargas de trabalho enviem por push maior utilização de CPU em uma fração do custo de licenciamento (por núcleo). Neste momento, o custo de computação, que inclui o licenciamento do sistema operacional, permanece o mesmo com o tamanho original. Para obter mais informações, consulte [tamanhos de VM do Azure para cargas de trabalho do banco de dados mais econômicos](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| NOME                | vCPU | Especificações           |
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
| Standard_E32-16_v3  | 16   | O mesmo que E32s_v3 |
| Standard_E32-8s_v3  | 8    | O mesmo que E32s_v3 |
| Standard_E64-32s_v3 | 32   | O mesmo que E64s_v3 |
| Standard_E64-16s_v3 | 16   | O mesmo que E64s_v3 |
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
