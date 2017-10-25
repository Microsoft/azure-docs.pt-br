---
title: Mover uma VM do Linux no Azure | Microsoft Docs
description: "Mova uma VM Linux para outro grupo de recursos ou outra assinatura do Azure no modelo de implantação do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 4695a9c934f97f2b2d448c4990e7ad5533e38e9f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Mover uma VM Linux para outro grupo de recursos ou outra assinatura
Este artigo explica como mover uma VM Linux entre grupos de recursos ou assinaturas. Mover uma VM entre assinaturas poderá ser útil se você tiver criado uma VM em uma assinatura pessoal e agora quiser movê-la para a assinatura da sua empresa.

> [!IMPORTANT]
>No momento, não é possível mover o Managed Disks. 
>
>Novas IDs de recurso são criadas como parte da mudança. Após a mudança da VM, você precisa atualizar ferramentas e scripts para usar as novas IDs de recurso. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Usar a CLI do Azure para mover uma VM
Para mover uma VM com êxito, você precisa mover a VM e todos os seus recursos de suporte. Use o comando **azure group show** para listar todos os recursos em um grupo de recursos e suas IDs. Ele ajuda a direcionar a saída desse comando para um arquivo de modo que seja possível copiar e colar as IDs em comandos posteriores.

    azure group show <resourceGroupName>

Para mover uma VM e seus recursos para outro grupo de recursos, use o comando da CLI **azure resource move** . O exemplo a seguir mostra como mover uma VM e os recursos mais comuns que ela exige. Usamos o parâmetro **-i** e o passamos em uma lista de IDs separadas por vírgulas (sem espaços) para os recursos a serem movidos.

    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      

    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"

Se quiser mover a VM e seus recursos para outra assinatura, adicione o parâmetro **--destination-subscriptionId &#60;destinationSubscriptionID&#62;** para especificar a assinatura de destino.

Se estiver trabalhando no Prompt de Comando em um computador Windows, você precisará adicionar um **$** na frente dos nomes de variável quando os declarar. Isso não é necessário no Linux.

Será solicitado que você confirme que deseja mover o recurso especificado. Digite **Y** para confirmar que deseja mover os recursos.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Próximas etapas
Você pode mover vários tipos diferentes de recursos entre grupos de recursos e assinaturas. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](../../resource-group-move-resources.md).    

