---
title: Cotas de vCPU do Azure | Microsoft Docs
description: Saiba mais sobre as cotas de vCPU do Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 40fd91ed39731b95cf01a627e3178ab6cfaaa7d3
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670995"
---
# <a name="virtual-machine-vcpu-quotas"></a>Cotas de vCPU de máquina virtual

As cotas de vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais são organizadas em duas camadas para cada assinatura, em cada região. A primeira camada é a vCPUs Total Regional e a segunda camada são os vários núcleos da família de tamanho da VM, como as vCPUs da série D. Sempre que uma nova VM é implantada a vCPUs para a máquina virtual não deve exceder a cota de vCPU para a família de tamanho VM ou a cota total vCPU regional. Se qualquer uma das cotas é excedida, a implantação de VM não será permitida. Também há uma cota para o número total de máquinas virtuais na região. Os detalhes sobre cada uma dessas cotas podem ser vistos no **uso + cotas** seção o **assinatura** página o [portal do Azure](https://portal.azure.com), ou você pode consultar os valores usando o Azure CLI.


## <a name="check-usage"></a>Verificar o uso

Você pode verificar o uso de cotas usando [az vm list-usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

A saída deve ser semelhante a esta:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Instâncias de máquina virtual reservada
Instâncias de máquina virtual reservada, que têm o escopo voltado para uma assinatura única sem flexibilidade para o tamanho da VM, adicionarão um novo aspecto às cotas de vCPU. Esses valores descrevem o número de instâncias de tamanho indicado que devem ser implantadas na assinatura. Eles funcionam como um espaço reservado no sistema de cotas para garantir que a cota seja reservada para garantir que as reservas do Azure sejam implantadas na assinatura. Por exemplo, se uma assinatura específica tiver 10 reservas Standard_D1, o limite de uso para as reservas Standard_D1 será 10. Isso fará com que o Azure garanta que haja sempre pelo menos 10 vCPUs disponíveis na cota de vCPUs Regionais Totais para serem usados para instâncias Standard_D1 e pelo menos 10 VCPUs disponíveis na cota de vCPU Família D padrão para serem usados para as instâncias Standard_D1.

Se um aumento de cota for necessário para adquirir um RI assinatura única, você pode [solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) na sua assinatura.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre cobrança e cotas, confira [Assinatura do Azure e limite de serviços, cotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
