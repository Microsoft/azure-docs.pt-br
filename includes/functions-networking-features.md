---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936746"
---


| Recurso |[Plano de Consumo](../articles/azure-functions/consumption-plan.md)|[Plano Premium](../articles/azure-functions/functions-premium-plan.md)|[Plano dedicado](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrições de IP de entrada e acesso ao site privado](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Sim|✅Sim|✅Sim|✅Sim|✅Sim|
|[Integração de rede virtual](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Não|✅Sim (Regional)|✅Sim (regional e gateway)|✅Sim| ✅Sim|
|[Gatilhos de rede virtual (não HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Não| ✅Sim |✅Sim|✅Sim|✅Sim|
|[Conexões híbridas](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (somente Windows)|❌Não|✅Sim|✅Sim|✅Sim|✅Sim|
|[Restrições de IP de saída](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Não| ✅Sim|✅Sim|✅Sim|✅Sim|