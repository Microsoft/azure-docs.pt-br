---
title: Exemplos do Azure PowerShell – Instalar aplicativos
description: Esse script cria um conjunto de dimensionamento de máquinas virtuais executando o Windows Server 2016 e usa a Extensão de Script Personalizado para instalar um aplicativo Web básico.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: ea4dbf06bd1c129131d57748986e22feca80d808
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89079601"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-powershell"></a>Instalar aplicativos em um conjunto de dimensionamento de máquinas virtuais com o PowerShell
Esse script cria um conjunto de dimensionamento de máquinas virtuais executando o Windows Server 2016 e usa a Extensão de Script Personalizado para instalar um aplicativo Web básico. Depois de executar o script, é possível acessar o aplicativo Web por meio de navegador da Web.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/install-apps/install-apps.ps1 "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Cria o conjunto de dimensionamento de máquinas virtuais e todos os recursos de suporte, incluindo a rede virtual, o balanceador de carga e as regras de NAT. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Obtém informações sobre um conjunto de dimensionamento de máquinas virtuais. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Adiciona uma extensão de VM para um Script personalizado para instalar um aplicativo Web básico. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Atualiza o modelo de conjunto de dimensionamento de máquinas virtuais para aplicar a extensão de VM. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Obtém informações sobre o endereço IP público atribuído usado pelo balanceador de carga. |
|  [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).
