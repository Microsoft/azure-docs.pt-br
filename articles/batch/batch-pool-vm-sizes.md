---
title: Escolher tamanhos e imagens de VM para pools
description: Como escolher entre os tamanhos de VM disponíveis e as versões de so para nós de computação em pools do lote do Azure
ms.topic: conceptual
ms.date: 03/08/2021
ms.custom: seodec18
ms.openlocfilehash: 42b8743fac6a6c64e98271490f0bfc4671fa7698
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455188"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Escolher um tamanho e uma imagem da VM para nós de computação em um pool do lote do Azure

Ao selecionar um tamanho de nó para um pool de Lote do Azure, você pode escolher dentre quase todos os tamanhos de VM disponíveis no Azure. O Azure oferece uma variedade de tamanhos para VMs do Linux e do Windows para diferentes cargas de trabalho.

## <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos de VM com suporte

Há algumas exceções e limitações para escolher um tamanho de VM para seu pool do lote:

- Não há suporte no Lote para algumas séries de VM ou tamanhos de VM.
- Alguns tamanhos de VM são restritos e precisam ser especificamente habilitados antes de serem alocados.

### <a name="pools-in-virtual-machine-configuration"></a>Pools na configuração de Máquina Virtual

Os pools do lote na configuração de máquina virtual dão suporte a quase todos os [tamanhos de VM](../virtual-machines/sizes.md). Veja a tabela a seguir para saber mais sobre tamanhos compatíveis e restrições.

| Série da VM  | Tamanhos com suporte |
|------------|---------|
| A Básico | Todos os tamanhos *exceto* Basic_A0 (A0) |
| Um | Todos os tamanhos, *exceto* Standard_A0, Standard_A8, Standard_A9, Standard_A10, Standard_A11 |
| Av2 | Todos os tamanhos |
| B | Sem suporte |
| DC | Sem suporte |
| Dv2, DSv2 | Todos os tamanhos |
| Dv3, Dsv3 | Todos os tamanhos |
| Dav4, Dasv4 | Todos os tamanhos |
| Ddv4, Ddsv4 |  Todos os tamanhos |
| Dv4, Dsv4 | Sem suporte |
| Ev3, Esv3 | Todos os tamanhos, exceto para E64is_v3 |
| Eav4, Easv4 | Todos os tamanhos |
| Edv4, Edsv4 |  Todos os tamanhos |
| Ev4, Esv4 | Sem suporte |
| F, Fs | Todos os tamanhos |
| Fsv2 | Todos os tamanhos |
| G, Gs | Todos os tamanhos |
| H | Todos os tamanhos |
| HB | Todos os tamanhos |
| HBv2 | Todos os tamanhos |
| HC | Todos os tamanhos |
| Ls | Todos os tamanhos |
| Lsv2 | Todos os tamanhos |
| M | Todos os tamanhos |
| Mv2<sup>1</sup> | Todos os tamanhos |
| NC | Todos os tamanhos |
| NCv2 | Todos os tamanhos |
| NCv3 | Todos os tamanhos |
| NCasT4_v3 | Todos os tamanhos |
| ND | Todos os tamanhos |
| NDv2 | Nenhum - ainda não disponível |
| NV | Todos os tamanhos |
| NVv3 | Todos os tamanhos |
| NVv4 | Todos os tamanhos |
| SAP HANA | Sem suporte |

<sup>1</sup> essas séries de VMs só podem ser usadas com imagens de VM de geração 2.

### <a name="using-generation-2-vm-images"></a>Usando imagens de VM de geração 2

Algumas séries de VMs, como [Mv2](../virtual-machines/mv2-series.md), só podem ser usadas com [imagens de VM de geração 2](../virtual-machines/generation-2.md). As imagens de VM de geração 2 são especificadas como qualquer imagem de VM, usando a propriedade ' SKU ' da configuração [' imageReference '](/rest/api/batchservice/pool/add#imagereference) ; as cadeias de caracteres ' SKU ' têm um sufixo como "-G2" ou "-Gen2". Para obter uma lista de imagens de VM com suporte pelo lote, incluindo imagens de geração 2, use a API ["listar imagens com suporte"](/rest/api/batchservice/account/listsupportedimages) , o [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage)ou o [CLI do Azure](/cli/azure/batch/pool/supported-images).

### <a name="pools-in-cloud-service-configuration"></a>Pools na configuração de Serviço de Nuvem

Os pools de lote na configuração do Serviço de Nuvem oferecem suporte a todos os [tamanhos de VM para Serviços de Nuvem](../cloud-services/cloud-services-sizes-specs.md)**exceto** para:

| Série da VM  | Tamanhos sem suporte |
|------------|-------------------|
| Séria A   | Extrapequena       |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerações de tamanhos

- **Requisitos do aplicativo** - considere as características e os requisitos dos aplicativos que você vai executar nos nós. Os aspectos como se o aplicativo tem multithread e quanta memória ele consome podem ajudar a determinar o tamanho do nó mais adequado e econômico. Para [cargas de trabalho de MPI](batch-mpi.md) de instâncias múltiplas ou aplicativos CUDA, considere tamanhos de VM de [HPC](../virtual-machines/sizes-hpc.md) especializado ou [habilitado para GPU](../virtual-machines/sizes-gpu.md), respectivamente. Para obter mais informações, consulte [Usar instâncias habilitadas para RDMA ou habilitadas para GPU em Conjuntos de lotes](batch-pool-compute-intensive-sizes.md).

- **Tarefas por nó** - Geralmente, você seleciona um tamanho de nó supondo que uma tarefa seja executada no nó por vez. No entanto, pode ser vantajoso ter várias tarefas (e, portanto, várias instâncias do aplicativo) [executadas em paralelo](batch-parallel-node-tasks.md) em nós de computação durante a execução do trabalho. Nesse caso, é comum escolher um tamanho multicore de nó para acomodar a demanda crescente de execução de tarefas paralelas.

- **Carregar níveis para tarefas diferentes** - Todos os nós em um pool têm o mesmo tamanho. Se você pretende executar aplicativos com diferentes requisitos de sistema e/ou níveis de carga, é recomendável usar pools separados.

- **Disponibilidade de região** – Uma série ou tamanho de VM pode não estar disponível nas regiões onde você cria suas contas do Lote. Para verificar se um tamanho está disponível, consulte [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

- **Cotas** - As [cotas principais](batch-quota-limit.md#resource-quotas) na sua conta do Lote podem limitar o número de nós de um determinado tamanho que você pode adicionar a um pool do Lote. Quando necessário, você pode [solicitar um aumento de cota](batch-quota-limit.md#increase-a-quota).

- **Configuração do pool** - Em geral, você tem mais opções de tamanho de VM quando você cria um pool na configuração da Máquina Virtual, em comparação com a configuração do serviço de nuvem.

## <a name="supported-vm-images"></a>Imagens de VM com suporte

Use uma das APIs a seguir para retornar uma lista de imagens de VM do Windows e Linux com suporte no momento pelo lote, incluindo as IDs de SKU do agente de nó para cada imagem:

- API REST do serviço de lote: [listar imagens com suporte](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- CLI do Azure: [AZ batch pool com suporte-imagens](/cli/azure/batch/pool/supported-images)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- Para obter informações sobre tamanhos de VM de computação intensiva, consulte [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do Lote](batch-pool-compute-intensive-sizes.md).
