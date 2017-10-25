---
title: "Usar VMs do Azure de computação intensiva com o Lote | Microsoft Docs"
description: "Como tirar proveito dos tamanhos de VM compatíveis com RDMA ou habilitadas para GPU em pools do Lote do Azure"
services: batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: danlep
ms.openlocfilehash: 8a1097353d24ad4c807803511e93c90394816138
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do Lote

Para executar determinados trabalhos em lotes, você pode querer tirar proveito dos tamanhos de VM do Azure criados para a computação em larga escala. Por exemplo, para executar [cargas de trabalho de MPI](batch-mpi.md) de várias instâncias, você pode escolher os tamanhos das séries A8, A9 ou H que têm um adaptador de rede para o RDMA (Acesso Remoto Direto à Memória). Esses tamanhos se conectam a uma rede InfiniBand para comunicação entre nós, o que pode acelerar aplicativos de MPI. Ou, para aplicativos CUDA, você pode escolher tamanhos da série N que incluem cartões de GPU (unidade de processamento gráfico) da NVIDIA Tesla.

Este artigo fornece diretrizes e exemplos para usar alguns dos tamanhos especializados do Azure nos pools do Lote. Para obter especificações e contexto, consulte:

* Tamanhos de VM de computação de alto desempenho ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Tamanhos de VM habilitados para GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Limites de assinatura e de conta

* **Cotas** – a [cota de núcleos dedicados por conta do Lote](batch-quota-limit.md#resource-quotas) pode limitar o número ou tipo de nós que você pode adicionar a um pool de Lote. É mais provável que você atinja uma cota ao escolher tamanhos de VM compatíveis com RDMA, habilitados para GPU ou outros tamanhos de VM com vários núcleos. Por padrão, essa cota é de 20 núcleos. Uma cota separada se aplica a [VMs de baixa prioridade](batch-low-pri-vms.md), se você as usa. 

Se você precisa solicitar um aumento de cota, abra uma [solicitação de suporte ao cliente online](../azure-supportability/how-to-create-azure-support-request.md) gratuitamente.

* **Disponibilidade de região** – as VMs de computação intensiva podem não estar disponíveis nas regiões em que você cria suas contas do Lote. Para verificar se um tamanho está disponível, consulte [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Dependências

Os recursos de RDMA e GPU dos tamanhos de computação intensiva têm suporte somente em determinados sistemas operacionais. Dependendo do seu sistema operacional, talvez seja necessário instalar ou configurar driver adicional ou outros softwares. As tabelas a seguir resumem essas dependências. Consulte os artigos vinculados para obter detalhes. Para obter opções para configurar pools do Lote, veja mais adiante neste artigo.


### <a name="linux-pools---virtual-machine-configuration"></a>Pools do Linux – configuração de máquina virtual

| Tamanho | Recurso | Sistemas operacionais | Software necessário | Configurações do pool |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | SUSE Linux Enterprise Server 12 HPC ou<br/>HPC baseado em CentOS<br/>(Azure Marketplace) | Intel MPI 5 | Habilitar a comunicação entre nós, desabilitar a execução de tarefas simultâneas |
| [Série NC*](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms) | GPU NVIDIA Tesla K80 | Ubuntu 16.04 LTS.<br/>Red Hat Enterprise Linux 7.3 ou<br/>CentOS 7.3<br/>(Azure Marketplace) | Drivers NVIDIA CUDA Toolkit 8.0 | N/D | 
| [Série NV](../virtual-machines/linux/n-series-driver-setup.md#install-grid-drivers-for-nv-vms) | GPU NVIDIA Tesla M60 | Ubuntu 16.04 LTS<br/>Red Hat Enterprise Linux 7.3<br/>CentOS 7.3<br/>(Azure Marketplace) | Drivers NVIDIA GRID 4.3 | N/D |

* A conectividade RDMA em VMs NC24r tem suporte em HPC baseado em CentOS 7.3 com Intel MPI.



### <a name="windows-pools---virtual-machine-configuration"></a>Pools do Windows – configuração de máquina virtual

| Tamanho | Recurso | Sistemas operacionais | Software necessário | Configurações do pool |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2 ou<br/>Windows Server 2012 (Azure Marketplace) | Microsoft MPI 2012 R2 ou posterior, ou<br/> Intel MPI 5<br/><br/>Extensão de VM do Azure HpcVMDrivers | Habilitar a comunicação entre nós, desabilitar a execução de tarefas simultâneas |
| [Série NC*](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla K80 | Windows Server 2016 ou <br/>Windows Server 2012 R2 (Azure Marketplace) | Drivers NVIDIA Tesla ou drivers CUDA Toolkit 8.0| N/D | 
| [Série NV](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla M60 | Windows Server 2016 ou<br/>Windows Server 2012 R2 (Azure Marketplace) | Drivers NVIDIA GRID 4.3 | N/D |

* A conectividade RDMA em VMs NC24r tem suporte no Windows Server 2012 R2 com a extensão HpcVMDrivers e Microsoft MPI ou Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Pools do Windows – configuração de serviços de nuvem

> [!NOTE]
> Não há suporte para tamanhos da série N em pools do Lote com a configuração de serviços de nuvem.
>

| Tamanho | Recurso | Sistemas operacionais | Software necessário | Configurações do pool |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2,<br/>Windows Server 2012 ou<br/>Windows Server 2008 R2 (família do SO convidado) | Microsoft MPI 2012 R2 ou posterior, ou<br/>Intel MPI 5<br/><br/>Extensão de VM do Azure HpcVMDrivers | Habilitar a comunicação entre nós,<br/> desabilitar a execução de tarefas simultâneas |





## <a name="pool-configuration-options"></a>Opções de configuração do pool

Para configurar um tamanho de VM especializado para o pool do Lote, as ferramentas e APIs do Lote oferecem várias opções para instalar o software ou os drivers necessários, incluindo:

* [Tarefa inicial](batch-api-basics.md#start-task) – carregar um pacote de instalação como um arquivo de recurso em uma conta de armazenamento do Azure na mesma região que a conta do Lote. Crie uma linha de comando de tarefa inicial a fim de instalar o arquivo de recurso silenciosamente quando o pool for iniciado. Para obter mais informações, consulte a [Documentação da API REST](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > A tarefa inicial deve ser executada com permissões elevadas (administrador) e ela deve aguardar o êxito.
  >

* [Pacote de aplicativos](batch-application-packages.md) – adicionar um pacote de instalação compactado à sua conta do Lote e configurar uma referência de pacote no pool. Essa configuração carrega e descompacta o pacote em todos os nós no pool. Se o pacote for um instalador, crie uma linha de comando de tarefa inicial para instalar silenciosamente o aplicativo em todos os nós de pool. Opcionalmente, instale o pacote quando uma tarefa estiver agendada para ser executada em um nó.

* [Imagem de pool personalizada](batch-custom-images.md) – criar uma imagem personalizada de VM do Windows ou Linux que contenha os drivers, o software, ou outras configurações necessárias para o tamanho da VM. 

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) configura automaticamente o RDMA e a GPU para funcionar de forma transparente com cargas de trabalho em contêineres no Lote do Azure. O Batch Shipyard é totalmente controlado por arquivos de configuração. Há muitos exemplos de receitas de configuração disponíveis que habilitam as cargas de trabalho de GPU e RDMA, como a [Receita de GPU CNTK](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) que pré-configura os drivers de GPU nas VMs de série N e carrega o software Microsoft Cognitive Toolkit como uma imagem do Docker.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Exemplo: Microsoft MPI em um pool de VM A8

Para executar aplicativos de MPI do Windows em um pool de nós do Azure A8, você precisa instalar uma implementação de MPI com suporte. Aqui estão etapas de exemplo para instalar o [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) em um pool do Windows usando um pacote de aplicativos do Lote.

1. Baixe o [pacote de instalação](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) da versão mais recente do Microsoft MPI.
2. Crie um arquivo zip do pacote.
3. Carregue o pacote na sua conta do Lote. Para obter as etapas, consulte as diretrizes sobre [pacotes de aplicativos](batch-application-packages.md). Especifique uma ID de aplicativo, como *MSMPI*e uma versão, como *8.1*. 
4. Usando as APIs do Lote ou o Portal do Azure, crie um pool na configuração de serviços de nuvem com o número de nós e escala desejados. A tabela a seguir mostra configurações de exemplo para configurar o MPI no modo autônomo usando uma tarefa inicial:

| Configuração | Valor |
| ---- | ----- | 
| **Tipo de imagem** | Serviços de Nuvem |
| **Família de sistema operacional** | Windows Server 2012 R2 (família de SO 4) |
| **Tamanho do nó** | A8 Standard |
| **Comunicação entre nós habilitada** | True  |
| **Máx. de tarefas por nó** | 1 |
| **Referências do pacote de aplicativos** | MSMPI |
| **Tarefa inicial habilitada** | True <br>**Linha de comando** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Identidade de usuário** – Pool autouser, administrador<br/>**Aguardar o êxito** – True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Exemplo: drivers NVIDIA Tesla em pool de VM NC

Para executar aplicativos CUDA em um pool de nós do Linux NC, você precisa instalar o CUDA Toolkit 8.0 nos nós. O Toolkit instala os drivers de GPU NVIDIA Tesla necessários. Aqui estão as etapas de exemplo para implantar uma imagem personalizada do Ubuntu 16.04 LTS com os drivers de GPU:

1. Implante uma VM do Azure NC6 que execute o Ubuntu 16.04 LTS. Por exemplo, crie a VM na região Centro-Sul dos EUA. Certifique-se de criar a VM com armazenamento padrão e *sem* discos gerenciados.
2. Siga as etapas para conectar-se à VM e [instalar os drivers CUDA](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-vms).
3. Desprovisione o agente do Linux e, em seguida, [capture a imagem de VM Linux](../virtual-machines/linux/capture-image.md).
4. Crie uma conta do Lote em uma região que dá suporte a VMs NC.
5. Usando as APIs do Lote ou o Portal do Azure, crie um pool [usando a imagem personalizada](batch-custom-images.md) com o número de nós e escala desejados. A tabela a seguir mostra configurações de exemplo do pool para a imagem:

| Configuração | Valor |
| ---- | ---- |
| **Tipo de imagem** | Imagem personalizada |
| **Imagem personalizada** | Nome da imagem |
| **SKU do agente do nó** | batch.node.ubuntu 16.04 |
| **Tamanho do nó** | NC6 Standard |



## <a name="next-steps"></a>Próximas etapas

* Para executar trabalhos MPI em um pool do Lote do Azure, consulte os exemplos do [Windows](batch-mpi.md) ou [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/).

* Para obter exemplos de cargas de trabalho de GPU no Lote, consulte as receitas de [Batch Shipyard](https://github.com/Azure/batch-shipyard/).