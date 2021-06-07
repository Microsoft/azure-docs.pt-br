---
title: Desempenho e escalabilidade de tamanhos de VM da série HBv3
description: Saiba mais sobre o desempenho e a escalabilidade dos tamanhos de VM da série HBv3 no Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bf64cfc8ad00fc7f761019ed2fa66089434a96ba
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604763"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Desempenho da máquina virtual da série HBv3

As expectativas de desempenho usando o HPC comum são os seguintes:

| Carga de trabalho                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Tríade de fluxo                                    | 330-350 GB/s (~ 82-86 GB/s por NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) para o tamanho da VM 120-Core               |
| Largura de banda de & de latência RDMA                        | 1,2 microssegundos (1 byte), 192 GB/s (unidirecional)                                        |
| FIO no SSDs de NVMe local (RAID0)                  | leituras de 7 GB/s, gravações de 3 GB/s; leituras de IOPS de 186k, gravações de IOPS de 201k |

## <a name="process-pinning"></a>Fixação de processo

A [fixação de processos](compiling-scaling-applications.md#process-pinning) funciona bem em VMs da série HBv3 porque expõemos o silício subjacente como está para a VM convidada. É altamente recomendável fixar o processo para desempenho e consistência ideais.

## <a name="mpi-latency"></a>Latência MPI

O teste de latência MPI do OSU MicroBenchMark Suite pode ser executado conforme mostrado abaixo. Os scripts de exemplo estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>Largura de banda MPI
O teste de largura de banda MPI do OSU MicroBenchMark Suite pode ser executado conforme mostrado abaixo. Os scripts de exemplo estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Adaptador Mellanox Perftest
O [pacote Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) tem muitos testes de InfiniBand, como latência (ib_send_lat) e largura de banda (ib_send_bw). Um exemplo de comando é mostrado abaixo.
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [dimensionamento de aplicativos MPI](compiling-scaling-applications.md).
- Examine os resultados de desempenho e escalabilidade dos aplicativos HPC nas VMs HBv3 no [artigo TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843).
- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma exibição arquitetônica de nível superior da execução de cargas de trabalho do HPC, consulte [computação de alto desempenho (HPC) no Azure](/azure/architecture/topics/high-performance-computing/).
