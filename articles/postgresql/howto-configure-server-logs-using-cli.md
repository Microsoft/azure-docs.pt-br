---
title: Gerenciar logs-CLI do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como configurar e acessar os logs do servidor (arquivos. log) no banco de dados do Azure para PostgreSQL-servidor único usando o CLI do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 64582e7655ef2a3cf72547bfa8c3269f1624c890
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604168"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e acessar logs de servidor usando a CLI do Azure
Você pode listar e baixar logs de erro do servidor PostgreSQL do Azure usando a interface de linha de comando (CLI do Azure). No entanto, não há suporte para acesso aos logs de transação. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- O utilitário de linha de comando da [CLI do Azure](/cli/azure/install-azure-cli) ou o Azure Cloud Shell no navegador

## <a name="configure-logging"></a>Configurar o registro em log
Você pode configurar o servidor para acessar os logs de erro e os logs de consulta. Os logs de erros podem ter informações de ponto de verificação, conexão e vácuo automático.
1. Ative o registro em log.
2. Para ativar o registro em log de consulta, atualize **log\_statement** e **log\_min\_duration\_statement**.
3. Atualize o período de retenção.

Para mais informações, confira [Personalizando os parâmetros de configuração do servidor](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Listar logs
Para listar os arquivos de log disponíveis para o servidor, execute o comando [az postgres server-logs list](/cli/azure/postgres/server-logs).

Você pode listar os arquivos de log para o servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup**. Em seguida, direcione-os para um arquivo de texto chamado **log\_files\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Baixa logs localmente do servidor
Com o comando [az postgres server-logs download](/cli/azure/postgres/server-logs), você pode baixar arquivos de log individuais para o seu servidor. 

Use o exemplo a seguir para baixar o arquivo de log específico para o servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup** para seu ambiente local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre logs de servidor, confira [logs de servidor no banco de dados do Azure para PostgreSQL](concepts-server-logs.md).
- Para saber mais sobre os parâmetros de servidor, veja [Personalizar os parâmetros de configuração de servidor usando a CLI do Azure](howto-configure-server-parameters-using-cli.md).
