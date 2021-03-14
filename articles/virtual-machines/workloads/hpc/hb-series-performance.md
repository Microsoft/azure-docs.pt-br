---
title: Desempenho do tamanho da VM da série HB
description: Saiba mais sobre os resultados de testes de desempenho para tamanhos de VM da série HB no Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 1f2d24279d3e74774da05eba42c0e916370d4f1c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672164"
---
# <a name="hb-series-virtual-machine-sizes"></a>Tamanhos de máquina virtual da série HB

Vários testes de desempenho foram executados em tamanhos da série HB. A seguir estão alguns dos resultados deste teste de desempenho.

| Carga de trabalho                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Tríade de fluxo                                    | 260 GB/s (32-33 GB/s por CCX)  |
| High-Performance Linpack (HPL)                  | 1.000 GigaFLOPS (Rpeak), 860 GigaFLOPS (RMAX) |
| Largura de banda de & de latência RDMA                        | 1,27 microssegundos, 99,1 GB/s   |
| FIO no SSD de NVMe local                           | 1,7 GB/s leituras, 1,0 GB/s gravações      |  
| IOR em 4 * SSD Premium do Azure (p30 Managed Disks, RAID0) * *  | 725 MB/s leituras, 780 MB/gravações   |


## <a name="mpi-latency"></a>Latência MPI

O teste de latência MPI do OSU MicroBenchMark Suite é executado. Os scripts de exemplo estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="Latência MPI no Azure HB.":::

## <a name="mpi-bandwidth"></a>Largura de banda MPI

O teste de largura de banda MPI do OSU MicroBenchMark Suite é executado. Os scripts de exemplo estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="Largura de banda MPI no Azure HB.":::


## <a name="mellanox-perftest"></a>Adaptador Mellanox Perftest

O [pacote Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) tem muitos testes de InfiniBand, como latência (ib_send_lat) e largura de banda (ib_send_bw). Um exemplo de comando é mostrado abaixo.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Próximas etapas

- Leia sobre os comunicados mais recentes e alguns exemplos e resultados de HPC (computação de alto desempenho) nos [Blogs da comunidade de computação técnica do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma exibição arquitetônica de nível superior da execução de cargas de trabalho do HPC, consulte [computação de alto desempenho (HPC) no Azure](/azure/architecture/topics/high-performance-computing/).
