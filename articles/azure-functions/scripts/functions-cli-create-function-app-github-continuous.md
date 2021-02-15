---
title: Criar um aplicativo de funções com a implantação do GitHub – CLI do Azure
description: Criar um aplicativo de funções e implantar o código de função de um repositório GitHub usando o Azure Functions.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 219e993ad7132c90de6db680facc9b8f815947cc
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934374"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Criar um aplicativo de funções no Azure que é implantado no GitHub

Este script de exemplo do Azure Functions cria um aplicativo de funções usando o [Plano de consumo](../consumption-plan.md), junto com seus recursos relacionados. O script também configura o código de função para implantação contínua de um repositório do GitHub. 

Neste exemplo, você precisa de:

* Um repositório do GitHub com um código de funções, para o qual você tem permissões administrativas.
* Um [PAT (Token de Acesso Pessoal)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) para sua conta do GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Exemplo de script

Este exemplo cria um Aplicativo de funções do Azure e implanta o código da função do GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Cada comando da tabela é vinculado à documentação específica do comando. Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria a conta de armazenamento necessária para o aplicativo de funções. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções sem servidor [Plano de consumo](../consumption-plan.md) e a associa a um repositório Git ou Mercurial. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
