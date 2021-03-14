---
title: Criar um workspace do Log Analytics – Azure PowerShell
description: Script de exemplo do Azure PowerShell – criar um espaço de trabalho do Log Analytics
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: c04eb177eb8c52464be2ab7702a365d7a7b54e07
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040933"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Criar um espaço de trabalho do Log Analytics com o PowerShell

Esse script coloca rapidamente em funcionamento um espaço de trabalho do Azure Log Analytics, o que é necessário se você deseja começar a coletar, analisar e executar ações nos dados.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um novo espaço de trabalho do Log Analytics em sua assinatura. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Obtém informações sobre um workspace existente. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Cria um workspace no grupo de recursos e no local especificados. |


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

