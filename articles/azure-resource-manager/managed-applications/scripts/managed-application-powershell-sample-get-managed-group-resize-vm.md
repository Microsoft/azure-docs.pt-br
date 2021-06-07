---
title: Obter um grupo de recursos gerenciados e redimensionar VMs – Azure PowerShell
description: Fornece um script de exemplo do Azure PowerShell que obtém um grupo de recursos gerenciados para um Aplicativo Gerenciado do Azure. O script redimensiona as VMs.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 602aaeb67ca081ebac71ca1d6d24a2de3c020603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86055980"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Obter recursos em um grupo de recursos gerenciado e redimensionar VMs com o PowerShell

Este script recupera os recursos de um grupo de recursos gerenciado e redimensiona as VMs nesse grupo de recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para implantar o aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzManagedApplication](/powershell/module/az.resources/get-azmanagedapplication) | Liste os aplicativos gerenciados. Dê um nome ao grupo de recursos para destacar os resultados. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Liste os recursos. Forneça um grupo de recursos e um tipo de recurso para destacar os resultados. |
| [Update-AzVM](/powershell/module/az.compute/update-azvm) | Atualize o tamanho de uma máquina virtual. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/get-started-azureps).
