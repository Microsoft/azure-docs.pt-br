---
title: Tamanhos de VM do Azure – gerações anteriores | Microsoft Docs
description: Lista as gerações anteriores de tamanhos disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/01/2020
ms.author: mimckitt
ms.openlocfilehash: 90bc98d63b45e43c9325eed4fe019b18f52d0de8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500284"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Gerações anteriores de tamanhos de máquinas virtuais

Esta seção fornece informações sobre gerações anteriores de tamanhos de máquinas virtuais. Esses tamanhos ainda podem ser usados, mas as gerações mais recentes estão disponíveis.

## <a name="f-series"></a>Série F

A série F é baseada no processador 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell), que pode obter velocidades de relógio tão altas quanto 3.1 GHz com o Intel Turbo Boost Technology 2.0. Esse é o mesmo desempenho de CPU que o das VMs da série Dv2.  

As VMs da série F são uma ótima opção para as cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de tanta memória ou armazenamento temporário por vCPU.  Cargas de trabalho como análise, servidores de jogos, servidores Web e de processamento em lote serão beneficiados com o valor da série F.

ACU: 210 - 250

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>FS-series <sup>1</sup>

A série Fs fornece todas as vantagens da série F, além do armazenamento Premium.

ACU: 210 - 250

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

<sup>1</sup> a taxa de transferência máxima possível do disco (IOPS ou Mbps) com uma VM da série FS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, consulte [design para alto desempenho](premium-storage-performance.md).


## <a name="nvv2-series"></a>Série NVv2

**Recomendação de tamanho mais recente**: [série NVv3](nvv3-series.md)

As máquinas virtuais da série NVv2 contam com as GPUs [Tesla M60 NVIDIA](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e a tecnologia NVIDIA GRID com CPUs Intel Broadwell. Essas máquinas virtuais são direcionadas para aplicativos com gráficos acelerados por GPU e áreas de trabalho virtuais em que os clientes querem visualizar dados, simular resultados para exibição, trabalhar no CAD ou renderizar e transmitir conteúdo por streaming. Além disso, essas máquinas virtuais podem executar cargas de trabalho de precisão simples, como codificação e renderização. As máquinas virtuais NVv2 são compatíveis com o Armazenamento Premium e oferecem duas vezes mais memória (RAM) em comparação com a Série NV anterior.  

Cada GPU em instâncias da NVv2 vem com uma licença GRID. Esta licença oferece flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único usuário ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs | Estações de trabalho virtuais | Aplicativos virtuais |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

## <a name="older-generations-of-virtual-machine-sizes"></a>Gerações mais antigas de tamanhos de máquina virtual

Esta seção fornece informações sobre gerações mais antigas de tamanhos de máquina virtual. Esses tamanhos ainda têm suporte, mas não receberão capacidade adicional. Há tamanhos mais novos ou alternativos que estão geralmente disponíveis. Consulte tamanhos de [máquinas virtuais no Azure](./sizes.md) para escolher os tamanhos de VM que melhor se ajustarão às suas necessidades.  

Para obter mais informações sobre o redimensionamento de uma VM do Linux, consulte [redimensionar uma VM do Linux](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>A Básico  

**Recomendação de tamanho mais recente**: [série Av2](av2-series.md)

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

Os tamanhos da camada básicos são principalmente para as cargas de trabalho de desenvolvimento e outros aplicativos que não requerem o balanceamento de carga, dimensionamento automático ou máquinas virtuais que consomem muita memória.

| Tamanho – Tamanho\Nome | vCPU | Memória|NICs (Máx.)| Tamanho máximo do disco temporário | Máx. de discos de dados (1023 GB cada)| Máx. IOPS (300 por disco) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1 x 300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2 x 300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4 x 300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8 x 300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16 x 300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 usando CLI e PowerShell

No modelo de implantação clássica, alguns nomes de tamanhos de VM são ligeiramente diferentes na CLI e no PowerShell:

* Standard_A0 é ExtraSmall
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge

### <a name="a-series"></a>Séria A  

**Recomendação de tamanho mais recente**: [série Av2](av2-series.md)

ACU: 50-100

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 &nbsp; <sup>1</sup> | 1 | 0,768 | 20 | 1 | 1 x 500 | 2/100 |
| Standard_A1 | 1 | 1,75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3,5  | 135 | 4  | 4x500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16 x 500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4x500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16 x 500 | 4/2000 |

<sup>1</sup> O tamanho A0 está assinado em excesso no hardware físico. Para este tamanho específico somente, outras implantações de clientes podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é descrito a seguir como a linha de base esperada, sujeito a uma variação aproximada de 15%.

<br>

### <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva  

**Recomendação de tamanho mais recente**: [série Av2](av2-series.md)

ACU: 225

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

Os tamanhos A8-A11 e série H também são conhecidos como *instâncias de computação intensiva*. O hardware de datacenter do Azure que executa esses tamanhos é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho). A série de A8-A11 usa Intel Xeon E5-2670 a 2,6 GHz e a série H usa Intel Xeon E5-2667 v3 a 3,2 GHz.  

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | Máximo de NICs|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup>Para os aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR, que fornece latência ultrabaixa e largura de banda alta.  

> [!NOTE]
> As [VMs A8 – a11 estão planejadas para aposentadoria em 3/2021](https://azure.microsoft.com/updates/a8-a11-azure-virtual-machine-sizes-will-be-retired-on-march-1-2021/). É altamente recomendável não criar novas VMs A8 – a11. Migre quaisquer VMs A8-a11 existentes para tamanhos de VM de computação de alto desempenho mais novos e eficientes, como H, HB, HC, HBv2, bem como tamanhos de VM de computação de uso geral, como D, E e F para melhor desempenho-preço. Para obter mais informações, confira o [Guia de migração de HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>Série D  

**Recomendação de tamanho mais recente**: série [Dav4](dav4-dasv4-series.md), [série dv4](dv4-dsv4-series.md) e [série Ddv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3,5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> a família de VMs pode ser executada em uma das seguintes cpus: 2,2 GHz intel xeon® E5-2660 v2, 2,4 GHz intel xeon® E5-2673 v3 (Haswell) ou 2,3 GHz intel xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Série D - otimizado para memória  

**Recomendação de tamanho mais recente**: série [Dav4](dav4-dasv4-series.md), [série dv4](dv4-dsv4-series.md) e [série Ddv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> a família de VMs pode ser executada em uma das seguintes cpus: 2,2 GHz intel xeon® E5-2660 v2, 2,4 GHz intel xeon® E5-2673 v3 (Haswell) ou 2,3 GHz intel xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="preview-dc-series"></a>Visualização: série CC

**Recomendação de tamanho mais recente**: [série DCsv2](dcv2-series.md)

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

A série DC usa a última geração de processador E-2176G Intel XEON de 3.7 GHz com tecnologia de SGX e, com a tecnologia Intel Turbo Boost, pode chegar a até 4,7 GHz. 

| Tamanho          | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> As VMs da série DC são [VMs de geração 2](./generation-2.md#creating-a-generation-2-vm) e só oferecem suporte a `Gen2` imagens.


### <a name="ds-series"></a>Série DS  

**Recomendação de tamanho mais recente**: série [Dasv4](dav4-dasv4-series.md), [série Dsv4](dv4-dsv4-series.md) e [série Ddsv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3,5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> a família de VMs pode ser executada em uma das seguintes cpus: 2,2 GHz intel xeon® E5-2660 v2, 2,4 GHz intel xeon® E5-2673 v3 (Haswell) ou 2,3 GHz intel xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Série DS - otimizado para memória  

**Recomendação de tamanho mais recente**: série [Dasv4](dav4-dasv4-series.md), [série Dsv4](dv4-dsv4-series.md) e [série Ddsv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1, 2</sup>

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, consulte [design para alto desempenho](premium-storage-performance.md).
<sup>2</sup> a família de VMs pode ser executada em uma das seguintes cpus: 2,2 GHz intel xeon® E5-2660 v2, 2,4 GHz intel xeon® E5-2673 v3 (Haswell) ou 2,3 GHz intel xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="ls-series"></a>Série Ls

**Recomendação de tamanho mais recente**: [série Lsv2](lsv2-series.md)

A série Ls oferece até 32 vCPUs, usando a [família de processadores Intel® Xeon® E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls obtém o mesmo desempenho de CPU da série G/GS e vem com 8 GiB de memória por vCPU.

A série Ls não é compatível com a criação de um cache local para aumentar o IOPS que pode ser obtido por discos de dados duráveis. A alta taxa de transferência e o IOPS do disco local tornam as VMs da série ls ideais para repositórios NoSQL, como o Apache Cassandra e o MongoDB, que replicam dados em várias VMs para alcançar a persistência no caso de falha de uma única VM.

ACU: 180-240

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho | vCPU | Memória (GiB) | Armazenamento temporário (GiB) | Discos de dados máximos | Taxa de transferência máxima do armazenamento temporário (IOPS/MBps) | Taxa de transferência máxima do disco não armazenado em cache (IOPS/MBps) | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

A taxa de transferência máxima possível do disco com VMs da série Ls pode ser limitada pelo número, tamanho e divisão dos discos anexados. Para obter detalhes, consulte [design para alto desempenho](premium-storage-performance.md).

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.

### <a name="gs-series"></a>Série GS

**Recomendação de tamanho mais recente**: série [Easv4](eav4-easv4-series.md), série [Esv4](ev4-esv4-series.md), [Edsv4](edv4-edsv4-series.md) e [série M](m-series.md)

ACU: 180 - 240 <sup>1</sup>

Armazenamento Premium:  Com suporte

Cache de Armazenamento Premium:  Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série GS pode ser limitada pelo número, tamanho e distribuição dos discos anexados. Para obter detalhes, consulte [design para alto desempenho](premium-storage-performance.md).

<sup>2</sup> A instância é isolada em hardware dedicado a um único cliente.

<sup>3</sup> Tamanhos limitados de núcleos disponíveis.

<br>

### <a name="g-series"></a>Série G

**Recomendação de tamanho mais recente**: [Eav4](eav4-easv4-series.md), série [Ev4](ev4-esv4-series.md) e série [Edv4](edv4-edsv4-series.md) e [série M](m-series.md)

ACU: 180 - 240

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.
<br>

### <a name="nv-series"></a>Série NV
**Recomendação de tamanho mais recente**: [série NVv3](nvv3-series.md) e [série NVv4](nvv4-series.md)

As máquinas virtuais da série NV têm a tecnologia das GPUs [ Tesla M60 da NVIDIA ](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e NVIDIA GRID para aplicativos acelerados de área de trabalho e áreas de trabalho virtuais, em que os clientes podem visualizar seus dados ou simulações. Os usuários podem visualizar seus fluxos de trabalho com uso intensivo de gráficos em instâncias NV para obter capacidade gráfica superior, além de executar cargas de trabalho de precisão única, como codificação e renderização. As VMs da série NV também são alimentadas por CPUs do Intel Xeon E5-2690 v3 (Haswell).

Cada GPU em instâncias NV vem com uma licença GRID. Esta licença oferece flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único usuário ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

Armazenamento Premium:  Sem suporte

Cache de Armazenamento Premium:  Sem suporte

Migração ao Vivo: Sem suporte

Atualizações de preservação de memória: Sem suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs | Estações de trabalho virtuais | Aplicativos virtuais |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de um cartão M60.
<br>

### <a name="nc-series"></a>Série NC
**Recomendação de tamanho mais recente**: [NC T4 v3-Series](nct4-v3-series.md)

As VMs da série NC são alimentadas pela placa [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) e pelo processador Intel Xeon E5-2690 v3 (Haswell). Os usuários podem processar os dados mais rapidamente aproveitando o CUDA para aplicativos de exploração de energia, simulações de falhas, renderização de traçados de raio, aprendizado profundo e muito mais. A configuração NC24r fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.

[Armazenamento Premium](premium-storage-performance.md): sem suporte<br>
[Armazenamento em cache Premium](premium-storage-performance.md): sem suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = metade de um cartão K80.

*Compatível com RDMA


<br>


### <a name="ncv2-series"></a>Série NCv2
**Recomendação de tamanho mais recente**: [NC T4 v3-Series](nct4-v3-series.md) e [NC V100 v3-Series](ncv3-series.md)

VMs da série NCv2 têm a tecnologia de GPUs NVIDIA Tesla P100. Essas GPUs podem fornecer desempenho computacional 2 vezes maior que da série NC. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho HPC tradicionais como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Monte Carlo, entre outros. Além das GPUs, as VMs da série NCv2 também são alimentadas por CPUs Intel Xeon E5-2690 V4 (Broadwell).

A configuração NC24rs v2 fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.

[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>

> Para essa série de VMs, a cota de vCPU (núcleo) em sua assinatura é inicialmente definida como 0 em cada região. [Solicite um aumento de cota de vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) para esta série em uma [região disponível](https://azure.microsoft.com/regions/services/).
>
| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = um cartão P100.

*Compatível com RDMA

<br>

### <a name="nd-series"></a>Série ND
**Recomendação de tamanho mais recente**: [NDv2-Series](ndv2-series.md) e [NC V100 v3-Series](ncv3-series.md)

As máquinas virtuais da série ND são uma nova adição à família de GPU projetada para cargas de trabalho AI e Deep Learning. Elas oferecem um desempenho excelente para treinamento e Inferência. As instâncias do ND são alimentadas por [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPUs e CPUs Intel Xeon E5-2690 V4 (Broadwell). Essas instâncias oferecem um desempenho excelente para operações de ponto flutuante de precisão simples, para cargas de trabalho de AI que utilizam o Cognitive Toolkit, o TensorFlow, o Caffe e outras estruturas. A série ND também oferece um tamanho de memória de GPU muito maior (24 GB), permitindo usar modelos de rede neural muito maiores. Como a série NC, a série ND oferece uma configuração com uma baixa latência secundária, uma rede com alta taxa de transferência por meio de RDMA e a conectividade InfiniBand, permitindo executar trabalhos de grande escala que abrangem várias GPUs.

[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1 e 2<br>

> Para essa série de VMs, a cota de vCPU (núcleo) por região em sua assinatura é definida inicialmente como 0. [Solicite um aumento de cota de vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) para esta série em uma [região disponível](https://azure.microsoft.com/regions/services/).
>
| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = um cartão de P40.

*Compatível com RDMA

<br>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.