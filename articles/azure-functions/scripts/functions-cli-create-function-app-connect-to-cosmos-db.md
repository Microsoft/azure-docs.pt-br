---
title: Criar um aplicativo de funções com Azure Cosmos DB – CLI do Azure
description: Exemplo de script da CLI do Azure – Criar uma Função do Azure que se conecta a um BD do Azure Cosmos
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 261ede1493b93ccd3cfed19125bbabc3be871698
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786276"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Criar um Azure Function que se conecta a um Banco de Dados Cosmo do Azure

Este script de exemplo do Azure Functions cria um aplicativo de funções e conecta a função a um banco de dados do Azure Cosmos DB. A configuração de aplicativo criado que contém a conexão pode ser usada com um [Gatilho ou associação do Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Exemplo de script

Este exemplo cria um Aplicativo de funções do Azure e adiciona uma chave de acesso e um ponto de extremidade de BD do Cosmos para configurações do aplicativo.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Criar um grupo de recursos com local |
| [az storage accounts create](/cli/azure/storage/account#az_storage_account_create) | Criar uma conta de armazenamento |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Cria um aplicativo de funções no [Plano de consumo](../consumption-plan.md) sem servidor. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Crie um banco de dados do Azure Cosmos DB. |
| [az cosmosdb show](/cli/azure/cosmosdb#az_cosmosdb_show)| Obtém a conexão da conta do banco de dados. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az_cosmosdb_list_keys)| Obtém as chaves para o banco de dados. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Define a cadeia de conexão como uma configuração de aplicativo no aplicativo de funções. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
