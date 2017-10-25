---
title: "Tamanhos de VM Linux do Azure — HPC | Microsoft Docs"
description: "Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho Linux no Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: jonbeck
ms.openlocfilehash: fecc0656264f1c1d44aa8ad3aea321aa8cc4a0c8
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="high-performance-compute-linux-vm-sizes"></a>Tamanhos de VM Linux de computação de alto desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Instâncias compatíveis com RDMA
Um subconjunto das instâncias de computação intensiva (H16r, H16mr, A8 e A9) apresenta um adaptador de rede para conectividade RDMA (acesso remoto direto à memória). Essa interface é além do adaptador de rede do Azure padrão disponível para outros tamanhos de VM. 
  
Essa interface permite que instâncias compatíveis com RDMA se comuniquem em uma rede InfiniBand, operando em taxas FDR para máquinas virtuais H16r e H16mr e taxas QDR para máquinas virtuais A8 e A9. Esses recursos RDMA podem melhorar a escalabilidade e o desempenho de aplicativos de Interface de Transmissão de Mensagens (MPI) que são executados no Intel MPI 5.x ou uma versão posterior.

Implante as VMs compatíveis com RDMA no mesmo conjunto de disponibilidade (se usar o modelo de implantação do Azure Resource Manager) ou no mesmo serviço de nuvem (se usar o modelo de implantação clássica). A seguir estão os requisitos adicionais para VMs Linux compatíveis com RDMA acessarem a rede RDMA do Azure.

### <a name="distributions"></a>Distribuições
 
Implante uma VM de computação intensiva de uma das imagens no Azure Marketplace que dá suporte à conectividade RDMA:
  
* **Ubuntu** – Ubuntu Server 16.04 LTS. Configure os drivers RDMA na VM e registre-se na Intel para baixar o Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** - SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium). Os drivers RDMA são instalados e os pacotes do Intel MPI são distribuídos na VM. Instale o MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **HPC baseado em CentOS** - HPC baseado em CentOS 7.3, HPC baseado em CentOS 7.1, HPC baseado em CentOS 6.8, ou HPC baseado em CentOS 6.5 (para a série H, recomenda-se a versão 7.1 ou posterior). OS drivers RDMA e o Intel MPI 5.1 são instalados na VM.  
 
  > [!NOTE]
  > Nas imagens do HPC baseado em CentOS, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers RDMA do Linux são distribuídos como um pacote RPM, e as atualizações de driver poderão não funcionar caso o kernel seja atualizado.
  > 
 
### <a name="cluster-configuration"></a>Configuração do cluster 
    
Configurações adicionais do sistema são necessárias para executar trabalhos MPI em máquinas virtuais clusterizadas. Por exemplo, em um cluster de máquinas virtuais, você precisa estabelecer confiança entre os nós de computação. Para configurações típicas, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Considerações de topologia de rede
* Em VMs Linux habilitadas para RDMA no Azure, Eth1 é reservado para o tráfego de rede RDMA. Não altere as configurações de Eth1 ou as informações no arquivo de configuração que se refere a essa rede. Eth0 é reservado para o tráfego de rede regular do Azure.

* IP sobre InfiniBand (IB) não tem suporte no Azure. Apenas RDMA sobre IB é compatível.

## <a name="using-hpc-pack"></a>Usando o HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), a solução de gerenciamento de trabalho e cluster HPC gratuita da Microsoft, é uma opção para usar as instâncias de computação intensiva com o Linux. As últimas versões do HPC Pack dão suporte a várias distribuições Linux para execução em nós de computação implantados nas VMs do Azure, gerenciados por um nó de cabeçalho do Windows Server. Com os nós de computação Linux compatíveis com RDMA executando o Intel MPI, o HPC Pack pode agendar e executar os aplicativos Linux MPI que acessam a rede RDMA. Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Outros tamanhos
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Próximas etapas

- Para se familiarizar com a implantação e o uso dos tamanhos de computação intensiva com o RDMA no Linux, veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.




