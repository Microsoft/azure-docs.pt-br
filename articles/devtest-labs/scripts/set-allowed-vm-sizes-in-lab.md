---
title: 'Script do PowerShell: Definir tamanhos de VM permitidos no Azure Lab Services | Microsoft Docs'
description: Este artigo inclui um exemplo de script do PowerShell que define os tamanhos de VM (máquina virtual) permitidos no Azure Lab Services.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998118"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Usar o PowerShell para definir os tamanhos de VM permitidos no Azure Lab Services

Este exemplo de script do PowerShell define os tamanhos de VM (máquinas virtuais) permitidos no Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório**. O script exige que você tenha um laboratório existente. 

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| Find-AzResource | Pesquisas recursos com base nos parâmetros especificados. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obter recursos. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica um recurso. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Criar um recurso. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).

Exemplos adicionais de script do PowerShell do Azure Lab Services podem ser encontrados em [Exemplos de PowerShell do Azure Lab Services](../samples-powershell.md).
