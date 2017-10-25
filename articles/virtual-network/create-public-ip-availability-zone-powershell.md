---
title: "Criar um endereço IP público zoneado com o PowerShell | Microsoft Docs"
description: "Crie um IP público em uma zona de disponibilidade com o PowerShell."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: bfeba57036338b4e325d2f122443f2cde0373eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>Criar um endereço IP público em uma zona de disponibilidade com o PowerShell

Você pode implantar um endereço IP público em uma zona de disponibilidade do Azure (versão prévia). Uma zona de disponibilidade é uma zona fisicamente separada em uma região do Azure. Saiba como:

> * Criar um endereço IP público em uma zona de disponibilidade
> * Identificar recursos relacionados criados na zona de disponibilidade
  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este artigo requer que você tenha a versão 4.4.0 ou superior do módulo AzureRM instalada. Para saber qual é a versão, execute `Get-Module -ListAvailable AzureRM`. Se você precisar instalar ou atualizar, instale a versão mais recente do módulo AzureRM por meio da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!NOTE]
> As zonas de disponibilidade estão em versão prévia e prontas para seus cenários de desenvolvimento e teste. O suporte está disponível para famílias de tamanhos de VM, regiões e recursos do Azure selecionados. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Neste exemplo, um grupo de recursos chamado *myResourceGroup* é criado na região *westeurope*. *westeurope* é uma das regiões do Azure que dá suporte a zonas de disponibilidade em versão prévia.

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>Criar um endereço IP público zonal

Crie um endereço IP público em uma zona de disponibilidade usando o seguinte comando:

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> Quando você atribui um endereço IP público de SKU Standard ao adaptador de rede de uma máquina virtual, você deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.

## <a name="get-zone-information-about-a-public-ip-address"></a>Obter informações de zona sobre um endereço IP público

Obter as informações de zona de um endereço IP público usando o seguinte comando:

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [zonas de disponibilidade](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
- Saiba mais sobre [endereços IP públicos](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 