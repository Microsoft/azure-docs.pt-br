---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 7984172c2b66f2b09e31c646b111e4b9d04fce2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344235"
---
Os tamanhos de VM otimizados para memória oferecem uma taxa de memória alta para CPU que são ideais para servidores de banco de dados relacionais, caches médio a grande e análises in-memory. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho neste agrupamento. 

* A série M oferece a contagem de vCPU mais alta (até 128 vCPUs) e a memória maior (até 3,8 TiB) de qualquer máquina virtual na nuvem.  Ele é ideal para bancos de dados muito grandes ou outros aplicativos que se beneficiam de altas contagens de vCPU e de grandes quantidades de memória.

* Série Dv2, G-series e as equivalentes DSv2/GS são ideais para aplicativos que exigem CPUs mais rápidas, melhor desempenho de armazenamento temporário, ou que têm maior demanda de memória. Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.

* A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ele se baseia na última geração 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) de 2.4 GHz ou E5 2673 v4 2.3 GHz (Broadwell) processadores e com a Intel Turbo Boost Technology 2.0, pode chegar a até 3.1 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

* A série Ev3 inclui o processador E5-2673 v4 2.3 GHz (Broadwell) em uma configuração hyper-threading, fornecendo uma melhor proposta de valor para cargas de trabalho de uso mais geral e levando a Ev3 para o alinhamento com as VMs de uso geral da maioria das outras nuvens.  A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites de rede e disco foram ajustados em uma base por núcleo para alinhar com a mudança para o hyperthreading.  A série Ev3 é o acompanhamento até os tamanhos de VM de memória alta das famílias D/Dv2.

* A Computação do Azure oferece tamanhos de máquina virtual Isolada, para um tipo de hardware específico e dedicada a um único cliente.  Esses tamanhos de máquina virtual são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de outros clientes, para cargas de trabalho que envolvem elementos como requisitos normativos e de conformidade.  Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais Isoladas usando [o Suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Consulte as tabelas de famílias de máquina virtual abaixo para ver as opções de VM isoladas.

## <a name="esv3-series"></a>Série Esv3 

ACU: 160-190 <sup>1</sup>

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

As instâncias ESv3-series são baseadas no processador Intel XEON ® E5-2673 v4 (Broadwell) de 2.3 GHz e podem atingir 3.5 GHz com a Tecnologia Intel Turbo Boost 2.0, e utilizam armazenamento premium. As instâncias Ev3-series são ideais para aplicativos empresariais com uso intensivo de memória.


| Tamanho             | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4,000 / 32 (50)                                                       | 3.200 / 48                                | 2 / 1,000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8,000 / 64 (100)                                                      | 6.400 / 96                                | 2 / 2,000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16,000 / 128 (200)                                                    | 12.800 / 192                              | 4 / 4,000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32,000 / 256 (400)                                                    | 25.600 / 384                              | 8 / 8,000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40.000/320 (400)                                                    | 32.000/480                              | 8/10.000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64,000 / 512 (800)                                                    | 51.200 / 768                              | 8 / 16.000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / 30,000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / 30,000                             |


<sup>1</sup> A tecnologia Intel® Hyper-Threading da VM Esv3-series.

<sup>2</sup> Tamanhos limitados de núcleos disponíveis.

<sup>3</sup> A instância é isolada em hardware dedicado a um único cliente.


## <a name="ev3-series"></a>Ev3-series 

ACU: 160 - 190 <sup>1</sup>

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

As instâncias Ev3-series são baseadas no processador Intel XEON ® E5-2673 v4 (Broadwell) de 2.3 GHz e podem atingir 3.5 GHz com a Tecnologia Intel Turbo Boost 2.0. As instâncias Ev3-series são ideais para aplicativos empresariais com uso intensivo de memória.

O armazenamento do disco de dados é faturado separadamente das máquinas virtuais. Para usar discos de armazenamento premium, use os tamanhos ESv3. Os medidores de cobrança e preço para os tamanhos Esv3 são os mesmos da Ev3-series. 


| Tamanho            | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | NICs máximas / largura de banda da rede |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1,000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2,000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4,000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8,000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8/10.000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16.000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30,000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30,000           |

<sup>1</sup> A tecnologia Intel® Hyper-Threading da VM Ev3-series.

<sup>2</sup> Tamanhos limitados de núcleos disponíveis.

<sup>3</sup> A instância é isolada em hardware dedicado a um único cliente.


## <a name="m-series"></a>Série M 

ACU: 160-180 <sup>1</sup>

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

Acelerador de Gravação:  [Com suporte](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Tamanho            | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10.000 / 100 (793)  | 5.000  / 125 | 4 / 2.000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20.000 / 200 (1,587) | 10.000 / 250 | 8 / 4.000 |
| Standard_M32ts | 32 | 192    | 1.024 | 32 | 40.000 / 400 (3.174) | 20.000 / 500 | 8 / 8,000 |
| Standard_M32ls | 32 | 256    | 1.024 | 32 | 40.000 / 400 (3.174) | 20.000 / 500 | 8 / 8,000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1.024 | 32 | 40.000 / 400 (3.174) | 20.000 / 500 | 8 / 8,000 |
| Standard_M64s  | 64 | 1.024   | 2.048 | 64 | 80.000 / 800 (6.348)| 40.000 / 1.000 | 8 / 16.000          |
| Standard_M64ls  | 64 | 512    | 2.048 | 64 | 80.000 / 800 (6.348) | 40.000 / 1.000 | 8 / 16.000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1.792 | 2.048 | 64 | 80.000 / 800 (6.348)| 40.000 / 1.000 | 8 / 16.000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2.048        | 4.096  | 64 | 160,000 / 1,600 (12,696) | 80.000 / 2.000                            | 8 / 30,000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3.892  | 4.096 | 64 | 160,000 / 1,600 (12,696) | 80.000 / 2.000                            | 8 / 30,000          |
| Standard_M64   | 64  | 1.024 | 7.168  | 64 | 80.000  / 800  (1.228) | 40.000 / 1.000 | 8 / 16.000 |
| Standard_M64m  | 64  | 1.792 | 7.168  | 64 | 80.000  / 800  (1.228) | 40.000 / 1.000 | 8 / 16.000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2.048 | 14.336 | 64 | 250.000 / 1.600 (2.456) | 80.000 / 2.000 | 8/32.000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3.892 | 14.336 | 64 | 250.000 / 1.600 (2.456) | 80.000 / 2.000 | 8/32.000 |



<sup>1</sup> A tecnologia Intel® Hyper-Threading da VM série M

<sup>2</sup> mais de 64 Vcpus exigem um destes sistemas operacionais convidados com suporte: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux ou CentOS 7.3 ou Oracle Linux 7.3 com LIS 4.2.1.

<sup>3</sup> Tamanhos limitados de núcleos disponíveis.

<sup>4</sup> A instância é isolada em hardware dedicado a um único cliente.
<br>

## <a name="gs-series"></a>Série GS 

ACU: 180 - 240 <sup>1</sup>

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10.000 / 100 (264) |5.000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20.000 / 200 (528) |10.000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40.000 / 400 (1.056) |20.000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80.000 / 800 (2.112) |40.000 / 1.000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160.000 / 1.600 (4.224) |80.000 / 2.000 |8 / 20000 |

<sup>1</sup> A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série GS pode ser limitada pelo número, tamanho e distribuição dos discos anexados. Para ver os detalhes, confira [Projetar para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

<sup>3</sup> Tamanhos limitados de núcleos disponíveis.

<br>

## <a name="g-series"></a>Série G

ACU: 180 - 240

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho         | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1.536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3.072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6.144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.
<br>

## <a name="dsv2-series-11-15"></a>Série DSv2 11-15

ACU: 210 - 250 <sup>1</sup>

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8.000 / 64 (72) |6.400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16.000 / 128 (144) |12.800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32.000 / 256 (288) |25.600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64.000 / 512 (576) |51.200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80.000 / 640 (720) |64.000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DSv2 pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para ver os detalhes, confira [Projetar para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.  
<sup>3</sup> Tamanhos limitados de núcleos disponíveis.  
<sup>4</sup> 25000 Mbps com Rede Acelerada. 

<br>

## <a name="dv2-series-11-15"></a>Série Dv2 11-15

ACU: 210 - 250

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho              | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1.000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.  
<sup>2</sup> 25000 Mbps com Rede Acelerada. 