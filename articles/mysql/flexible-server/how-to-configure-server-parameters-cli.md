---
title: Configurar parâmetros do servidor-CLI do Azure-banco de dados do Azure para MySQL servidor flexível
description: Este artigo descreve como configurar os parâmetros de serviço no servidor flexível do banco de dados do Azure para MySQL usando o utilitário de linha de comando CLI do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 21f9b3dcb94be105c8b7279e6ac447540da65447
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110093"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Configurar parâmetros de servidor no banco de dados do Azure para o servidor flexível do MySQL usando o CLI do Azure

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Você pode listar, mostrar e atualizar parâmetros para um servidor flexível do banco de dados do Azure para MySQL usando CLI do Azure, o utilitário de linha de comando do Azure. Os parâmetros de servidor são configurados com o valor padrão e recomendado quando você cria o servidor.  

Este artigo descreve como listar, mostrar e atualizar parâmetros de servidor usando o CLI do Azure.

>[!Note]
> Os parâmetros do servidor podem ser atualizados globalmente no nível do servidor, usar o [CLI do Azure](./how-to-configure-server-parameters-cli.md) ou [portal do Azure](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Um servidor flexível do banco de dados do Azure para MySQL](quickstart-create-server-cli.md)
- Utilitário de linha de comando do [Azure CLI](/cli/azure/install-azure-cli) ou use o Azure Cloud Shell no navegador.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Listar parâmetros de servidor para o banco de dados do Azure para MySQL servidor flexível
Para listar todos os parâmetros em um servidor e seus valores, execute o comando [AZ MySQL Flexible-Server Parameter List](/cli/azure/mysql/flexible-server/parameter) .

Você pode listar os parâmetros de servidor para o servidor **mydemoserver.mysql.Database.Azure.com** em grupo de recursos **MyResource** Group.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Para obter a definição de cada um dos parâmetros listados, consulte a seção de referência do MySQL em [Variáveis do Sistema do Servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-parameter-details"></a>Mostrar detalhes do parâmetro de servidor
Para mostrar detalhes sobre um parâmetro específico para um servidor, execute o comando [AZ MySQL flexível-Server Parameter show](/cli/azure/mysql/flexible-server/parameter) .

Este exemplo mostra detalhes do parâmetro de servidor de **\_ \_ log de consulta lenta** para o servidor **mydemoserver.mysql.Database.Azure.com** em grupo de recursos **MyResource Group.**
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Modificar um valor de parâmetro de servidor
Você também pode modificar o valor de um determinado parâmetro de servidor, que atualiza o valor de configuração subjacente para o mecanismo do servidor MySQL. Para atualizar o parâmetro de servidor, use o comando [AZ MySQL flexível-Server Parameter Set](/cli/azure/mysql/flexible-server/parameter) . 

Para atualizar o parâmetro de servidor de **\_ \_ log de consulta lenta** do servidor **mydemoserver.mysql.Database.Azure.com** em grupo de recursos **MyResource Group.**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Se você quiser redefinir o valor de um parâmetro, omita o parâmetro opcional `--value` e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Esse código redefine o **\_ \_ log de consulta lenta** para o valor padrão **off**. 

## <a name="setting-non-modifiable-server-parameters"></a>Definindo parâmetros de servidor não modificáveis

Se o parâmetro de servidor que você deseja atualizar não for modificável, você poderá, opcionalmente, definir o parâmetro no nível de conexão usando `init_connect` . Isso define os parâmetros de servidor para cada cliente que se conecta ao servidor. 

Atualize o parâmetro do servidor **init \_ Connect** do servidor **mydemoserver.mysql.Database.Azure.com** em grupo de recursos **MyResource** Group para definir valores como conjunto de caracteres.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect` pode ser usado para mudar parâmetros que não exigem privilégio(s) SUPER no nível da sessão. Para verificar se você pode definir o parâmetro usando `init_connect`, execute o comando `set session parameter_name=YOUR_DESIRED_VALUE;` e, se ele apresentar o erro **Acesso negado, você precisa de privilégios SUPER**, não será possível definir o parâmetro usando "init_connect".

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser preenchidas, chamando o procedimento armazenado `mysql.az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL.

> [!NOTE]
> Se estiver executando o comando `mysql.az_load_timezone` do Workbench do MySQL, talvez seja necessário desativar primeiro o modo de atualização segura usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Você deve reiniciar o servidor para garantir que as tabelas de fuso horário sejam populadas corretamente.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Para exibir os valores de fuso horário disponíveis, execute o comando a seguir:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Configurar o fuso horário de nível global

O fuso horário de nível global pode ser definido usando o comando [AZ MySQL flexível-Server Parameter Set](/cli/azure/mysql/flexible-server/parameter) .

O comando a seguir atualiza o parâmetro de servidor de **\_ fuso horário** do servidor **mydemoserver.mysql.Database.Azure.com** em grupo de recursos **MyResource** Group para **US/Pacífico**.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Configurar o fuso horário do nível de sessão

O fuso horário do nível de sessão pode ser configurado, executando o comando `SET time_zone` a partir de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL. O exemplo abaixo configura o fuso horário para **EUA/Pacífico**.  

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MySQL para [Funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Próximas etapas

- Como configurar [parâmetros de servidor no Portal do Azure](./how-to-configure-server-parameters-portal.md)
