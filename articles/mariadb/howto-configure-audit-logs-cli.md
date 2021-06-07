---
title: Acessar logs de auditoria-CLI do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MariaDB no CLI do Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c1d446d8ee2863077ad84c361876758336f5a3cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665167"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>Configurar e acessar os logs de auditoria do banco de dados do Azure para Maria DB no CLI do Azure

Você pode configurar os [logs de auditoria do banco de dados do Azure para MariaDB](concepts-audit-logs.md) no CLI do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia:

- Você precisa de um [banco de dados do Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

>[!IMPORTANT]
> É recomendável registrar apenas os tipos de eventos e os usuários necessários para fins de auditoria para garantir que o desempenho do servidor não seja muito afetado.

Habilite e configure o log de auditoria usando as seguintes etapas: 

1. Ative os logs de auditoria definindo o parâmetro **audit_logs_enabled** como "ativado". 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events** .
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione os usuários do MariaDB a serem excluídos do log atualizando o parâmetro **audit_log_exclude_users** . Especifique os usuários fornecendo seu nome de usuário MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione qualquer usuário MariaDB específico a ser incluído para registro em log atualizando o parâmetro **audit_log_include_users** . Especifique os usuários fornecendo seu nome de usuário MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md) no banco de dados do Azure para MariaDB
- Saiba como configurar logs de auditoria no [portal do Azure](howto-configure-audit-logs-portal.md)
