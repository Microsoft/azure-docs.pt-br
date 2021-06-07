---
title: Configurar parâmetros-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como configurar parâmetros postgres no banco de dados do Azure para PostgreSQL-servidor único usando o CLI do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: cadf21423ac7eb997db5bb42005ee307d9813331
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604185"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Personalizar parâmetros de configuração do servidor para o banco de dados do Azure para PostgreSQL-servidor único usando o CLI do Azure
Você pode listar, exibir e atualizar os parâmetros de configuração de um servidor PostgreSQL do Azure usando a CLI (Interface de Linha de Comando) do Azure. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Criar um banco de dados e um servidor de Banco de Dados do Azure para PostgreSQL seguindo [Criar um Banco de Dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instalar a interface de linha de comando da [CLI do Azure](/cli/azure/install-azure-cli) no computador ou usar o [Azure Cloud Shell](../cloud-shell/overview.md) no portal do Azure usando seu navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Listar os parâmetros de configuração de servidor para o bando de dados do Azure para servidor PostgreSQL
Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [az postgres server configuration list](/cli/azure/postgres/server/configuration).

É possível listar os parâmetros de configuração do servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar os detalhes sobre um parâmetro de configuração específico para um servidor, execute o comando [az postgres server configuration show](/cli/azure/postgres/server/configuration).

Este exemplo mostra detalhes do **registro\_min\_mensagens** parâmetros de configuração de servidor para servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor
Você pode modificar o valor de determinados parâmetros de configuração, que atualiza o valor da configuração subjacente para o mecanismo do servidor PostgreSQL. Para atualizar o valor de configuração, execute o comando [az postgres server configuration set](/cli/azure/postgres/server/configuration). 

Para atualizar o **registro\_min\_mensagens** parâmetros de configuração de servidor para servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Se você quiser redefinir o valor de um parâmetro de configuração, basta simplesmente optar por deixar o parâmetro opcional `--value` e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Esse comando redefine a configuração **log\_min\_messages** para o valor padrão **WARNING**. Para obter mais informações sobre a configuração do servidor e os valores permitidos, veja a documentação do PostgreSQL em [Configuração do Servidor](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Próximas etapas
- [Saiba como reiniciar um servidor](howto-restart-server-cli.md)
- Para configurar e acessar os logs de servidor, confira [Logs de servidor no Banco de Dados do Azure para PostgreSQL](concepts-server-logs.md)
