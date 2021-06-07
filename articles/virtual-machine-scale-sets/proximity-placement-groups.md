---
title: Visualização de grupos de posicionamento por proximidade para conjuntos de dimensionamento de máquinas virtuais
description: Saiba mais sobre como criar e usar grupos de posicionamento por proximidade para conjuntos de dimensionamento de máquinas virtuais do Windows no Azure.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: proximity-placement-groups
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: eeb0dd6cd5ce838c0f3a68763c40612038ee82f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933484"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Visualização: Criação e uso dos grupos de posicionamento por proximidade usando o PowerShell

Para colocar as VMs o mais próximo possível umas das outras, atingindo a menor latência possível, implante o conjunto de dimensionamento dentro de um [grupo de posicionamento por proximidade](../virtual-machines/co-location.md#proximity-placement-groups).

Um grupo de posicionamento por proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos uns dos outros. Os grupos de posicionamento por proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.

> [!IMPORTANT]
> Os grupos de posicionamento por proximidade estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Os grupos de posicionamento por proximidade não estão disponíveis nessas regiões durante a visualização: **Leste do Japão**, **Leste da Austrália** e **Índia Central**.


## <a name="create-a-proximity-placement-group"></a>Criar um grupo de posicionamento de proximidade
Crie um grupo de posicionamento por proximidade usando o cmdlet [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup). 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Lista de grupos de posicionamento por proximidade

Liste todos os grupos de posicionamento por proximidade usando o cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Criar um conjunto de escala

Crie uma escala no grupo de posicionamento por proximidade usando `-ProximityPlacementGroup $ppg.Id` para se referir à ID do grupo de posicionamento por proximidade quando usar [New-AzVMSS](/powershell/module/az.compute/new-azvmss) para criar o conjunto de dimensionamento.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Veja a instância no grupo de posicionamento usando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Próximas etapas

Você também pode usar a [CLI do Azure](../virtual-machines/linux/proximity-placement-groups.md) para criar grupos de posicionamento por proximidade.
