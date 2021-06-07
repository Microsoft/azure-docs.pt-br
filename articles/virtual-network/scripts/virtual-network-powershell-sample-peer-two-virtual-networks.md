---
title: Emparelhar duas redes virtuais – exemplo de script do Azure PowerShell
description: Crie e conecte duas redes virtuais na mesma região. Use o script do Azure para duas redes virtuais emparelhadas para conectar as redes por meio da rede do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d268fe602f8acdcf3f8d5eff312ff2805bd1d952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89075011"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Emparelhar exemplo de script de duas redes virtuais

Esse script cria e conecta duas redes virtuais na mesma região através da rede do Azure. Depois de executar o script, você criará um emparelhamento entre duas redes virtuais.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/powershell) ou de uma instalação local do PowerShell. Se você usar o PowerShell localmente, este script exigirá o módulo PowerShell Az versão 5.4.1 ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando da tabela a seguir é vinculado à documentação específica do comando:

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)| Cria uma sub-rede e uma rede virtual do Azure. |
| [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering) | Cria um emparelhamento entre duas redes virtuais.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/).

Amostras de script PowerShell de rede virtual adicionais podem ser encontradas em [Amostras de PowerShell de rede virtual](../powershell-samples.md).
