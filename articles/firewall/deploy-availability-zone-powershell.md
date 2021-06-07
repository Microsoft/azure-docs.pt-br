---
title: Implantar o Firewall do Azure com Zonas de Disponibilidade usando o PowerShell
description: Neste artigo, você aprenderá a implantar um firewall do Azure com Zonas de Disponibilidade usando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 46ecc4754a064e26e61365ed6ca167606bef9d81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94656098"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Implantar um Firewall do Azure com zonas de disponibilidade usando o Azure PowerShell

O Firewall do Azure pode ser configurado durante a implantação para abranger várias Zonas de Disponibilidade para aumentar a disponibilidade.

Esse recurso habilita os seguintes cenários:

- Você pode aumentar a disponibilidade para 99,99% de tempo de atividade. Para saber mais, confira o [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) do Firewall do Azure. O SLA de tempo de atividade de 99,99% é oferecido quando duas ou mais Zonas de Disponibilidade estão selecionadas.
- Você também pode associar o Firewall do Azure a uma zona específica apenas por motivos de proximidade, usando o SLA de 99,95% padrão de serviço.

Para obter mais informações sobre Zonas de Disponibilidade de firewall do Azure, consulte [o que é o Firewall do Azure?](overview.md)

O exemplo a seguir Azure PowerShell mostra como você pode implantar um firewall do Azure com Zonas de Disponibilidade.

## <a name="create-a-firewall-with-availability-zones"></a>Criar um firewall com Zonas de Disponibilidade

Este exemplo cria um firewall nas zonas 1, 2 e 3.

Quando o endereço IP público padrão é criado, nenhuma zona específica é especificada. Isso cria um endereço IP com redundância de zona por padrão. Os endereços IP públicos padrão podem ser configurados em todas as zonas ou em uma única zona.

É importante saber, porque você não pode ter um firewall na zona 1 e um endereço IP na zona 2. Mas você pode ter um firewall na zona 1 e o endereço IP em todas as zonas, ou um firewall e um endereço IP na mesma zona única para fins de proximidade.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Monitorar os logs do Firewall do Azure](./firewall-diagnostics.md)