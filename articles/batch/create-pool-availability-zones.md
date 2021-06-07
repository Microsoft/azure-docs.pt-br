---
title: Criar um pool entre zonas de disponibilidade
description: Saiba como criar um pool do lote com a política de zonal para ajudar a proteger contra falhas.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 56e718bedf504b8e69598c2d99ab8b889a470b89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725281"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Criar um pool do lote do Azure entre Zonas de Disponibilidade

Regiões do Azure que dão suporte a [zonas de disponibilidade](https://azure.microsoft.com/global-infrastructure/availability-zones/) têm um mínimo de três zonas separadas, cada uma com sua própria fonte de energia, rede e sistema de resfriamento independentes. Ao criar um pool do lote do Azure usando a configuração de máquina virtual, você pode optar por provisionar seu pool do lote em Zonas de Disponibilidade. Criar seu pool com essa política zonal ajuda a proteger seus nós de computação do lote de falhas no nível do datacenter do Azure.

Por exemplo, você pode criar seu pool com a política zonal em uma região do Azure que dá suporte a três Zonas de Disponibilidade. Se um datacenter do Azure em uma zona de disponibilidade tiver uma falha de infraestrutura, o pool do lote ainda terá nós íntegros nos outros dois Zonas de Disponibilidade, de modo que o pool permanecerá disponível para o agendamento de tarefas.

## <a name="regional-support-and-other-requirements"></a>Suporte regional e outros requisitos

O lote mantém a paridade com o Azure para dar suporte a Zonas de Disponibilidade. Para usar a opção zonal, o pool deve ser criado em uma [região do Azure com suporte](../availability-zones/az-region.md).

Para que o pool do lote seja alocado entre as zonas de disponibilidade, a região do Azure na qual o pool é criado deve dar suporte à SKU de VM solicitada em mais de uma zona. Você pode validar isso chamando a [API de lista de SKUs de recursos](/rest/api/compute/resourceskus/list) e verificar o campo **locationInfo** de [resourceSku](/rest/api/compute/resourceskus/list#resourcesku). Certifique-se de que há suporte para mais de uma zona para a SKU de VM solicitada.

Para [contas do lote no modo de assinatura do usuário](accounts.md#batch-accounts), verifique se a assinatura na qual você está criando o pool não tem uma restrição de oferta de zona no SKU de VM solicitado. Para confirmar isso, chame a [API da lista de SKUs de recursos](/rest/api/compute/resourceskus/list) e verifique o [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions). Se houver uma restrição de zona, você poderá enviar um [tíquete de suporte](/troubleshoot/azure/general/region-access-request-process) para remover a restrição de zona.

Observe também que você não poderá criar um pool com uma política zonal se ela tiver a comunicação entre nós habilitada e usar uma [SKU de VM que dá suporte a InfiniBand](../virtual-machines/workloads/hpc/enable-infiniband.md).

## <a name="create-a-batch-pool-across-availability-zones"></a>Criar um pool do lote entre Zonas de Disponibilidade

Os exemplos a seguir mostram como criar um pool do lote entre Zonas de Disponibilidade.

> [!NOTE]
> Ao criar seu pool com uma política de zonal, o serviço de lote tentará alocar seu pool em todos os Zonas de Disponibilidade na região selecionada; Você não pode especificar uma alocação específica entre as zonas.

### <a name="batch-management-client-net-sdk"></a>SDK do .NET do cliente de gerenciamento de lote

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>API REST do Lote

URL DA API REST

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Corpo da solicitação

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- Saiba como [criar um pool em uma sub-rede de uma rede virtual do Azure](batch-virtual-network.md).
- Saiba como [criar um pool do lote do Azure sem endereços IP públicos](./batch-pool-no-public-ip-address.md).