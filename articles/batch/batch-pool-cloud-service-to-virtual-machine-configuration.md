---
title: Migrar a configuração do pool do lote dos serviços de nuvem para máquinas virtuais
description: Saiba como atualizar sua configuração de pool para a configuração mais recente e recomendada
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200570"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Migrar a configuração do pool do lote dos serviços de nuvem para a máquina virtual

Atualmente, os pools do lote podem ser criados usando [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) ou [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration). É recomendável usar somente a configuração da máquina virtual, pois essa configuração dá suporte a todos os recursos do lote.

Os pools de configuração de serviços de nuvem não dão suporte a alguns dos recursos do lote atual e não oferecerão suporte a nenhum recurso recém-adicionado. Você não poderá criar novos pools ' CloudServiceConfiguration ' ou adicionar novos nós a pools existentes [após 29 de fevereiro de 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

Se as soluções do lote usarem atualmente pools ' cloudServiceConfiguration ', recomendamos alterar para ' virtualMachineConfiguration ' assim que possível. Isso permitirá que você se beneficie de todos os recursos do lote, como uma seleção expandida [de séries de VM](batch-pool-vm-sizes.md), VMS do Linux, [contêineres](batch-docker-container-workloads.md), [Azure Resource Manager redes virtuais](batch-virtual-network.md)e a [criptografia de disco do nó](disk-encryption.md).

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Criar um pool usando a configuração de máquina virtual

Você não pode alternar um pool ativo existente que usa ' cloudServiceConfiguration ' para usar ' virtualMachineConfiguration '. Em vez disso, você precisará criar novos pools. Depois de criar os novos pools do ' virtualMachineConfiguration ' e replicar todos os trabalhos e tarefas, você poderá excluir o antigo ' cloudServiceConfiguration'pools que você não está mais usando.

Todas as APIs do lote, as ferramentas de linha de comando, a portal do Azure e a interface do usuário do Batch Explorer permitem criar pools usando ' virtualMachineConfiguration '.

Para obter uma explicação do processo de criação de pools que usam ' virtualMachineConfiguration ', consulte o [tutorial do .net](tutorial-parallel-dotnet.md) ou o [tutorial do Python](tutorial-parallel-python.md).

## <a name="pool-configuration-differences"></a>Diferenças de configuração de pool

Algumas das principais diferenças entre as duas configurações incluem:

- OS nós de pool ' cloudServiceConfiguration ' usam apenas o sistema operacional Windows. OS pools ' virtualMachineConfiguration ' podem usar o sistema operacional Linux ou Windows.
- Em comparação com os pools ' cloudServiceConfiguration ', os pools ' virtualMachineConfiguration ' têm um conjunto mais rico de recursos, como suporte a contêiner, discos de dados e criptografia de disco.
- Os tempos de exclusão e de inicialização do pool e do nó podem diferir ligeiramente entre os pools ' cloudServiceConfiguration ' e os pools ' virtualMachineConfiguration '.
- OS nós de pool ' virtualMachineConfiguration ' utilizam discos do sistema operacional gerenciados. O [tipo de disco gerenciado](../virtual-machines/disks-types.md) que é usado para cada nó depende do tamanho da VM escolhido para o pool. Se um ' tamanho da VM for especificado para o pool, por exemplo, ' Standard_D2s_v3 ', será usado um SSD Premium. Se um tamanho de VM ' não s' for especificado, por exemplo, ' Standard_D2_v3 ', será usado um HDD padrão.

   > [!IMPORTANT]
   > Assim como ocorre com máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais, o disco gerenciado do sistema operacional usado para cada nó provoca um custo, que é adicional ao custo das VMs. Não há custo de disco do sistema operacional para OS nós ' cloudServiceConfiguration ', pois o disco do sistema operacional é criado no SSD local dos nós.

## <a name="azure-data-factory-custom-activity-pools"></a>Azure Data Factory pools de atividades personalizadas

Os pools do lote do Azure podem ser usados para executar Data Factory atividades personalizadas. Todos os pools ' cloudServiceConfiguration ' usados para executar atividades personalizadas precisarão ser excluídos e novos pools ' virtualMachineConfiguration ' criados.

Ao criar seus novos pools para execução Data Factory atividades personalizadas, siga estas práticas:

- Pause todos os pipelines antes de criar os novos pools e exclusão dos antigos para garantir que nenhuma execução será interrompida.
- A mesma ID de pool pode ser usada para evitar alterações de configuração de serviço vinculado.
- Retome os pipelines quando novos pools forem criados.

Para obter mais informações sobre como usar o lote do Azure para executar Data Factory atividades personalizadas, consulte [serviço vinculado do lote do Azure](../data-factory/compute-linked-services.md#azure-batch-linked-service) e  [atividades personalizadas em um pipeline de data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as configurações de pool](nodes-and-pools.md#configurations).
- Saiba mais sobre [as práticas recomendadas de pool](best-practices.md#pools).
- Consulte a referência da API REST para [adição de pool](/rest/api/batchservice/pool/add) e [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).