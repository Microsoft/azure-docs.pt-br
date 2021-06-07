---
title: Desempenho do tamanho da VM da série HBv2
description: Saiba mais sobre os resultados de testes de desempenho para tamanhos de VM da série HBv2 no Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6edaed1011b867cac9920a19be6bb5fb7157e16f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721226"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>Tamanhos de máquina virtual da série HBv2

Vários testes de desempenho foram executados em VMs de tamanho da [série HBv2](../../hbv2-series.md) . A seguir estão alguns dos resultados deste teste de desempenho.


| Carga de trabalho                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Tríade de fluxo                                    | 350 GB/s (21-23 GB/s por CCX)                                     |
| High-Performance Linpack (HPL)                  | 4 TeraFLOPS (Rpeak, FP64), 8 TeraFLOPS (RMAX, FP32)               |
| Largura de banda de & de latência RDMA                        | 1,2 microssegundos, 190 GB/s                                        |
| FIO no SSD de NVMe local                           | 2,7 GB/s leituras, 1,1 GB/s gravações; leituras de IOPS de 102 mil, 115 gravações de IOPS |
| IOR em 8 * SSD Premium do Azure (P40 Managed Disks, RAID0) * *  | 1,3 GB/s de leituras, 2,5 GB/gravações; leituras de IOPS de 101k, gravações de IOPS de 105K |


## <a name="mpi-latency"></a>Latência MPI

O teste de latência MPI do OSU MicroBenchMark Suite é executado. Os scripts de exemplo estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="Latência MPI no Azure HB.":::


## <a name="mpi-bandwidth"></a>Largura de banda MPI

O teste de largura de banda MPI do OSU MicroBenchMark Suite é executado. Os scripts de exemplo estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="Largura de banda MPI no Azure HB.":::


## <a name="mellanox-perftest"></a>Adaptador Mellanox Perftest

O [pacote Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) tem muitos testes de InfiniBand, como latência (ib_send_lat) e largura de banda (ib_send_bw). Um exemplo de comando é mostrado abaixo. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Próximas etapas

- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma exibição arquitetônica de nível superior da execução de cargas de trabalho do HPC, consulte [computação de alto desempenho (HPC) no Azure](/azure/architecture/topics/high-performance-computing/).