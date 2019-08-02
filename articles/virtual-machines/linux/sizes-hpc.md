---
title: Tamanhos de VM Linux do Azure — HPC | Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho Linux no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
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
ms.openlocfilehash: 847f25d9be1a8654bbc0435d7874acb0ff793304
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695593"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tamanhos de máquina virtual de computação de alto desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

O SR-IOV habilitados tamanhos de VM no Azure permitem a praticamente qualquer tipo de MPI a ser usado.
Em VMs de habilitado não SR-IOV, há suporte para apenas a versões do Intel MPI 5.x. Em versões posteriores (2017, 2018) do tempo de execução do Intel MPI biblioteca pode ou não ser compatível com os drivers de RDMA do Linux do Azure.


### <a name="supported-os-images"></a>Imagens do sistema operacional com suporte
 
O Azure Marketplace tem muitas distribuições do Linux que dão suporte à conectividade RDMA:
  
* **HPC baseado em centOS** - para VMs, com base em CentOS versão 6.5 habilitadas para não-SR-IOV HPC ou uma versão posterior, até 7.5 são adequados. Para VMs da série H, versões 7.1 para 7.5 são recomendadas. OS drivers RDMA e o Intel MPI 5.1 são instalados na VM.
  Para VMs de SR-IOV, HPC do CentOS 7.6 vem pré-carregado com os drivers RDMA e os vários pacotes MPI e não otimizados.
  Para outras imagens de VM RHEL/CentOS, adicione a extensão de InfiniBandLinux para habilitar InfiniBand. Essa extensão de VM do Linux instala drivers Mellanox OFED, (em VMs de SR-IOV) para conectividade RDMA. O seguinte cmdlet do PowerShell instala a versão mais recente (versão 1.0) da extensão InfiniBandDriverLinux em uma VM compatível com RDMA existente. A VM compatíveis com RDMA *myVM* e é implantado no grupo de recursos denominado *myResourceGroup* no *Oeste dos EUA* região da seguinte maneira:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Como alternativa, as extensões de VM podem ser incluídas nos modelos do Azure Resource Manager para facilitar a implantação com o seguinte elemento JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  O comando a seguir instala a extensão de InfiniBandDriverLinux mais recente versão 1.0 em todas as VMs compatíveis com RDMA em um conjunto nomeado de dimensionamento VM existente *myVMSS* implantada no grupo de recursos denominado *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Nas imagens do HPC baseado em CentOS, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers de RDMA do Linux são distribuídos como um pacote RPM, e as atualizações de driver poderão não funcionar se o kernel é atualizado.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15. Os drivers RDMA são instalados e os pacotes do Intel MPI são distribuídos na VM. Instale o MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS, 18.04 LTS. Configure os drivers RDMA na VM e registre-se na Intel para baixar o Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Para obter mais detalhes sobre como habilitar InfiniBand, configurar o MPI, consulte [InfiniBand habilitar](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters de VMs de HPC do Linux que podem se comunicar usando a rede RDMA, incluindo: 

* **Máquinas virtuais** - implante as VMs HPC compatíveis com RDMA no mesmo conjunto de disponibilidade (ao usar o modelo de implantação do Azure Resource Manager). Se você usar o modelo de implantação clássico, implante as VMs no mesmo serviço de nuvem. 

* **Conjuntos de dimensionamento de máquina virtual** – em uma escala de máquina virtual definido, certifique-se de que você limite a implantação em um único grupo de posicionamento. Por exemplo, em um modelo do Resource Manager, defina a `singlePlacementGroup`propriedade como`true`. 

* **MPI entre máquinas virtuais** - se a comunicação MPI se for necessário entre máquinas virtuais (VMs), certifique-se de que as VMs estão no mesma conjunto de disponibilidade ou da máquina virtual mesmo conjunto de dimensionamento.

* **Azure CycleCloud** - Crie um cluster HPC em [Azure CycleCloud](/azure/cyclecloud/) para executar tarefas MPI em nós do Linux.

* **Azure Batch** - Crie um pool do [Azure Batch](/azure/batch/) para executar cargas de trabalho MPI em nós de computação do Linux. Para obter mais informações, consulte [Usar instâncias habilitadas para RDMA ou habilitadas para GPU em Conjuntos de lotes](../../batch/batch-pool-compute-intensive-sizes.md). Consulte também o [Batch Shipyard](https://github.com/Azure/batch-shipyard) projeto, para executar cargas de trabalho baseados em contêiner no lote.

* **Pacote Microsoft HPC** - [Pacote HPC](https://docs.microsoft.com/powershell/high-performance-computing/overview) dá suporte a várias distribuições do Linux para execução em nós de computação implantados nas VMs do Azure compatíveis com RDMA, gerenciados por um nó principal do Windows Server. Para obter uma implementação de exemplo, consulte [Criar cluster RDMA do HPC Pack Linux no Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Considerações sobre rede
* Em não-SR-IOV, RDMA habilitado VMs do Linux no Azure, eth1 é reservada para o tráfego de rede RDMA. Não altere as configurações de eth1 ou todas as informações no arquivo de configuração, referindo-se a essa rede.
* Em VMs (HB e HC-series) habilitadas para SR-IOV, ib0 é reservado para o tráfego de rede RDMA.
* A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. Para executar aplicativos MPI em instâncias implantadas em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.
* Dependendo da sua opção de ferramenta de gerenciamento de cluster, pode ser necessária uma configuração adicional do sistema para executar tarefas MPI. Por exemplo, em um cluster de VMs, você talvez precise estabelecer confiança entre os nós de cluster por gerar chaves SSH ou estabelecendo logons SSH sem senha.


## <a name="other-sizes"></a>Outros tamanhos
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como configurar, otimizar e dimensionar [cargas de trabalho HPC](../workloads/hpc/configure.md) no Azure.
- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
