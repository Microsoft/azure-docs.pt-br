---
title: Série DC – Máquinas Virtuais do Azure
description: Especificações para as VMs da série DC.
author: susaxen
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 677f4df0873f8b72d40dd373035111e2e0002491
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549223"
---
# <a name="dcsv2-series"></a>Série DCsv2


A série DCsv2 pode ajudar a proteger a confidencialidade e a integridade dos dados e do código enquanto eles são processados na nuvem pública. Esses computadores têm o suporte da última geração do processador Intel XEON E-2288G com a tecnologia SGX. Com a Tecnologia Intel Turbo Boost, esses computadores podem chegar a 5,0 GHz. As instâncias da série DCsv2 permitem que os clientes criem aplicativos seguros baseados em enclave para proteger o código e os dados enquanto estiverem em uso.

Exemplos de casos de uso incluem: compartilhamento de dados confidenciais entre vários participantes, detecção de fraudes, antilavagem de dinheiro, blockchain, análise de uso confidencial, análise de inteligência e machine learning confidencial.

[Armazenamento Premium](premium-storage-performance.md):*<br> 
 [cache de armazenamento Premium](premium-storage-performance.md)com suporte: migração ao vivo com suporte <br> 
 [](maintenance-and-updates.md): <br> 
 [atualizações de preservação de memória](maintenance-and-updates.md)sem suporte: <br> 
 [suporte à geração de VM](generation-2.md)sem suporte: <br> 
 [rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md)de geração 2: com suporte (* requer um mínimo de 4 vCPU *) <br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): com suporte <br>

*Exceto para Standard_DC8_v2 <br>

| Tamanho             | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Número máximo de NICs/largura de banda de rede esperada (MBps) | Memória EPC (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2\.000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4\.000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8\.000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16.000/128                                                                                         | 2   | 168                                         |

- As VMs da série DCsv2 são [VMs de geração 2](./generation-2.md#creating-a-generation-2-vm) e dão suporte apenas a imagens `Gen2`.
- Disponível atualmente nas regiões listadas [aqui](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).
- Geração anterior de VMs de computação confidencial: [Série DC](sizes-previous-gen.md#preview-dc-series)
- Criar VMs DCsv2 usando o [portal do Azure](./linux/quick-create-portal.md) ou o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos: [tipos de disco](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
