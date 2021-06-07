---
title: Criar um pool com endereços IP públicos especificados
description: Saiba como criar um pool do lote que usa seus próprios endereços IP públicos.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91849321"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Criar um pool do lote do Azure com endereços IP públicos especificados

Ao criar um pool do lote do Azure, você pode [provisionar o pool em uma sub-rede de uma VNet (rede virtual](batch-virtual-network.md) ) do Azure que você especificar. As máquinas virtuais no pool do lote são acessadas por meio de endereços IP públicos criados pelo lote. Esses endereços IP públicos podem ser alterados durante o tempo de vida do pool, o que significa que as configurações de rede poderão ficar desatualizadas se os endereços IP não forem atualizados.

Você pode criar uma lista de endereços IP públicos estáticos para usar com as máquinas virtuais em seu pool. Isso permite que você controle a lista de endereços IP públicos e certifique-se de que eles não serão alterados inesperadamente. Isso pode ser especialmente útil se você estiver trabalhando com qualquer serviço externo, como um banco de dados, que restringe o acesso a determinados endereços IP.

Para obter informações sobre como criar pools sem endereços IP públicos, leia [criar um pool do lote do Azure sem endereços IP públicos](./batch-pool-no-public-ip-address.md).

## <a name="prerequisites"></a>Pré-requisitos

- **Autenticação**. Para usar um endereço IP público, a API do cliente do lote deve usar a [autenticação Azure Active Directory (AD)](batch-aad-auth.md).

- **Uma rede virtual do Azure**. Você deve usar uma [rede virtual](batch-virtual-network.md) da mesma assinatura do Azure na qual está criando o pool e seus endereços IP. Somente VNets com base em Azure Resource Manager podem ser usados. Certifique-se de que a VNet atenda a todos os [requisitos gerais](batch-virtual-network.md#vnet-requirements).

- **Pelo menos um endereço IP público do Azure**. Para criar um ou mais endereços IP públicos, você pode usar o [portal do Azure](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), a [CLI (interface de Command-Line do Azure)](/cli/azure/network/public-ip#az-network-public-ip-create)ou o [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Certifique-se de seguir os requisitos listados abaixo.

> [!NOTE]
> O lote aloca automaticamente recursos de rede adicionais no grupo de recursos que contém os endereços IP públicos. Para cada um dos nós dedicados de 100, o lote geralmente aloca um NSG (grupo de segurança de rede) e um balanceador de carga. Esses recursos são limitados pelas cotas de recursos da assinatura. Ao usar pools maiores, talvez seja necessário [solicitar um aumento de cota](batch-quota-limit.md#increase-a-quota) para um ou mais desses recursos.

## <a name="public-ip-address-requirements"></a>Requisitos de endereço IP público

Tenha em mente os seguintes requisitos ao criar seus endereços IP públicos:

- Os endereços IP públicos devem estar na mesma assinatura e região que a conta do lote usada para criar o pool.
- A **atribuição de endereço IP** deve ser definida como **estática**.
- O **SKU** deve ser definido como **Standard**.
- Um nome DNS deve ser especificado.
- Os endereços IP públicos devem ser usados somente para os pools de configuração de máquina virtual. Nenhum outro recurso deve usar esses endereços IP ou o pool pode apresentar falhas de alocação.
- Nenhuma política de segurança ou bloqueios de recursos deve restringir o acesso de um usuário ao endereço IP público.
- O número de endereços IP públicos especificados para o pool deve ser grande o suficiente para acomodar o número de VMs direcionadas para o pool. Deve ser pelo menos a soma das propriedades **targetDedicatedNodes**   e **targetLowPriorityNodes**   do pool. Se não houver endereços IP suficientes, o pool alocará parcialmente os nós de computação e um erro de redimensionamento ocorrerá. Atualmente, o lote usa um endereço IP público para cada 100 VMs.
- Sempre tenha um buffer adicional de endereços IP públicos. É recomendável adicionar pelo menos um endereço IP público adicional ou aproximadamente 10% do total de endereços IP públicos que você adiciona a um pool, o que for maior. Esse buffer adicional ajudará o lote com sua otimização interna ao reduzir verticalmente, bem como permitir a expansão mais rápida após uma redução ou redução vertical de uma falha.
- Depois que o pool é criado, você não pode adicionar ou alterar a lista de endereços IP públicos usados pelo pool. Se você precisar modificar a lista, deverá excluir o pool e, em seguida, recriá-lo.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Criar um pool do lote com endereços IP públicos

O exemplo a seguir mostra como usar a [API REST do serviço de lote do Azure](/rest/api/batchservice/pool/add) para criar um pool que usa endereços IP públicos.

### <a name="batch-service-rest-api"></a>API REST do Serviço em Lotes

URI da API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Corpo da solicitação

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- Saiba como [criar um pool em uma sub-rede de uma rede virtual do Azure](batch-virtual-network.md).
- Saiba como [criar um pool do lote do Azure sem endereços IP públicos](./batch-pool-no-public-ip-address.md).

