---
title: Criar uma VNet para aplicativos de várias camadas – exemplo de script do Azure PowerShell
description: Criar uma rede virtual para aplicativos de várias camadas – exemplo de script do Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51cafb0a344c5b6226b3b033d8c60327eaf2c05f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267934"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Criar uma rede de exemplo do script de aplicativos de várias camadas

Este exemplo de script cria uma rede virtual com sub-redes de front-end e back-end. O tráfego para a sub-rede de front-end é limitado a HTTP e SSH, enquanto o tráfego para a sub-rede de back-end é limitado a MySQL, porta 3306. Depois de executar o script, você terá duas máquinas virtuais, um em cada sub-rede, nas quais você pode implantar o servidor Web e o software MySQL.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/powershell) ou de uma instalação local do PowerShell. Se você usar o PowerShell localmente, este script exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!-- gitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/17748 -->
Uma ID de sub-rede é atribuída depois que você cria uma rede virtual. Especificamente, usando o cmdlet New-AzVirtualNetwork com a opção -Subnet. Se você configurar a sub-rede usando o cmdlet New-AzVirtualNetworkSubnetConfig antes da chamada para New-AzVirtualNetwork, não verá a ID de sub-rede até depois de chamar New-AzVirtualNetwork.

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual do Azure e uma sub-rede front-end. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria uma sub-rede back-end. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público para acessar a VM da Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria as interfaces de rede virtual e as anexa às sub-redes de front-end e back-end da rede virtual. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria NSG (grupos de segurança de rede) associados às sub-redes de front-end e back-end. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |Cria regras NSG que permitem ou bloqueiam portas específicas para sub-redes específicas. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Cria máquinas virtuais e anexa um NIC a cada VM. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos e todos os seus recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/).

Amostras de script PowerShell de rede virtual adicionais podem ser encontradas em [Amostras de PowerShell de rede virtual](../powershell-samples.md).
