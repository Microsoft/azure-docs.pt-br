---
title: Acessar logs de consulta lentos-CLI do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como acessar os logs lentos no banco de dados do Azure para MariaDB usando o utilitário de linha de comando CLI do Azure.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9c8f69f00ed4314fbe8b3fd1958b52c82ce55d99
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662382"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Configurar e acessar logs de consultas lentas do banco de dados do Azure para Maria DB usando CLI do Azure

Você pode baixar o banco de dados do Azure para MariaDB logs de consulta lento usando CLI do Azure, o utilitário de linha de comando do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Banco de dados do Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- A [CLI do Azure](/cli/azure/install-azure-cli) ou o Azure Cloud Shell no navegador

## <a name="configure-logging"></a>Configurar o registro em log
É possível configurar o servidor para acessar os logs de consulta lenta do MySQL executando as seguintes etapas:
1. Ative o log de consultas lentas definindo o parâmetro de **\_ \_ log de consulta lenta** como ativado.
2. Selecione para onde os logs são gerados usando **a \_ saída do log**. Para enviar logs para o armazenamento local e Azure Monitor logs de diagnóstico, selecione **arquivo**. Para enviar logs somente para Azure Monitor logs, selecione **nenhum**
3. Ajuste outros parâmetros como **long\_query\_time** e **log\_slow\_admin\_statements**.

Para saber como definir o valor desses parâmetros por meio da CLI do Azure, consulte [Como configurar parâmetros do servidor](howto-configure-server-parameters-cli.md).

Por exemplo, o comando da CLI a seguir ativa o log de consultas lentas, define o tempo de consulta longo para 10 segundos e desativa o registro em log da instrução de admin lenta. Por fim, ele lista as opções de configuração para sua análise.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Listar logs para o servidor do Banco de Dados do Azure para MariaDB
Se **log_output** estiver configurado como "arquivo", você poderá acessar os logs diretamente do armazenamento local do servidor. Para listar os arquivos de log de consulta lenta disponíveis para seu servidor, execute o comando [AZ MariaDB Server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

Você pode listar os arquivos de log para o servidor **mydemoserver.mariadb.database.azure.com** no grupo de recursos **myresourcegroup**. Em seguida, direcione-os para um arquivo de texto chamado **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Baixar logs do servidor
Se **log_output** estiver configurado como "arquivo", você poderá baixar arquivos de log individuais do seu servidor com o comando [AZ MariaDB Server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) .

Use o exemplo a seguir para baixar o arquivo de log específico para o servidor **mydemoserver.mariadb.database.azure.com** no grupo de recursos **myresourcegroup** para o ambiente local.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [logs de consulta lentos no banco de dados do Azure para MariaDB](concepts-server-logs.md).
