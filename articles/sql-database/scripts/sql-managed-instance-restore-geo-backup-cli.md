---
title: Exemplo da CLI – Restaurar o backup geográfico – Banco de Dados SQL do Azure
description: Script de exemplo da CLI do Azure para restaurar um Banco de Dados de Instância Gerenciada do SQL do Azure de um backup com redundância geográfica.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 07/03/2019
ms.openlocfilehash: 5468cfc733b037f6287e8536c8c85994f0840161
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231245"
---
# <a name="use-cli-to-restore-a-managed-instance-database-to-another-geo-region"></a>Usar a CLI para restaurar um banco de dados de Instância Gerenciada para outra região geográfica

Este exemplo de script da CLI do Azure restaura um banco de dados de Instância Gerenciada do SQL do Azure de uma região geográfica remota (restauração geográfica).  

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

### <a name="prerequisites"></a>Pré-requisitos

Um par existente de instâncias gerenciadas; confira [Usar a CLI do Azure para criar uma Instância Gerenciada de SQL do Azure](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

```azurepowershell-interactive
#!/bin/bash
$instance = "<instanceId>" # add instance here
$targetInstance = "<targetInstanceId>" # add target instance here
$resource = "<resourceId>" # add resource here

$randomIdentifier = $(Get-Random)
$managedDatabase = "managedDatabase-$randomIdentifier"

echo "Creating $($managedDatabase) on $($instance)..."
az sql midb create -g $resource --mi $instance -n $managedDatabase

echo "Restoring $($managedDatabase) to $($targetInstance)..."
az sql midb restore -g $resource --mi $instance -n $managedDatabase --dest-name $targetInstance --time "2018-05-20T05:34:22"
```

## <a name="sample-reference"></a>Exemplo de referência

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Script | Descrição |
|---|---|
| [az sql midb](/cli/azure/sql/midb) | Comandos de banco de dados de instância gerenciada. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
