---
title: Criar um aplicativo de funções com armazenamento conectado – CLI do Azure
description: Amostra de script da CLI do Azure – Criar uma função do Azure que se conecta a um armazenamento Azure
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 539c3a7dd95045b2e569dbb339be0e5a0c845902
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786271"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Criar um aplicativo de funções com uma conexão de conta de Armazenamento nomeada 

Este script de exemplo do Azure Functions cria um aplicativo de funções e conecta a função a uma conta de Armazenamento do Azure. A configuração do aplicativo criado que contém a conexão pode ser usada com um [gatilho ou associação de armazenamento](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Exemplo de script

Este exemplo cria um Aplicativo de funções do Azure e adiciona a cadeia de conexão de armazenamento para uma configuração de aplicativo.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crie um grupo de recursos com local. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Criar uma conta de armazenamento. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Cria um aplicativo de funções no [Plano de consumo](../consumption-plan.md) sem servidor. |
| [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string) | Obtém a cadeia de conexão para a conta. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Define a cadeia de conexão como uma configuração de aplicativo no aplicativo de funções. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
