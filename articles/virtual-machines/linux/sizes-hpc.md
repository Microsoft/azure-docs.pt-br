---
title: Tamanhos de VM Linux do Azure — HPC | Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho Linux no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 44b965bd60d976d4d28dc5e31d78a1c838d4ee02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542159"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tamanhos de máquina virtual de computação de alto desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Há suporte para apenas as versões 5.x do Intel MPI.

> [!NOTE]
> Em versões posteriores (2017, 2018) do tempo de execução do Intel MPI biblioteca pode ou não ser compatível com os drivers de RDMA do Linux do Azure.

### <a name="distributions"></a>Distribuições
 
Implante uma VM de computação intensiva de uma das imagens no Azure Marketplace que dá suporte à conectividade RDMA:
  
* **Ubuntu** – Ubuntu Server 16.04 LTS. Configure os drivers RDMA na VM e registre-se na Intel para baixar o Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** - SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium). Os drivers RDMA são instalados e os pacotes do Intel MPI são distribuídos na VM. Instale o MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **HPC baseado em HPC** - HPC baseado em CentOS 6.5 ou versão superior (para série H, recomenda-se a versão 7.1 ou posterior). OS drivers RDMA e o Intel MPI 5.1 são instalados na VM.  
 
  > [!NOTE]
  > Nas imagens do HPC baseado em CentOS, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers RDMA do Linux são distribuídos como um pacote RPM, e as atualizações de driver poderão não funcionar caso o kernel seja atualizado.
  > 
 
### <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters de VMs de HPC do Linux que podem se comunicar usando a rede RDMA, incluindo: 

* **Máquinas virtuais** - implante as VMs HPC compatíveis com RDMA no mesmo conjunto de disponibilidade (ao usar o modelo de implantação do Azure Resource Manager). Se você usar o modelo de implantação clássico, implante as VMs no mesmo serviço de nuvem. 

* **Conjuntos de dimensionamento de máquinas virtuais**: em um conjunto de dimensionamento de VM, assegure-se de limitar a implantação a um único grupo de veiculações. Por exemplo, em um modelo do Resource Manager, defina a `singlePlacementGroup`propriedade como`true`. 

* **Azure CycleCloud** - Crie um cluster HPC em [Azure CycleCloud](/azure/cyclecloud/) para executar tarefas MPI em nós do Linux.

* **Azure Batch** - Crie um pool do [Azure Batch](/azure/batch/) para executar cargas de trabalho MPI em nós de computação do Linux. Para obter mais informações, consulte [Usar instâncias habilitadas para RDMA ou habilitadas para GPU em Conjuntos de lotes](../../batch/batch-pool-compute-intensive-sizes.md). Consulte também o [Batch Shipyard](https://github.com/Azure/batch-shipyard) projeto, para executar cargas de trabalho baseados em contêiner no lote.

* **Pacote Microsoft HPC** - [Pacote HPC](https://docs.microsoft.com/powershell/high-performance-computing/overview) dá suporte a várias distribuições do Linux para execução em nós de computação implantados nas VMs do Azure compatíveis com RDMA, gerenciados por um nó principal do Windows Server. Para obter uma implementação de exemplo, consulte [Criar cluster RDMA do HPC Pack Linux no Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

Dependendo da sua opção de ferramenta de gerenciamento de cluster, pode ser necessária uma configuração adicional do sistema para executar tarefas MPI. Por exemplo, em um cluster de VMs, talvez seja necessário estabelecer confiança entre os nós do cluster, gerando chaves SSH ou estabelecendo confiança SSH sem senha.

### <a name="network-topology-considerations"></a>Considerações de topologia de rede
* Em VMs Linux habilitadas para RDMA no Azure, Eth1 é reservado para o tráfego de rede RDMA. Não altere as configurações de Eth1 ou as informações no arquivo de configuração que se refere a essa rede. Eth0 é reservado para o tráfego de rede regular do Azure.

* A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. 




## <a name="other-sizes"></a>Outros tamanhos
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.




