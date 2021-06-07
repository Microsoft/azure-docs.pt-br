---
title: 'CLI do Azure: Mover um banco de dados entre pools elásticos'
description: Use um script de exemplo da CLI do Azure para criar dois pools elásticos e mover um banco de dados no Banco de Dados SQL de um pool elástico para outro.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 9fcb23750137c8286efbc0e1fb7446e324c572dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747337"
---
# <a name="use-the-azure-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>Usar a CLI do Azure para mover um banco de dados no Banco de Dados SQL em um pool elástico do SQL

Este exemplo de script da CLI do Azure cria dois pools elásticos, move um banco de dados em pool no Banco de Dados SQL de um pool elástico SQL para outro e, em seguida, remove o banco de dados do pool do pool elástico SQL para que ele se torne um banco de dados individual no Banco de Dados SQL.

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exemplo de referência

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Descrição |
|---|---|
| [az sql server](/cli/azure/sql/server) | Comandos de servidor. |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | Comandos de pool elástico. |
| [az sql db](/cli/azure/sql/db) | Comandos de banco de dados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../az-cli-script-samples-content-guide.md).
