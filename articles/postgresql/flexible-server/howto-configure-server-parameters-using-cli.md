---
title: Configurar parâmetros-banco de dados do Azure para PostgreSQL-servidor flexível
description: Este artigo descreve como configurar parâmetros do postgres no banco de dados do Azure para PostgreSQL – servidor flexível usando o CLI do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 315e4439ca66a3fbadac228c013797f516bc2940
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605757"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Personalizar parâmetros de servidor para o banco de dados do Azure para PostgreSQL-servidor flexível usando o CLI do Azure

Você pode listar, exibir e atualizar os parâmetros de configuração de um servidor PostgreSQL do Azure usando a CLI (Interface de Linha de Comando) do Azure. Um subconjunto de parâmetros de mecanismo é exposto em nível de servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:
- Criar um banco de dados e um servidor de Banco de Dados do Azure para PostgreSQL seguindo [Criar um Banco de Dados do Azure para o servidor PostgreSQL](quickstart-create-server-cli.md)
- Instalar a interface de linha de comando da [CLI do Azure](/cli/azure/install-azure-cli) no computador ou usar o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal do Azure usando seu navegador.

## <a name="list-server-parameters-for-a-flexible-server"></a>Listar parâmetros de servidor para um servidor flexível

Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [AZ postgres Flexible-Server Parameter List](/cli/azure/postgres/flexible-server/parameter) .

Você pode listar os parâmetros de servidor para o servidor **mydemoserver.Postgres.Database.Azure.com** em grupo de recursos **MyResource** Group.

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>Mostrar detalhes do parâmetro de servidor

Para mostrar detalhes sobre um parâmetro específico para um servidor, execute o comando [AZ postgres flexível-Server Parameter show](/cli/azure/postgres/flexible-server/parameter)  .

Este exemplo mostra detalhes do parâmetro de servidor **log \_ min \_ messages** para o servidor **mydemoserver.Postgres.Database.Azure.com** em grupo de recursos **MyResource Group.**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>Modificar valor de parâmetro de servidor

Você também pode modificar o valor de um determinado parâmetro de servidor, que atualiza o valor de configuração subjacente para o mecanismo do servidor PostgreSQL. Para atualizar o parâmetro, use o comando [AZ postgres flexível-Server Parameter Set](/cli/azure/postgres/flexible-server/parameter) . 

Para atualizar o parâmetro de servidor **log \_ min \_ messages** do servidor **mydemoserver.Postgres.Database.Azure.com** em grupo de recursos **MyResource Group.**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

Se você quiser redefinir o valor de um parâmetro, basta escolher deixar o `--value` parâmetro opcional e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

Esse comando redefine o parâmetro **log \_ min \_ messages** para o valor padrão **Warning**. Para obter mais informações sobre parâmetros de servidor e valores permitidos, consulte a documentação do PostgreSQL na [configuração de parâmetros](https://www.postgresql.org/docs/12/config-setting.html).

## <a name="next-steps"></a>Próximas etapas

- Para configurar e acessar os logs de servidor, confira [Logs de servidor no Banco de Dados do Azure para PostgreSQL](concepts-logging.md)
