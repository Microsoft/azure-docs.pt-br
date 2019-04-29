---
title: Tamanhos de VM Windows do Azure — HPC | Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho Windows no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 58d4ced041b6f5cf767b45191e28a4b395f584b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540466"
---
# <a name="high-performance-compute-vm-sizes"></a>Tamanhos de VM de computação de alto desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Sistema operacional** -Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** – Microsoft MPI (MS-MPI) 2012 R2 ou posterior, Intel MPI Library 5.x

  Implementações de MPI com suporte usam a interface direta da rede Microsoft para comunicação entre instâncias. 

* **Espaço de endereço de rede RDMA** - A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. Para executar aplicativos MPI em instâncias implantadas em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.

* **Extensão de VM HpcVmDrivers**: em VMs compatíveis com RDMA, adicione a extensão HpcVmDrivers para instalar drivers de dispositivo de rede do Windows para conectividade RDMA. (Em algumas implantações das instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente.) Para adicionar a extensão de VM a uma VM, use cmdlets do [Azure PowerShell](/powershell/azure/overview). 

  
  O comando a seguir instala a versão 1.1 mais recente da extensão HpcVMDrivers em uma VM compatível com RDMA existente denominada *myVM* implantada no grupo de recursos chamada *myResourceGroup* na região *Oeste dos EUA*:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Para obter mais informações, consulte [Recursos e extensões da máquina virtual](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Também é possível trabalhar com extensões de VMs implantadas no [modelo de implantação clássico](classic/manage-extensions.md).

### <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters de VMs do Windows HPC que podem se comunicar usando a rede RDMA, incluindo: 

* **Máquinas virtuais** - implante as VMs HPC compatíveis com RDMA no mesmo conjunto de disponibilidade (ao usar o modelo de implantação do Azure Resource Manager). Se você usar o modelo de implantação clássico, implante as VMs no mesmo serviço de nuvem. 

* **Conjuntos de dimensionamento de máquinas virtuais**: em um conjunto de dimensionamento de VM, assegure-se de limitar a implantação a um único grupo de veiculações. Por exemplo, em um modelo do Resource Manager, defina a `singlePlacementGroup`propriedade como`true`. 

* **Azure CycleCloud** - Crie um cluster HPC em [Azure CycleCloud](/azure/cyclecloud/) para executar tarefas MPI em nós do Windows.

* **Azure Batch** - Crie um pool do [Azure Batch](/azure/batch/) para executar cargas de trabalho MPI em nós de computação do Windows Server. Para obter mais informações, consulte [Usar instâncias habilitadas para RDMA ou habilitadas para GPU em Conjuntos de lotes](../../batch/batch-pool-compute-intensive-sizes.md). Consulte também o [Batch Shipyard](https://github.com/Azure/batch-shipyard) projeto, para executar cargas de trabalho baseados em contêiner no lote.

* **Pacote Microsoft HPC** - [Pacote HPC](https://docs.microsoft.com/powershell/high-performance-computing/overview) inclui um ambiente de tempo de execução para MS-MPI que usa a rede RDMA do Azure quando implantado em VMs do Windows compatíveis com RDMA. Por exemplo implantações, consulte [configurar um cluster de RDMA do Windows com o pacote HPC para executar aplicativos MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Outros tamanhos
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](../virtual-machines-windows-sizes-memory.md)
- [Armazenamento otimizado](../virtual-machines-windows-sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter listas de verificação para usar as instâncias de computação intensiva com o HPC Pack no Windows Server, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Para usar instâncias de computação intensiva para executar aplicativos MPI com o Lote do Azure, consulte [Usar tarefas de várias instâncias para executar aplicativos MPI (Interface de Transmissão de Mensagens) no Lote do Azure](../../batch/batch-mpi.md).

- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.




