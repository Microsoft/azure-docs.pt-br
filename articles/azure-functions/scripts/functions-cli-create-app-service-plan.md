---
title: Criar um Aplicativo de funções em um Plano do Serviço de Aplicativo – CLI do Azure
description: Exemplo de script da CLI do Azure - Criar um Aplicativo de funções em um Plano do Serviço de Aplicativo
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ea368714ded56d77a10e24b853144bdf1dd42dfe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786307"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Criar um Aplicativo de funções em um Plano do Serviço de Aplicativo

Este script de exemplo do Azure Functions cria um aplicativo de funções, que é um contêiner para suas funções. O aplicativo de funções criado usa um plano do Serviço de Aplicativo dedicado, o que significa que os recursos de seu servidor estão sempre ativados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Exemplo de script

Esse script cria um Aplicativo de funções do Azure usando um [plano do Serviço de Aplicativo](../dedicated-plan.md) dedicado.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Cada comando da tabela é vinculado à documentação específica do comando. Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de armazenamento do Azure. |
| [az functionapp plan create](/cli/azure/functionapp/plan#az_functionapp_plan_create) | Cria um plano Premium. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Implante um aplicativo de funções no plano do Serviço de Aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
