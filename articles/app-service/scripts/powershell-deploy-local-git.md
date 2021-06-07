---
title: 'PowerShell: Implantar do repositório Git local'
description: Saiba como usar o Azure PowerShell para automatizar a implantação e o gerenciamento do Serviço de Aplicativo. Esta amostra descreve como implantar um código por meio de um repositório Git local.
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 03d2f2d3928393f8a251640a03d82b5963140141
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89070285"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Criar um aplicativo Web e implantar o código de um repositório local Git

Este script de exemplo cria um aplicativo Web no Serviço de Aplicativo com seus recursos relacionados e, em seguida, implanta seu código de aplicativo da Web em um repositório Git local.

Se for necessário, atualize para o Azure PowerShell mais recente usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure. Além disso, o código do aplicativo precisa ser confirmado em um repositório Git local.

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Após a execução da amostra de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria um aplicativo Web com o grupo de recursos e o grupo do Serviço de Aplicativo necessários. Quando o diretório atual contiver um repositório Git, adicione também um `azure` remoto. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../samples-powershell.md).
