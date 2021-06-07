---
title: Criar um IP público-Azure PowerShell
description: Saiba como criar um IP público usando Azure PowerShell
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: f81e76e32bc0a50b945c54cdfcac0575540484bc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502426"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Início rápido: criar um endereço IP público usando Azure PowerShell

Este artigo mostra como criar um recurso de endereço IP público usando Azure PowerShell. Para obter mais informações sobre a quais recursos isso pode ser associado, a diferença entre o SKU básico e Standard e outras informações relacionadas, consulte [endereços IP públicos](./public-ip-addresses.md).  Para este exemplo, nos concentraremos somente nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para VNet do Azure](./ipv6-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) chamado **MyResource** Group no local **eastus2** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Criar IP público

---
# <a name="standard-sku---using-zones"></a>[**SKU padrão-usando zonas**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>O comando a seguir funciona para o módulo AZ. Network versão 4.5.0 ou posterior.  Para obter mais informações sobre os módulos do PowerShell que estão sendo usados no momento, consulte a [documentação do PowerShellGet](/powershell/module/powershellget/).

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público com redundância de zona padrão chamado **MyStandardZRPublicIP** no **myresourceproperties**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> Para módulos AZ. Network com mais de 4.5.0, execute o comando acima sem especificar um parâmetro de zona para criar um endereço IP com redundância de zona. 
>

Para criar um endereço IP público de zona padrão no Zona 2 chamado **myStandardZonalPublicIP** no **MyResource**, use o seguinte comando:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Observe que as opções acima para zonas são apenas seleções válidas em regiões com [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**SKU padrão-sem zonas**](#tab/option-create-public-ip-standard)

>[!NOTE]
>O comando a seguir funciona para o módulo AZ. Network versão 4.5.0 ou posterior.  Para obter mais informações sobre os módulos do PowerShell que estão sendo usados no momento, consulte a [documentação do PowerShellGet](/powershell/module/powershellget/).

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público padrão como um recurso não zonal chamado **MyStandardPublicIP** no **myresourceproperties**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Essa seleção é válida em todas as regiões e é a seleção padrão para endereços IP públicos padrão em regiões sem [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público estático básico chamado **MyBasicPublicIP** no **MyResource**.  Os IPs públicos básicos não têm o conceito de zonas de disponibilidade.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** poderá ser selecionada alterando o AllocationMethod para ' dinâmico '.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre as variáveis individuais listadas acima, consulte [gerenciar endereços IP públicos](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Próximas etapas
- Associar um [endereço IP público a uma máquina virtual](./associate-public-ip-address-vm.md#azure-portal)
- Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).