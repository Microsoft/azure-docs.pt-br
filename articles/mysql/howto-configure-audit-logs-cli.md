---
title: Acessar logs de auditoria-CLI do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MySQL no CLI do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7ffabae785a589bacf349356079f6046039f9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541990"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurar e acessar os logs de auditoria no CLI do Azure

Você pode configurar os [logs de auditoria do banco de dados do Azure para MySQL](concepts-audit-logs.md) no CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para percorrer este guia de instruções:

- Você precisa de um [servidor de banco de dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

>[!IMPORTANT]
> É recomendável registrar apenas os tipos de eventos e os usuários necessários para fins de auditoria para garantir que o desempenho do servidor não seja muito afetado.

Habilite e configure o log de auditoria usando as seguintes etapas:

1. Ative os logs de auditoria definindo o parâmetro **audit_logs_enabled** como "ativado". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events** .
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione os usuários do MySQL a serem excluídos do log atualizando o parâmetro **audit_log_exclude_users** . Especifique os usuários fornecendo seu nome de usuário do MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione qualquer usuário específico do MySQL a ser incluído para registro em log atualizando o parâmetro **audit_log_include_users** . Especifique os usuários fornecendo seu nome de usuário do MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md) no banco de dados do Azure para MySQL
- Saiba como configurar logs de auditoria no [portal do Azure](howto-configure-audit-logs-portal.md)
