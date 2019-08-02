---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: bb945695e0525876e044117e26c239e21d66473f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673216"
---
Esta seção fornece informações sobre as gerações anteriores de tamanhos de máquina virtual. Esses tamanhos ainda podem ser usados, mas as gerações mais recentes estão disponíveis. 

## <a name="f-series"></a>Série F

A série F é baseada no processador 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell), que pode obter velocidades de relógio tão altas quanto 3.1 GHz com o Intel Turbo Boost Technology 2.0. Esse é o mesmo desempenho de CPU que o das VMs da série Dv2.  

As VMs da série F são uma ótima opção para as cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de tanta memória ou armazenamento temporário por vCPU.  Cargas de trabalho como análise, servidores de jogos, servidores Web e de processamento em lote serão beneficiados com o valor da série F.

ACU: 210 - 250

Armazenamento Premium:  Sem suporte

Cache de armazenamento Premium:  Sem suporte

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

Cache de armazenamento Premium:  Com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000 / 32 (12) |3200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16000 / 128 (48) |12800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32000 / 256 (96) |25600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64000 / 512 (192) |51200 / 768 |8 / 12000 |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

<sup>1</sup>A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série Fs pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para saber mais, consulte [Projetar para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Série Ls

A série Ls oferece até 32 vCPUs, usando a [família de processadores Intel® Xeon® E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls obtém o mesmo desempenho de CPU da série G/GS e vem com 8 GiB de memória por vCPU.

A série Ls não é compatível com a criação de um cache local para aumentar o IOPS que pode ser obtido por discos de dados duráveis. A alta taxa de transferência e IOPS do disco local torna as VMs da série Ls ideal para repositórios NoSQL, como Apache Cassandra e MongoDB que replicam dados entre várias VMs para obter persistência no caso de falha de uma única VM.

ACU: 180-240

Armazenamento Premium:  Com suporte

Cache de armazenamento Premium:  Sem suporte
 
| Tamanho          | vCPU | Memória (GiB) | Armazenamento temporário (GiB) | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário (IOPS / MBps) | Taxa de transferência máxima do disco não armazenado em cache (IOPS / MBps) | Máximo de NICs/Largura de banda de rede esperado (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000 / 200 | 5000 / 125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000 / 400 | 10000 / 250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000 / 800 | 20000 / 500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000 / 1600   | 40000 / 1000     | 8 / 20000 | 

A taxa de transferência máxima possível do disco com VMs da série Ls pode ser limitada pelo número, tamanho e divisão dos discos anexados. Para saber mais, consulte [Projetar para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> A instância é isolada em hardware dedicado a um único cliente.

## <a name="nvv2-series-preview"></a>Série NVv2 (versão prévia)

**Recomendação de tamanho mais recente**: [Série NVv3 (visualização)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series-preview-1)

As máquinas virtuais da série NVv2 contam com as GPUs [Tesla M60 NVIDIA](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e a tecnologia NVIDIA GRID com CPUs Intel Broadwell. Essas máquinas virtuais são direcionadas para aplicativos com gráficos acelerados por GPU e áreas de trabalho virtuais em que os clientes querem visualizar dados, simular resultados para exibição, trabalhar no CAD ou renderizar e transmitir conteúdo por streaming. Além disso, essas máquinas virtuais podem executar cargas de trabalho de precisão simples, como codificação e renderização. As máquinas virtuais NVv2 são compatíveis com o Armazenamento Premium e oferecem duas vezes mais memória (RAM) em comparação com a Série NV anterior.  

Cada GPU em instâncias da NVv2 vem com uma licença GRID. Esta licença oferece flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único usuário ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs | Estações de trabalho virtuais | Aplicativos virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 usando CLI e PowerShell

No modelo de implantação clássica, alguns nomes de tamanhos de VM são ligeiramente diferentes na CLI e no PowerShell:

* Standard_A0 é ExtraSmall
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge
