---
title: Usar VMs do Azure de computação intensiva com o Lote | Microsoft Docs
description: Como aproveitar os tamanhos de VM de GPU e HPC nos pools do Lote do Azure
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 687783520b082cdfd1a6ffc91a8641ea35fafd68
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323343"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Usar instâncias de GPU ou RDMA em pools do Lote

Para executar determinados trabalhos do Lote, é possível aproveitar os tamanhos de VM do Azure projetados para computação em grande escala. Por exemplo:

* Para executar [cargas de trabalho MPI](batch-mpi.md) de várias instâncias, escolha séries H ou outros tamanhos que tenham um adaptador de rede para RDMA (Acesso Remoto Direto à Memória). Esses tamanhos se conectam a uma rede InfiniBand para comunicação entre nós, o que pode acelerar aplicativos de MPI. 

* Para aplicativos CUDA, escolha os tamanhos da série N que incluem as placas GPU (unidade de processamento gráfico) NVIDIA Tesla.

Este artigo fornece diretrizes e exemplos para usar alguns dos tamanhos especializados do Azure nos pools do Lote. Para obter especificações e contexto, consulte:

* Tamanhos de VM de computação de alto desempenho ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Tamanhos de VM habilitados para GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Determinados tamanhos de VM podem não estar disponíveis nas regiões em que você cria as contas do Lote. Para verificar se um tamanho está disponível, consulte [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/) e [Escolher um tamanho de VM para um pool do Lote](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Dependências

Os recursos de GPU ou RDMA de tamanhos de computação intensiva no Lote têm suporte apenas em determinados sistemas operacionais. (A lista de sistemas operacionais com suporte é um subconjunto daqueles com suporte para máquinas virtuais criadas nesses tamanhos.) Dependendo de como você cria o pool do Lotes, talvez seja necessário instalar ou configurar um driver adicional ou outro software nos nós. As tabelas a seguir resumem essas dependências. Consulte os artigos vinculados para obter detalhes. Para obter opções para configurar pools do Lote, veja mais adiante neste artigo.

### <a name="linux-pools---virtual-machine-configuration"></a>Pools do Linux – configuração de máquina virtual

| Tamanho | Recurso | Sistemas operacionais | Software necessário | Configurações do pool |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, ou<br/>HPC baseado em CentOS<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Drivers de RDMA do Linux | Habilitar a comunicação entre nós, desabilitar a execução de tarefas simultâneas |
| [Séries NC, NCv2, NCv3, NDv2](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (varia de acordo com a série) | Ubuntu 16.04 LTS, ou<br/>CentOS 7.3 ou 7.4<br/>(Azure Marketplace) | Drivers CUDA Toolkit ou NVIDIA CUDA | N/D | 
| [Séries NV, NVv2](../virtual-machines/linux/n-series-driver-setup.md) | GPU NVIDIA Tesla M60 | Ubuntu 16.04 LTS, ou<br/>CentOS 7.3<br/>(Azure Marketplace) | Drivers NVIDIA GRID | N/D |

<sup>*</sup>Os tamanhos da série N compatíveis com RDMA também incluem GPUs NVIDIA Tesla

### <a name="windows-pools---virtual-machine-configuration"></a>Pools do Windows – configuração de máquina virtual

| Tamanho | Recurso | Sistemas operacionais | Software necessário | Configurações do pool |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 ou<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 ou posterior, ou<br/> Intel MPI 5<br/><br/>Drivers de RDMA do Windows | Habilitar a comunicação entre nós, desabilitar a execução de tarefas simultâneas |
| [Séries NC, NCv2, NCv3, ND, NDv2](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (varia de acordo com a série) | Windows Server 2016 ou <br/>2012 R2 (Azure Marketplace) | Drivers CUDA Toolkit ou NVIDIA CUDA| N/D | 
| [Séries NV, NVv2](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla M60 | Windows Server 2016 ou<br/>2012 R2 (Azure Marketplace) | Drivers NVIDIA GRID | N/D |

<sup>*</sup>Os tamanhos da série N compatíveis com RDMA também incluem GPUs NVIDIA Tesla

### <a name="windows-pools---cloud-services-configuration"></a>Pools do Windows – configuração de serviços de nuvem

> [!NOTE]
> Não há suporte para tamanhos da série N em pools do Lote com a configuração do serviço de nuvem.
>

| Tamanho | Recurso | Sistemas operacionais | Software necessário | Configurações do pool |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 ou<br/>2008 R2 (família do SO convidado) | Microsoft MPI 2012 R2 ou posterior, ou<br/>Intel MPI 5<br/><br/>Drivers de RDMA do Windows | Habilitar a comunicação entre nós,<br/> desabilitar a execução de tarefas simultâneas |

## <a name="pool-configuration-options"></a>Opções de configuração do pool

Para configurar um tamanho de VM especializado para o pool do Lote, há várias opções para instalar o software ou drivers necessários:

* Para pools na configuração da máquina virtual, escolha uma imagem de VM do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) pré-configurada que tenha drivers e software pré-instalados. Exemplos: 

  * [CentOS-based 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) - inclui drivers RDMA e Intel MPI 5.1

  * [Máquina Virtual de Ciência de Dados](../machine-learning/data-science-virtual-machine/overview.md) para Linux ou Windows - inclui drivers NVIDIA CUDA

  * Imagens do Linux para cargas de trabalho de contêiner do Lote que também incluem drivers GPU e RDMA:

    * [CentOS (com drivers GPU e RDMA) para pools de contêiner do Lote do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (com drivers GPU e RDMA) para pools de contêiner do Lote do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Crie uma [imagem personalizada da VM do Windows ou Linux](batch-custom-images.md) na qual você instalou drivers, software ou outras configurações necessárias para o tamanho da VM. 

* Crie um [pacote de aplicativos](batch-application-packages.md) do Lote a partir de um driver compactado ou instalador de aplicativo, configure o Lote para implantar o pacote nos nós do pool e instale uma vez quando cada nó for criado. Por exemplo, se o pacote de aplicativos for um instalador, crie uma linha de comando [start task](batch-api-basics.md#start-task) para instalar silenciosamente o aplicativo em todos os nós do pool. Considere usar um pacote de aplicativos e uma tarefa inicial de pool, se a carga de trabalho depender de uma versão de driver específica.

  > [!NOTE] 
  > A tarefa inicial deve ser executada com permissões elevadas (administrador) e ela deve aguardar o êxito. Tarefas de execução longa aumentarão o tempo para provisionar um pool do Lote.
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) configura automaticamente os drivers GPU e RDMA para trabalhar de forma transparente com cargas de trabalho em contêiner no Lote do Azure. O Batch Shipyard é totalmente controlado por arquivos de configuração. Há muitas configurações de receita de exemplo disponíveis que permitem cargas de trabalho GPU e RDMA, como a [Receita de GPU do CNTK](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) que pré-configura os drivers GPU nas VMs da série N e carrega o software Microsoft Cognitive Toolkit como uma imagem do Docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Exemplo: Drivers GPU NVIDIA no pool de VM da NC do Windows

Para executar aplicativos CUDA em um pool de nós da NC do Windows, é necessário instalar os drivers GPU NVDIA. As etapas de exemplo a seguir usam um pacote de aplicativos para instalar os drivers GPU NVIDIA. Será possível escolher essa opção, se a carga de trabalho depender de uma versão específica do driver GPU.

1. Baixe um pacote de instalação dos drivers GPU no Windows Server 2016 pelo [Site da NVIDIA](https://www.nvidia.com/Download/index.aspx) - por exemplo, [versão 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Salve o arquivo localmente usando um nome curto como *GPUDriverSetup.exe*.
2. Crie um arquivo zip do pacote.
3. Carregue o pacote na sua conta do Lote. Para obter as etapas, consulte as diretrizes sobre [pacotes de aplicativos](batch-application-packages.md). Especifique uma ID de aplicativo como *GPUDriver* e uma versão como *411.82*.
1. Usando as APIs do Lote ou o portal do Azure, crie um pool na configuração da máquina virtual com o número desejado de nós e escala. A tabela a seguir mostra as configurações de exemplo para instalar os drivers GPU NVIDIA silenciosamente usando uma tarefa inicial:

| Configuração | Valor |
| ---- | ----- | 
| **Tipo de imagem** | Marketplace (Linux/Windows) |
| **Publicador** | MicrosoftWindowsServer |
| **Oferta** | WindowsServer |
| **Sku** | 2016-Datacenter |
| **Tamanho do nó** | NC6 Standard |
| **Referências do pacote de aplicativos** | GPUDriver, versão 411.82 |
| **Tarefa inicial habilitada** | verdadeiro<br>**Linha de comando** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Identidade de usuário** – Pool autouser, administrador<br/>**Aguardar o êxito** – True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Exemplo: Drivers GPU NVIDIA no pool de VM da NC do Linux

Para executar aplicativos CUDA em um conjunto de nós da NC do Linux, é necessário instalar os drivers GPU NVIDIA Tesla necessários do CUDA Toolkit. As etapas de exemplo a seguir criam e implantam uma imagem personalizada do Ubuntu 16.04 LTS com os drivers GPU:

1. Implante uma VM do Azure série NC executando Ubuntu 16.04 LTS. Por exemplo, crie a VM na região Centro-Sul dos EUA. 
2. Adicione a [extensão dos drivers GPU NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) à VM, usando o portal do Azure, um computador cliente que conecta-se à assinatura do Azure ou ao Azure Cloud Shell. Como alternativa, siga as etapas para conectar a VM e [instalar os drivers](../virtual-machines/linux/n-series-driver-setup.md) manualmente.
3. Siga as etapas para criar um instantâneo [e uma imagem de VM do Linux personalizada](batch-custom-images.md) para Lote.
4. Crie uma conta do Lote em uma região que dá suporte a VMs NC.
5. Usando as APIs do Lote ou o Portal do Azure, crie um pool [usando a imagem personalizada](batch-custom-images.md) com o número de nós e escala desejados. A tabela a seguir mostra configurações de exemplo do pool para a imagem:

| Configuração | Valor |
| ---- | ---- |
| **Tipo de imagem** | Imagem personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **SKU do agente do nó** | batch.node.ubuntu 16.04 |
| **Tamanho do nó** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Exemplo: Microsoft MPI em um pool de VM do Windows H16r

Para executar aplicativos Windows MPI em um pool de nós de VM do Azure H16r, é necessário configurar a extensão HpcVmDrivers e instalar o [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). A seguir, são apresentados exemplos de etapas para implantar uma imagem personalizada do Windows Server 2016 com os drivers e softwares necessários:

1. Implante uma VM do Azure H16r em execução no Windows Server 2016. Por exemplo, crie a VM na região Oeste dos EUA. 
2. Adicione a extensão HpcVmDrivers à VM, [executando um comando do Azure PowerShell](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) de um computador cliente que conecta-se à assinatura do Azure ou usando o Azure Cloud Shell. 
1. Faça uma conexão de Área de Trabalho Remota para a VM.
1. Baixe o [pacote de instalação](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) da última versão do Microsoft MPI e, em seguida, instale o Microsoft MPI.
1. Siga as etapas para criar um instantâneo [e uma imagem personalizada da VM do Windows](batch-custom-images.md) para Lote.
1. Usando as APIs do Lote ou o Portal do Azure, crie um pool [usando a imagem personalizada](batch-custom-images.md) com o número de nós e escala desejados. A tabela a seguir mostra configurações de exemplo do pool para a imagem:

| Configuração | Valor |
| ---- | ---- |
| **Tipo de imagem** | Imagem personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **SKU do agente do nó** | batch.node.windows amd64 |
| **Tamanho do nó** | H16r Standard |
| **Comunicação entre nós habilitada** | verdadeiro |
| **Máx. de tarefas por nó** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Exemplo: Intel MPI em um pool de VM do Linux H16r

Para executar aplicativos MPI em um pool de nós da série H do Linux, uma opção é usar a imagem [CentOS-based 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) do Azure Marketplace. Drivers RDMA do Linux e Intel MPI são pré-instalados. Essa imagem também dá suporte a cargas de trabalho do contêiner do Docker.

Usando as APIs do Lote ou o portal do Azure, crie um pool usando essa imagem e com o número desejado de nós e escala. A tabela a seguir mostra as configurações do pool de exemplo:

| Configuração | Valor |
| ---- | ---- |
| **Tipo de imagem** | Marketplace (Linux/Windows) |
| **Publicador** | OpenLogic |
| **Oferta** | CentOS-HPC |
| **Sku** | 7.4 |
| **Tamanho do nó** | H16r Standard |
| **Comunicação entre nós habilitada** | verdadeiro |
| **Máx. de tarefas por nó** | 1 |

## <a name="next-steps"></a>Próximas etapas

* Para executar trabalhos MPI em um pool do Lote do Azure, consulte os exemplos do [Windows](batch-mpi.md) ou [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/).

* Para obter exemplos de cargas de trabalho de GPU no Lote, consulte as receitas de [Batch Shipyard](https://github.com/Azure/batch-shipyard/).