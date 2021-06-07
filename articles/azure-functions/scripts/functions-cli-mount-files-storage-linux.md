---
title: Montar um compartilhamento de arquivo para um aplicativo de funções Python – CLI do Azure
description: Crie um aplicativo de funções Python sem servidor e monte um compartilhamento de arquivo existente usando a CLI do Azure.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0037cea24b1989c4f7a4d2ddd6bf3f8f7e812b3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762273"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Montar um compartilhamento de arquivo para um aplicativo de funções Python usando a CLI do Azure

Este script de exemplo do Azure Functions cria um aplicativo de funções e um compartilhamento nos Arquivos do Azure. Ele monta o compartilhamento para que os dados possam ser acessados por suas funções.  

>[!NOTE]
>O aplicativo de funções criado é executado no Python versão 3.7. O Azure Functions também [dá suporte às versões 3.6 e 3.8 do Python](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Exemplo de script

Esse script cria um aplicativo de funções no Azure Functions usando o [Plano de consumo](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Cada comando da tabela é vinculado à documentação específica do comando. Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de armazenamento do Azure. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Cria um aplicativo de funções. |
| [az storage share create](/cli/azure/storage/share#az_storage_share_create) | Cria um compartilhamento de arquivos do Azure na conta de armazenamento. | 
| [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create) | Cria um diretório no compartilhamento. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) | Monta o compartilhamento para o aplicativo de funções. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_list) | Mostra os compartilhamentos de arquivos montados no aplicativo de funções. | 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
