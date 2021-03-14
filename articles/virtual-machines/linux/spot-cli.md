---
title: Usar a CLI para implantar máquinas virtuais do Azure Spot
description: Saiba como usar a CLI para implantar máquinas virtuais do Azure spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0a7be682f921efdfae486e8f6545758964a941ae
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098852"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Implantar máquinas virtuais do Azure Spot usando o CLI do Azure

O uso de [máquinas virtuais do Azure Spot](../spot-vms.md) permite que você aproveite a capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as máquinas virtuais do Azure Spot. Portanto, as máquinas virtuais de ponto do Azure são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, cargas de trabalho de computação grande e muito mais.

Os preços para as máquinas virtuais do Azure Spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Você tem a opção de definir um preço máximo que está disposto a pagar, por hora, para a VM. O preço máximo de uma máquina virtual de ponto do Azure pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765`seria um preço máximo de $0,98765 USD por hora. Se você definir o preço máximo como `-1` , a VM não será removida com base no preço. O preço da VM será o preço atual para a máquina virtual do Azure spot ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível. Para obter mais informações sobre como definir o preço máximo, consulte [máquinas virtuais do Azure Spot – preços](../spot-vms.md#pricing).

O processo para criar uma máquina virtual do Azure Spot usando o CLI do Azure é o mesmo detalhado no [artigo de início rápido](./quick-create-cli.md). Basta adicionar o parâmetro '--priority spot ', definir `--eviction-policy` como desalocar (esse é o padrão) ou `Delete` e fornecer um preço máximo ou `-1` . 


## <a name="install-azure-cli"></a>Instalar a CLI do Azure.

Para criar máquinas virtuais do Azure Spot, você precisa estar executando o CLI do Azure versão 2.0.74 ou posterior. Execute **az --version** para descobrir a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

Entrar no Azure usando [login az](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Criar uma máquina virtual do Azure Spot

Este exemplo mostra como implantar uma máquina virtual de ponto do Linux Azure que não será removida com base no preço. A política de remoção é definida para desalocar a VM, para que ela possa ser reiniciada posteriormente. Se você quiser excluir a VM e o disco subjacente quando a VM for removida, defina `--eviction-policy` como `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Depois que a VM for criada, você poderá consultar para ver o preço máximo de cobrança de todas as VMs no grupo de recursos.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simular uma remoção

Você pode [simular uma remoção](/rest/api/compute/virtualmachines/simulateeviction) de uma máquina virtual de ponto do Azure, para testar como seu aplicativo responderá a uma remoção repentina. 

Substitua o seguinte pelas suas informações: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```
`Response Code: 204` significa que a remoção simulada foi bem-sucedida. 

**Próximas etapas**

Você também pode criar uma máquina virtual do Azure Spot usando [Azure PowerShell](../windows/spot-powershell.md), [portal](../spot-portal.md)ou um [modelo](spot-template.md).

Consulte as informações de preços atuais usando a [API de preços de varejo do Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) para obter informações sobre a máquina virtual do Azure Spot. O `meterName` e `skuName` os dois conterão `Spot` .

Se você encontrar um erro, consulte [códigos de erro](../error-codes-spot.md).
