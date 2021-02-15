---
title: Cotas de vCPU para o Azure
description: Saiba mais sobre as cotas de vCPU para máquinas virtuais do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: b00d84e1c2dc8f104046fe24360d79e6952dfacc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88950282"
---
# <a name="check-vcpu-quotas-using-azure-powershell"></a>Verifique as cotas do vCPU usando Azure PowerShell

As cotas de vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais são organizadas em duas camadas para cada assinatura, em cada região. A primeira camada é a vCPUs Total Regional e a segunda camada são os vários núcleos da família de tamanho da VM, como as vCPUs da série D. Sempre que uma nova VM é implantada a vCPUs para a máquina virtual não deve exceder a cota de vCPU para a família de tamanho VM ou a cota total vCPU regional. Se qualquer uma das cotas é excedida, a implantação de VM não será permitida. Também há uma cota para o número total de máquinas virtuais na região. Os detalhes sobre cada uma dessas cotas podem ser vistos na seção **uso + cotas** da página de **assinatura** no [portal do Azure](https://portal.azure.com), ou você pode consultar os valores usando o PowerShell.

> [!NOTE]
> A cota é calculada com base no número total de núcleos em alocados e desalocados. Se precisar de núcleos adicionais, [solicite um aumento de cota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) ou exclua VMs desnecessárias. 
 
## <a name="check-usage"></a>Verificar o uso

Você pode usar o cmdlet [Get-AzVMUsage](/powershell/module/az.compute/get-azvmusage) para verificar o seu uso de cotas.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

A saída parecerá com o seguinte:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Instâncias de máquina virtual reservada
Instâncias de máquina virtual reservada, que têm o escopo voltado para uma assinatura única sem flexibilidade para o tamanho da VM, adicionarão um novo aspecto às cotas de vCPU. Esses valores descrevem o número de instâncias de tamanho indicado que devem ser implantadas na assinatura. Eles funcionam como um espaço reservado no sistema de cotas para garantir que a cota seja reservada para garantir que instâncias de VMs reservadas sejam implantadas na assinatura. Por exemplo, se uma assinatura específica tiver 10 instâncias de VM reservadas Standard_D1, o limite de uso para instâncias de VMs reservadas Standard_D1 será 10. Isso fará com que o Azure garanta que haja sempre pelo menos 10 vCPUs disponíveis na cota de vCPUs Regionais Totais para serem usados para instâncias Standard_D1 e pelo menos 10 VCPUs disponíveis na cota de vCPU Família D padrão para serem usados para as instâncias Standard_D1.

Se um aumento de cota for necessário para comprar uma RI de Assinatura Única, você poderá [solicitar um aumento de cota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) na sua assinatura.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre cobrança e cotas, confira [Assinatura do Azure e limite de serviços, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json).
