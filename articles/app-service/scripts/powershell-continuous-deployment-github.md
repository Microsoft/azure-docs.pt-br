---
title: 'PowerShell: implantar continuamente do GitHub'
description: Saiba como usar o Azure PowerShell para automatizar a implantação e o gerenciamento do Serviço de Aplicativo. Esta amostra descreve como criar um aplicativo com a CI/CD no GitHub.
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0ccb108b55844f822a42a3f1bccbb182a5658289
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89073855"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Criar um aplicativo web com a implantação contínua do GitHub

Este script de exemplo cria um aplicativo Web no Serviço de Aplicativo com os recursos relacionados e, em seguida, configura a [implantação contínua](../deploy-continuous-deployment.md) em um repositório GitHub. Para implantação do GitHub sem a implantação contínua, veja [Criar um aplicativo Web e implantar o código do GitHub](powershell-deploy-github.md).

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure. Além disso, verifique se:

- O código do aplicativo está em um repositório GitHub público ou privado que você possui. Para obter builds automáticos, estruture o repositório de acordo com a tabela [Preparar o repositório](../deploy-continuous-deployment.md#prepare-your-repository).
- Você [criou um token de acesso pessoal em sua conta do GitHub](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Após a execução da amostra de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria um aplicativo web. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica um recurso em um grupo de recursos. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../samples-powershell.md).
