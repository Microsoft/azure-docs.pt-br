---
title: Mover uma VM no usando o CLI do Azure
description: Mova uma VM para outra assinatura ou grupo de recursos do Azure usando o CLI do Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 7dbe06a9f2fff8abf59adbdfc9e41055c85e8f2c
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889290"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Mover uma VM para outro grupo de recursos ou outra assinatura
Este artigo explica como mover uma VM (máquina virtual) entre grupos de recursos ou assinaturas. Mover uma VM entre assinaturas poderá ser útil se você tiver criado uma VM em uma assinatura pessoal e agora quiser movê-la para a assinatura da sua empresa.

> [!IMPORTANT]
>Novas IDs de recurso são criadas como parte da mudança. Depois que a VM for movida, você precisará atualizar suas ferramentas e scripts para usar as novas IDs de recursos.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Usar a CLI do Azure para mover uma VM


Antes de mover sua VM usando a CLI do Azure, você precisa verificar se as assinaturas de origem e destino existem no mesmo inquilino. Para verificar se as duas assinaturas têm a mesma ID de locatário, use [az account show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Se as IDs do locatário para as assinaturas de origem e de destino não forem iguais, entre em contato com o [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) para mover os recursos para um novo locatário.

Para mover uma VM com êxito, você precisa mover a VM e todos os seus recursos de suporte. Use o comando [az resource list](/cli/azure/resource) para listar todos os recursos em um grupo de recursos e suas IDs. Ele ajuda a direcionar a saída desse comando para um arquivo de modo que seja possível copiar e colar as IDs em comandos posteriores.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```
A `table` saída não estará disponível se você usar `--interactive` . Altere a saída para outra opção, como `json` .

Para mover uma VM e seus recursos para outro grupo de recursos, use [az resource move](/cli/azure/resource). O exemplo a seguir mostra como mover uma VM e os recursos mais comuns que ela exige. Use o parâmetro **-ids** e passe-o em uma lista de IDs separadas por vírgulas (sem espaços) para os recursos a serem movidos.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Se quiser mover a VM e seus recursos para outra assinatura, adicione o parâmetro **--destination-subscriptionId** para especificar a assinatura de destino.

Quando for solicitado que você confirme que deseja mover os recursos especificados, insira **Y** para confirmar.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Próximas etapas
Você pode mover vários tipos diferentes de recursos entre grupos de recursos e assinaturas. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
