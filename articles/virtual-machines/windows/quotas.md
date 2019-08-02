---
title: Cotas de vCPU do Azure | Microsoft Docs
description: Saiba mais sobre as cotas de vCPU do Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 8912387583c24945de22bcb029d40e4d4766b1fb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719871"
---
# <a name="virtual-machine-vcpu-quotas"></a>Cotas de vCPU de máquina virtual

As cotas de vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais são organizadas em duas camadas para cada assinatura, em cada região. A primeira camada é a vCPUs Total Regional e a segunda camada são os vários núcleos da família de tamanho da VM, como as vCPUs da série D. Sempre que uma nova VM é implantada a vCPUs para a máquina virtual não deve exceder a cota de vCPU para a família de tamanho VM ou a cota total vCPU regional. Se qualquer uma das cotas é excedida, a implantação de VM não será permitida. Também há uma cota para o número total de máquinas virtuais na região. Os detalhes sobre cada uma dessas cotas podem ser vistos na seção **uso + cotas** da página de **assinatura** no [portal do Azure](https://portal.azure.com), ou você pode consultar os valores usando o PowerShell.

 [!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)] 
 
## <a name="check-usage"></a>Verificar o uso

Você pode usar o cmdlet [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) para verificar o seu uso de cotas.

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

Se um aumento de cota for necessário para comprar uma RI de Assinatura Única, você poderá [solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) na sua assinatura.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre cobrança e cotas, confira [Assinatura do Azure e limite de serviços, cotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
