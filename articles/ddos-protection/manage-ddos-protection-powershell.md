---
title: Criar e configurar um plano de proteção contra DDoS do Azure usando o Azure PowerShell
description: Saiba como criar um plano de proteção contra DDoS usando Azure PowerShell
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 69f9b5a74566879ecf8f15f23e689ebb731da45a
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814135"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Início rápido: criar e configurar a proteção contra DDoS do Azure Standard usando o Azure PowerShell

Introdução ao padrão de proteção contra DDoS do Azure usando Azure PowerShell. 

Um plano de proteção contra DDoS define um conjunto de redes virtuais que têm um padrão de proteção DDoS habilitada, entre assinaturas. Você pode configurar um plano de proteção contra DDoS para sua organização e vincular redes virtuais de várias assinaturas para o mesmo plano. 

Neste guia de início rápido, você criará um plano de proteção contra DDoS e o vinculará a uma rede virtual. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção contra DDoS

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo.

Para criar um grupo de recursos, use [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Neste exemplo, vamos nomear nosso grupo de recursos _MyResource_ Group e usar o local _leste dos EUA_ :

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Agora, crie um plano de proteção contra DDoS chamado _MyDdosProtectionPlan_:

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>Habilitar DDoS para uma rede virtual

### <a name="enable-ddos-for-a-new-virtual-network"></a>Habilitar DDoS para uma nova rede virtual

Você pode habilitar a proteção contra DDoS ao criar uma rede virtual. Neste exemplo, vamos nomear nossa rede virtual _MyVnet_: 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>Habilitar DDoS para uma rede virtual existente

Você pode associar uma rede virtual existente ao criar um plano de proteção contra DDoS:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Validar e testar

Primeiro, verifique os detalhes do seu plano de proteção contra DDoS:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Verifique se o comando retorna os detalhes corretos do seu plano de proteção contra DDoS.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter seus recursos para o próximo tutorial. Se não for mais necessário, exclua o grupo de recursos _MyResource_ Group. Ao excluir o grupo de recursos, você também exclui o plano de proteção contra DDoS e todos os seus recursos relacionados. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Para desabilitar a proteção contra DDoS para uma rede virtual: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

Se você quiser excluir um plano de proteção contra DDoS, deverá primeiro dissociar todas as redes virtuais dele.

## <a name="next-steps"></a>Próximas etapas

Para saber como exibir e configurar a telemetria para seu plano de proteção contra DDoS, continue com os tutoriais.

> [!div class="nextstepaction"]
> [Exibir e configurar a telemetria da Proteção contra DDoS](telemetry.md)