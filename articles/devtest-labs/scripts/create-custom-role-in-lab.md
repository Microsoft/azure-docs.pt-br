---
title: PowerShell – Criar uma função personalizada em um laboratório do Azure DevTest Labs
description: Este artigo fornece um script do Azure PowerShell que criará uma função personalizada em um laboratório do Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3757d6c16a762a0f76f4718f117da93322af074c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021190"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Use o PowerShell para criar uma função personalizada em um laboratório no Azure DevTest Labs

Este script de amostra do PowerShell cria uma função personalizada para ser usada em um laboratório no Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Um laboratório**. O script exige que você tenha um laboratório existente. 

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Obtém as operações de um provedor de recursos do Azure que são protegidas usando o RBAC do Azure. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Lista todas as funções do Azure disponíveis para atribuição. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Cria uma Função personalizada. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).

Exemplos adicionais de script do PowerShell do Azure Lab Services podem ser encontrados em [Exemplos de PowerShell do Azure Lab Services](../samples-powershell.md).
