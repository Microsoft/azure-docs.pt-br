---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: d0802cfcf05874044b6e116ba194c16a79f9d309
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541584"
---
Esta seção fornece informações sobre as gerações anteriores de tamanhos de máquina virtual. Esses tamanhos ainda podem ser usados, mas as gerações mais recentes estão disponíveis. 

## <a name="f-series"></a>Série F

A série F é baseada no processador 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell), que pode obter velocidades de relógio tão altas quanto 3.1 GHz com o Intel Turbo Boost Technology 2.0. Esse é o mesmo desempenho de CPU que o das VMs da série Dv2.  

As VMs da série F são uma ótima opção para as cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de tanta memória ou armazenamento temporário por vCPU.  Cargas de trabalho como análise, servidores de jogos, servidores Web e de processamento em lote serão beneficiados com o valor da série F.

ACU: 210 - 250

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho         | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>FS-series <sup>1</sup>

A série Fs fornece todas as vantagens da série F, além do armazenamento Premium.

ACU: 210 - 250

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4.000 / 32 (12) |3.200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8.000 / 64 (24) |6.400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16.000 / 128 (48) |12.800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32.000 / 256 (96) |25.600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64.000 / 512 (192) |51.200 / 768 |8 / 12000 |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

<sup>1</sup>A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série Fs pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para saber mais, consulte [Projetar para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Série Ls

A série Ls oferece até 32 vCPUs, usando a [família de processadores Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls obtém o mesmo desempenho de CPU da série G/GS e vem com 8 GiB de memória por vCPU.

A série Ls não é compatível com a criação de um cache local para aumentar o IOPS que pode ser obtido por discos de dados duráveis. A alta taxa de transferência e IOPS do disco local torna as VMs da série Ls ideal para repositórios NoSQL, como Apache Cassandra e MongoDB que replicam dados entre várias VMs para obter persistência no caso de falha de uma única VM.

ACU: 180-240

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Sem suporte
 
| Tamanho          | vCPU | Memória (GiB) | Armazenamento temporário (GiB) | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário (IOPS / MBps) | Taxa de transferência máxima do disco não armazenado em cache (IOPS / MBps) | Máximo de NICs/Largura de banda de rede esperado (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20.000 / 200 | 5.000 / 125  | 2 / 4.000  | 
| Standard_L8s   | 8  | 64  | 1.388 | 32 | 40.000 / 400 | 10.000 / 250 | 4 / 8.000  | 
| Standard_L16s  | 16 | 128 | 2.807 | 64 | 80,000 / 800 | 20.000 / 500 | 8 / 16.000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5.630 | 64   | 160.000 / 1.600   | 40.000 / 1.000     | 8 / 20.000 | 

A taxa de transferência máxima possível do disco com VMs da série Ls pode ser limitada pelo número, tamanho e divisão dos discos anexados. Para saber mais, consulte [Projetar para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 usando CLI e PowerShell

No modelo de implantação clássica, alguns nomes de tamanhos de VM são ligeiramente diferentes na CLI e no PowerShell:

* Standard_A0 é ExtraSmall
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge
