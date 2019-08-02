---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 7bfb96a40dedf780920e6fdd6e08e1b583bea902
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501203"
---
<!-- F-series, Fs-series* -->

Tamanhos de VM otimizados para computação têm uma alta relação de CPU/memória e são bons para servidores da Web com tráfego médio, dispositivos de rede, servidores de aplicativos e processos de lote. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho neste agrupamento.

A série Fsv2 é baseada no processador Intel® Xeon® Platinum 8168, apresentando uma velocidade de clock turbo com todos os núcleos de 3,4 GHz e uma frequência turbo de núcleo único de 3,7 GHz. As instruções Intel® AVX-512, que são novas em Processadores Escalonáveis da Intel, fornecerão um aumento de até duas vezes de desempenho para cargas de trabalho de processamento de vetor em operações de ponto flutuante de precisão simples e dupla. Em outras palavras, eles são muito rápidos para qualquer carga de trabalho computacional. 

A um preço de lista por hora inferior, a série Fsv2 é o melhor valor de preço/desempenho no portfólio do Azure com base na ACU (Unidade de Computação do Azure) por vCPU.

## <a name="fsv2-series-sup1sup"></a>Série Fsv2 <sup>1</sup>

ACU: 195 - 210

Armazenamento Premium:  Com suporte

Cache de armazenamento Premium:  Com suporte

| Tamanho             | da vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência de disco sem cache: IOPS / MBps | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000 / 768 (768)        | 76800 / 1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30000              |

<sup>1</sup> A tecnologia Intel® Hyper-Threading da VM série Fsv2

<sup>2</sup> mais de 64 Vcpus exigem um destes sistemas operacionais convidados com suporte: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux ou CentOS 7.3 ou Oracle Linux 7.3 com LIS 4.2.1

<sup>3</sup> A instância é isolada em hardware dedicado a um único cliente.