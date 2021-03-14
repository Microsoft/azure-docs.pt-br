---
title: Série Msv2 (versão prévia) – máquinas virtuais do Azure
description: Especificações para as VMs da série Msv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: a7f4757467523837423d52998eb6b8204090e627
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562564"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Memória média da série Msv2 e Mdsv2 (visualização)


> [!IMPORTANT]
> Junte-se à versão prévia preenchendo o formulário em **https://aka.ms/Mv2MedMemoryPreview** .  

A série de VMs de memória média Msv2 e Mdsv2 apresenta o processador Intel® Xeon® Platinum 8280 (Cascadey Lake) com uma frequência básica de base de 2,7 GHz e 4,0 GHz, com uma frequência de um único núcleo. Com essas VMs, os clientes obtêm maior flexibilidade com opções de disco local e sem disco. Os clientes também têm acesso a um conjunto de novos tamanhos de VM isoladas com mais CPU e memória que vão até 192 vCPU com 4 TiB de memória. 


As VMs da série Msv2 e Mdsv2 são somente geração 2 e dão suporte a um subconjunto de imagens com suporte de geração 2. Veja abaixo a lista completa de imagens com suporte para a série Msv2 e Mdsv2.  

- Windows Server 2019 ou posterior
- SUSE Linux Enterprise Server 12 SP4 e posterior ou SUSE Linux Enterprise Server 15 SP1 e posterior
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 ou posterior 
- Oracle Enterprise Linux 7,7 ou posterior

Para obter mais informações sobre máquinas virtuais de geração 2, consulte [suporte para VMs de geração 2 no Azure](./generation-2.md).



[Armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Cache de armazenamento Premium](premium-storage-performance.md): com suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 2<br>
[Acelerador de gravação](./how-to-enable-write-accelerator.md): com suporte<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): com suporte<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): sem suporte <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Memória Msv2 média em disco insuficiente 

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs | Largura de banda de rede esperada (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2.048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2.048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 de memória média com disco  

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Disco de dados máximo | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps | Taxa de transferência de disco sem cache: IOPS/MBps | Máximo de NICs | Largura de banda de rede esperada (Mbps) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2.048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2.048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2.048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2.048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
