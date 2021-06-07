---
title: Configurar a preferência de roteamento para um endereço IP público - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Saiba como configurar a preferência de roteamento para um endereço IP público usando o Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 5fac791247dbb785e087ac21f3fd5532064c6c8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667886"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Configurar a preferência de roteamento para um endereço IP público usando o Azure PowerShell

Este artigo mostra como configurar a preferência de roteamento via rede ISP (opção **Internet**) para um endereço IP público usando o Azure PowerShell. Depois de criar o endereço IP público, você pode associá-lo aos seguintes recursos do Azure para os tráfegos de entrada e saída para a Internet:

* Máquina virtual
* Conjunto de escala de máquina virtual
* AKS (Serviço de Kubernetes do Azure)
* Balanceador de carga voltado para a Internet
* Gateway de Aplicativo
* Firewall do Azure

Por padrão, a preferência de roteamento para o endereço IP público é definida para a rede global da Microsoft para todos os serviços do Azure e pode ser associada a qualquer serviço do Azure.

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 6.9.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na região *eastus*:

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>Criar um IP Público com a preferência de roteamento da Internet

O comando a seguir cria um novo IP público com um tipo *Internet* para a preferência de roteamento na região *Leste dos EUA* do Azure:

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

Você pode associar o endereço IP público criado acima a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Use a seção CLI na página do tutorial: [Associe um endereço IP público a uma máquina virtual](associate-public-ip-address-vm.md#azure-cli) para associar o IP Público à sua VM. Você também pode associar o endereço IP público criado acima a um [Azure Load Balancer](../load-balancer/load-balancer-overview.md), atribuindo-o à configuração **front-end** do balanceador de carga. O endereço IP público serve como um endereço IP virtual de balanceamento de carga (VIP).

## <a name="clean-up-resources"></a>Limpar os recursos

Se não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [preferência de roteamento em endereços IP públicos](routing-preference-overview.md).
- [Configure a preferência de roteamento para uma VM usando o Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md).
