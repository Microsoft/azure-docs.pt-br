---
title: Cotas do vCPU
description: Saiba mais sobre as cotas de vCPU do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: quota
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 1b9c0d50754d582ca7ada5d0b46c6f998b59d3ae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549559"
---
# <a name="check-vcpu-quotas-using-the-azure-cli"></a>Verifique as cotas do vCPU usando o CLI do Azure

As cotas de vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais são organizadas em duas camadas para cada assinatura, em cada região. A primeira camada é a vCPUs Total Regional e a segunda camada são os vários núcleos da família de tamanho da VM, como as vCPUs da série D. Sempre que uma nova VM é implantada a vCPUs para a máquina virtual não deve exceder a cota de vCPU para a família de tamanho VM ou a cota total vCPU regional. Se qualquer uma das cotas é excedida, a implantação de VM não será permitida. Também há uma cota para o número total de máquinas virtuais na região. Os detalhes sobre cada uma dessas cotas podem ser vistos no **uso + cotas** seção o **assinatura** página o [portal do Azure](https://portal.azure.com), ou você pode consultar os valores usando o Azure CLI.

> [!NOTE]
> A cota é calculada com base no número total de núcleos em alocados e desalocados. Se precisar de núcleos adicionais, [solicite um aumento de cota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) ou exclua VMs desnecessárias. 


## <a name="check-usage"></a>Verificar o uso

Você pode verificar o uso de cotas usando [az vm list-usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

A saída deve parecer com esta:


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

Se um aumento de cota for necessário para adquirir um RI assinatura única, você pode [solicitar um aumento de cota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) na sua assinatura.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre cobrança e cotas, confira [Assinatura do Azure e limite de serviços, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json).
