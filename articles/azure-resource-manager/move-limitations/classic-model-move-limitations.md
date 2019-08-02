---
title: Mover os recursos de implantação clássico do Azure para nova assinatura ou grupo de recursos
description: Use o Azure Resource Manager para mover os recursos de implantação clássico para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 4770f957b6b9eea75b50776a7491b1ca479e50e2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723500"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Mover a orientação para os recursos de modelo de implantação clássico

As etapas para mover os recursos implantados por meio do modelo clássico diferem com base em se você estiver movendo os recursos dentro de uma assinatura ou para uma nova assinatura.

## <a name="move-in-the-same-subscription"></a>Mover na mesma assinatura

Ao mover recursos de um grupo de recursos para outro na mesma assinatura, as seguintes restrições se aplicarão:

* Redes virtuais (clássicas) não podem ser movidas.
* Máquinas virtuais (clássicas) devem ser movidas com o serviço de nuvem.
* Um serviço de nuvem pode ser movido apenas quando a movimentação incluir todas as suas máquinas virtuais.
* Apenas um serviço de nuvem pode ser movido por vez.
* Apenas uma conta de armazenamento (clássica) pode ser movida por vez.
* Uma conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou um serviço de nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma assinatura, use o [operações de movimentação padrão](../resource-group-move-resources.md) por meio do portal, PowerShell do Azure, CLI do Azure ou API REST. Use as mesmas operações como você usa para mover os recursos do Resource Manager.

## <a name="move-across-subscriptions"></a>Mover entre assinaturas

Ao mover recursos para uma nova assinatura, as seguintes restrições se aplicarão:

* Todos os recursos clássicos na assinatura devem ser movidos na mesma operação.
* A assinatura de destino não deve ter nenhum outro recurso clássico.
* A movimentação pode ser solicitada apenas por meio de uma API REST separada para movimentações clássicas. Os comandos de movimentação padrão do Gerenciador de Recursos não funcionam quando há uma movimentação dos recursos clássicos para uma nova assinatura.

Para mover recursos clássicos para uma nova assinatura, use operações REST específicas para recursos clássicos. Para usar o REST, execute as seguintes etapas:

1. Verifique se a assinatura de origem pode participar de uma movimentação entre assinaturas. Use a operação a seguir:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No corpo da solicitação, inclua:

   ```json
   {
    "role": "source"
   }
   ```

     A resposta para a operação de validação é no seguinte formato:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Verifique se a assinatura de destino pode participar de uma movimentação entre assinaturas. Use a operação a seguir:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No corpo da solicitação, inclua:

   ```json
   {
    "role": "target"
   }
   ```

     A resposta está no mesmo formato que a validação de assinatura de origem.
1. Se ambas as assinaturas forem aprovadas na validação, mova todos os recursos clássicos de uma assinatura para outra, use a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    No corpo da solicitação, inclua:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

A operação pode executar por vários minutos.

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas para mover recursos clássicos, entre em contato com [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../resource-group-move-resources.md) (Mover recursos para o novo grupo de recursos ou assinatura).
