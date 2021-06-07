---
title: Script da CLI do Azure – Restaurar um servidor do Banco de Dados do Azure para PostgreSQL
description: Esse script de exemplo de CLI do Azure mostra como restaurar um servidor de Banco de Dados do Azure para PostgreSQL e seus bancos de dados a um ponto anterior.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 6d95c06efa347618a7c571b65f01f147aeb5ef1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778402"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Restaurar um servidor do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
Esse script de exemplo do CLI restaura um servidor individual do Banco de Dados do Azure para PostgreSQL a um ponto anterior.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Exemplo de script
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios. Substitua a ID de assinatura usada nos comandos `az monitor` pela sua ID de assinatura.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgresql server create](/cli/azure/postgres/server#az_postgres_server_create) | Cria um servidor PostgreSQL que hospeda os bancos de dados. |
| [az postgresql server restore](/cli/azure/postgres/server#az_postgres_server_restore) | Restaura um servidor de backup. |
| [az group delete](/cli/azure/group) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o portal do Azure](../howto-restore-server-portal.md)
