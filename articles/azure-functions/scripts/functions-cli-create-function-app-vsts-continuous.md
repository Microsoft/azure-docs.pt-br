---
title: Criar um aplicativo de funções com a implantação do DevOps – CLI do Azure
description: Criar um aplicativo de funções e implantar o código da função do Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f31c6a76412939d179cdd282e5e643ab7e8531b5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786206"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Criar uma função no Azure que é implantada usando o Azure DevOps

Este tópico mostra como usar o Azure Functions para criar um aplicativo de função [sem servidor](https://azure.microsoft.com/solutions/serverless/) usando o [Plano de consumo](../consumption-plan.md). O aplicativo de funções, que é um contêiner para suas funções, é continuamente implantado de um repositório do Azure DevOps. 

Para concluir este tópico, você deve ter:

* Um repositório do Azure DevOps que contém o projeto de aplicativo de função e ao qual você tem permissões administrativas.
* Um [PAT (token de acesso pessoal)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) para acessar o repositório do Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Exemplo de script

Este exemplo cria um aplicativo de funções do Azure e implanta o código da função do Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, conta de armazenamento, aplicativo de função e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Cria a conta de armazenamento necessária para o aplicativo de funções. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Cria um aplicativo de funções no [Plano de consumo](../consumption-plan.md) sem servidor. |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config) | Associa um aplicativo de funções a um repositório Git ou Mercurial. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
