---
title: Script da CLI – Dimensionar servidor – Banco de Dados do Azure para MariaDB
description: Este exemplo de script da CLI dimensiona um servidor do Banco de Dados do Azure para MariaDB para um nível de desempenho diferente após consultar a métrica.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 3a099f1a3ab16c33baa8037e727d26f107a593a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785533"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorar e dimensionar um servidor do Banco de Dados do Azure para MariaDB usando a CLI do Azure
Este exemplo de script da CLI dimensiona a computação e o armazenamento para um único servidor de Banco de Dados do Azure para MariaDB após consultar a métrica. A computação pode aumentar ou reduzir. O armazenamento só pode aumentar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Exemplo de script
Atualize o script com sua ID de assinatura.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Cria um servidor MariaDB que hospeda os bancos de dados. |
| [az mariadb server update](/cli/azure/mariadb/server#az_mariadb_server_update) | Atualiza as propriedades do servidor MariaDB. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Lista o valor de métrica para os recursos. |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [computação e armazenamento do Banco de Dados do Azure para MariaDB](../concepts-pricing-tiers.md)
- Experimente scripts adicionais: [Amostras da CLI do Azure para o Banco de Dados do Azure para MariaDB](../sample-scripts-azure-cli.md)
- Saiba mais sobre a [CLI do Azure](/cli/azure)
