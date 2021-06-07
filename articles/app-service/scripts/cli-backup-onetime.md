---
title: 'CLI: fazer backup de um aplicativo'
description: Saiba como usar a CLI do Azure para automatizar a implantação e o gerenciamento do seu aplicativo do Serviço de Aplicativo. Esta amostra descreve como fazer backup de um aplicativo.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: a5771882e85c69ae54212b2ea8eb1e9482f27821
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782631"
---
# <a name="back-up-an-app-using-cli"></a>Fazer backup de um aplicativo usando a CLI

Este script de exemplo cria um aplicativo no Serviço de Aplicativo com os respectivos recursos e então cria um backup único para ele. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, você precisará da CLI do Azure versão 2.0 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-onetime/backup-onetime.sh?highlight=3-7 "Back up an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az storage account create`](/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de armazenamento. |
| [`az storage container create`](/cli/azure/storage/container#az_storage_container_create) | Cria um contêiner de armazenamento do Azure. |
| [`az storage container generate-sas`](/cli/azure/storage/container#az_storage_container_generate_sas) | Gera um token SAS para um contêiner de armazenamento do Azure.  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um Plano do Serviço de Aplicativo. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Cria um aplicativo do Serviço de Aplicativo. |
| [`az webapp config backup create`](/cli/azure/webapp/config/backup#az_webapp_config_backup_create) | Cria um backup para um aplicativo do Serviço de Aplicativo. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az_webapp_config_backup_list) | Obtém uma lista de backups para um aplicativo do Serviço de Aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../samples-cli.md).
