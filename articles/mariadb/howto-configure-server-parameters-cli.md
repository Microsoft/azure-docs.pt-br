---
title: Configurar os parâmetros de serviço no Banco de Dados do Azure para MariaDB
description: Este artigo descreve como configurar os parâmetros de serviço no Banco de Dados do Azure para MariaDB usando o utilitário de linha de comando da CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 4e0bf45f1c67a5e07d6ed632f6560d094b673c0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61040023"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalizar os parâmetros de configuração do servidor usando a CLI do Azure
Você pode listar, mostrar e atualizar os parâmetros de configuração de um Banco de Dados do Azure para MariaDB usando a CLI do Azure, o utilitário de linha de comando do Azure. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Um banco de dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Utilitário de linha de comando do [Azure CLI](/cli/azure/install-azure-cli) ou use o Azure Cloud Shell no navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Listar parâmetros de configuração do servidor para o Banco de Dados do Azure para MariaDB
Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list).

Você pode listar os parâmetros de configuração do servidor para o servidor **mydemoserver.mariadb.database.azure.com** sob o grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Para a definição de cada um dos parâmetros listados, consulte a seção de referência do MariaDB em [Server System Variables](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o comando [show da configuração do servidor az mariadb](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show).

Este exemplo mostra detalhes do parâmetro de configuração do servidor **slow\_query\_log** para o servidor **mydemoserver.mariadb.database.azure.com** sob o grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificar um valor do parâmetro de configuração do servidor
Você também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente para o mecanismo do servidor MariaDB. Para atualizar a configuração, use o comando [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set). 

Para atualizar o **slow\_query\_log** parâmetro de configuração do servidor do servidor **mydemoserver.mariadb.database.azure.com** sob o grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Se você quiser redefinir o valor de um parâmetro de configuração, omita o parâmetro opcional `--value` e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Esse código redefine a configuração **log de\_consultas\_lentas** para o valor padrão **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no seu servidor podem ser preenchidas chamando o procedimento armazenado `az_load_timezone` de uma ferramenta como a linha de comando do MariaDB ou o MariaDB Workbench.

> [!NOTE]
> Se você estiver executando o comando da `az_load_timezone`MariaDB Workbench, pode ser necessário desativar primeiro o modo de atualização segura usando`SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Para exibir os valores de fuso horário disponíveis, execute o comando a seguir:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Configurar o fuso horário de nível global

O fuso horário do nível global pode ser definido usando o comando [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set).

O comando a seguir atualiza o parâmetro de configuração do servidor **time\_zone** do servidor **mydemoserver.mariadb.database.azure.com** sob o grupo de recursos **myresourcegroup** para **EUA/Pacífico**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Configurar o fuso horário do nível de sessão

O fuso horário do nível de sessão pode ser configurado executando o comando `SET time_zone` a partir de uma ferramenta como a linha de comando do MariaDB ou o MariaDB Workbench. O exemplo abaixo configura o fuso horário para **EUA/Pacífico**.  

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MariaDB para [Funções de data e hora](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Próximas etapas

- Como configurar [parâmetros de servidor no Portal do Azure](howto-server-parameters.md)
