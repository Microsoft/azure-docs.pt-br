---
title: PowerShell – Adicionar uma imagem do marketplace a um laboratório do Azure DevTest Labs
description: Este script do PowerShell adiciona uma imagem de marketplace a um laboratório no Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 543f20af270769dd16e4a1ecf6ee93e9259cdfd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022262"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Use o PowerShell adiciona uma imagem de marketplace a um laboratório no Azure DevTest Labs

Este script de exemplo do PowerShell adiciona uma imagem de marketplace a um laboratório no Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório**. O script exige que você tenha um laboratório existente. 

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

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
