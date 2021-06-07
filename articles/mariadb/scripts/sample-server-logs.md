---
title: Script da CLI – Baixar logs de consulta lenta – Banco de Dados do Azure para MariaDB
description: Esse exemplo de script de CLI do Azure mostra como habilitar e baixar os logs de consulta lenta de um servidor do Banco de Dados do Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 42b1806d3aa1235dfd976dd5700204aa0b9e4700
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785542"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Habilitar e fazer o download dos logs de consulta lenta de um servidor do Banco de Dados do Azure para MariaDB usando a CLI do Azure
Esse exemplo de script de CLI mostra como habilitar e fazer o download dos logs de consulta lenta de um único servidor do Banco de Dados do Azure para MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Exemplo de script
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios. Substitua &lt;log_file_name&gt; nos comandos `az monitor` pelo seu próprio nome de arquivo de log do servidor.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Cria um servidor MariaDB que hospeda os bancos de dados. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list) | Liste os valores de configuração para um servidor. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) | Atualize a configuração de um servidor. |
| [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list) | Lista arquivos de log para um servidor. |
| [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download) | Faça o download de arquivos de log. |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Amostras da CLI do Azure para o Banco de Dados do Azure para MariaDB](../sample-scripts-azure-cli.md)
