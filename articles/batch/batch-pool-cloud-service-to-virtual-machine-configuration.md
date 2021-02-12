---
title: Migrar a configuração do pool do lote dos serviços de nuvem para máquinas virtuais
description: Saiba como atualizar sua configuração de pool para a configuração mais recente e recomendada
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: 417738be2c69101129079b8ff3a3d80634f9f99c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731492"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Migrar a configuração do pool do lote dos serviços de nuvem para máquinas virtuais

Os pools do lote podem ser criados usando [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) ou [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration). ' virtualMachineConfiguration ' é a configuração recomendada, pois oferece suporte a todos os recursos do lote. os pools ' cloudServiceConfiguration ' não dão suporte a todos os recursos e nenhum recurso novo está planejado.

Se você usar pools ' cloudServiceConfiguration ', é altamente recomendável que você mova para usar os pools ' virtualMachineConfiguration '. Isso permitirá que você se beneficie de todos os recursos do lote, como uma seleção expandida [de séries de VM](batch-pool-vm-sizes.md), VMS do Linux, [contêineres](batch-docker-container-workloads.md), [Azure Resource Manager redes virtuais](batch-virtual-network.md)e a [criptografia de disco do nó](disk-encryption.md).

Este artigo descreve como migrar para ' virtualMachineConfiguration '.

## <a name="new-pools-are-required"></a>Novos pools são necessários

Os pools ativos existentes não podem ser atualizados de ' cloudServiceConfiguration ' para ' virtualMachineConfiguration ', novos pools devem ser criados. A criação de pools usando ' virtualMachineConfiguration ' tem suporte em todas as APIs do lote, ferramentas de linha de comando, portal do Azure e a interface do usuário do Batch Explorer.

**Os tutoriais do [.net](tutorial-parallel-dotnet.md) e do [Python](tutorial-parallel-python.md) fornecem exemplos de criação de pool usando ' virtualMachineConfiguration '.**

## <a name="pool-configuration-differences"></a>Diferenças de configuração de pool

O seguinte deve ser considerado ao atualizar a configuração do pool:

- OS nós de pool ' cloudServiceConfiguration ' são sempre o sistema operacional Windows, os pools ' virtualMachineConfiguration ' podem ser sistemas operacionais Linux ou Windows.
- Em comparação com os pools ' cloudServiceConfiguration ', os pools ' virtualMachineConfiguration ' têm um conjunto mais rico de recursos, como suporte a contêiner, discos de dados e criptografia de disco.
- OS nós de pool ' virtualMachineConfiguration ' utilizam discos do sistema operacional gerenciados. O [tipo de disco gerenciado](../virtual-machines/disks-types.md) que é usado para cada nó depende do tamanho da VM escolhido para o pool. Se um ' tamanho da VM for especificado para o pool, por exemplo, ' Standard_D2s_v3 ', será usado um SSD Premium. Se um tamanho de VM ' não s' for especificado, por exemplo, ' Standard_D2_v3 ', será usado um HDD padrão.

   > [!IMPORTANT]
   > Assim como ocorre com máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais, o disco gerenciado do sistema operacional usado para cada nó provoca um custo, que é adicional ao custo das VMs. Não há custo de disco do sistema operacional para os nós ' cloudServiceConfiguration ', pois o disco do sistema operacional é criado no SSD local dos nós.

- Os tempos de exclusão e de inicialização do pool e do nó podem diferir ligeiramente entre os pools ' cloudServiceConfiguration ' e os pools ' virtualMachineConfiguration '.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as configurações de pool](nodes-and-pools.md#configurations).
- Saiba mais sobre [as práticas recomendadas de pool](best-practices.md#pools).
- Referência da API REST para [adição de pool](/rest/api/batchservice/pool/add) e [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).