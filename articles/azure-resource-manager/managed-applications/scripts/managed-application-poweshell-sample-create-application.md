---
title: 'Amostra de script do Azure PowerShell: Implantar um aplicativo gerenciado'
description: Fornece um script de exemplo do Azure PowerShell que implanta uma definição de aplicativo gerenciado na assinatura.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: a2687e9c943df8454ff42a17f44866dcdb7f4730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86055878"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Implantar um aplicativo gerenciado no catálogo de serviços com o PowerShell

Este script implanta uma definição de aplicativo gerenciado do catálogo de serviços.


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o comando a seguir para implantar o aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzManagedApplication](/powershell/module/az.resources/new-azmanagedapplication) | Crie um aplicativo gerenciado. Forneça a ID de definição e os parâmetros para o modelo. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/get-started-azureps).
