---
title: 'CLI do Azure: Monitorar e dimensionar um banco de dados individual no Banco de Dados SQL do Azure'
description: Usar um script de exemplo da CLI do Azure para monitorar e dimensionar um banco de dados individual no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: bd982ab6dc66674e705f080511282bcfeb909872
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787153"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Usar a CLI do Azure para monitorar e dimensionar um banco de dados individual no Banco de Dados SQL do Azure

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script da CLI do Azure dimensiona um banco de dados individual no Banco de Dados SQL do Azure para um tamanho da computação diferente depois de consultar as informações de tamanho do banco de dados.

Se você optar por instalar e usar a CLI do Azure localmente, este artigo exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Use [az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list) para obter uma lista das operações executadas no banco de dados, e use [az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel) para cancelar uma operação de atualização no banco de dados.

### <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exemplo de referência

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Script | Descrição |
|---|---|
| [az sql server](/cli/azure/sql/server) | Comandos de servidor. |
| [az sql db show-usage](/cli/azure/sql#az_sql_show_usage) | Mostra as informações de uso do tamanho de um banco de dados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos de script adicionais da CLI podem ser encontrados em [Scripts de exemplo da CLI do Azure](../az-cli-script-samples-content-guide.md).
