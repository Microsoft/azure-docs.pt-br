---
title: Gerenciar servidor-CLI do Azure-banco de dados do Azure para PostgreSQL
description: Saiba como gerenciar um banco de dados do Azure para o servidor PostgreSQL do CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 2ea07e2bc12e6fc0d62abd462b8537c6a93689f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935794"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Gerenciar um servidor único do banco de dados do Azure para PostgreSQL usando o CLI do Azure

Este artigo mostra como gerenciar seus servidores únicos implantados no Azure. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. Este artigo exige que você esteja executando a CLI do Azure versão 2.0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisará fazer logon em sua conta usando o comando [az login](/cli/azure/reference-index#az-login). Observe a propriedade **id**, que se refere à **ID da Assinatura** para sua conta do Azure.

```azurecli-interactive
az login
```

Selecione a assinatura específica em sua conta usando o comando [az account set](/cli/azure/account). Anote o valor de **id** da saída **az login** para usar como valor para o argumento **subscription** no comando. Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Para obter todas as suas assinaturas, use [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

Se você ainda não criou um servidor, consulte este guia de [início rápido](quickstart-create-server-database-azure-cli.md) para criar um.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Você pode escalar verticalmente seu tipo de preço, computação e armazenamento com facilidade usando o comando a seguir. Você pode ver toda a operação do servidor que pode executar [AZ postgres Server Overview](/cli/azure/mysql/server)

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Estes são os detalhes dos argumentos acima:

**Configuração** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Insira um nome exclusivo para o servidor do banco de dados do Azure para PostgreSQL. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter de 3 a 63 caracteres.
resource-group | myresourcegroup | Forneça o nome do grupo de recursos do Azure.
sku-name|GP_Gen5_2|Insira o nome do tipo de preço e a configuração de computação. Segue a convenção {tipo de preço} _{geração de computação}_ {vCores} em formato abreviado. Confira os [tipos de preço](./concepts-pricing-tiers.md) para obter mais informações.
storage-size | 6144 | A capacidade de armazenamento do servidor (a unidade é megabytes). Mínimo de 5120 e aumentos em incrementos de 1024.

> [!Important]
> - O armazenamento pode ser dimensionado verticalmente (no entanto, você não pode dimensionar o armazenamento verticalmente)
> - Não há suporte para escalar verticalmente do tipo de preço básico para uso geral ou com otimização de memória. Você pode escalar verticalmente com o  [uso de um script bash](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) ou [usando o PostgreSQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-postgresql-databases-on-a-server"></a>Gerencie bancos de dados PostgreSQL em um servidor.
Você pode usar qualquer um desses comandos para criar, excluir, listar e exibir as propriedades de banco de dados de um banco de dados em seu servidor

| Cmdlet | Uso| Descrição |
| --- | ---| --- |
|[criar o AZ postgres DB Create](/cli/azure/sql/db#az-mysql-db-create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Cria um banco de dados|
|[excluir o AZ postgres DB](/cli/azure/sql/db#az-mysql-db-delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Exclua seu banco de dados do servidor. Esse comando não exclui o servidor. |
|[lista AZ postgres DB](/cli/azure/sql/db#az-mysql-db-list)|```az postgres db list -g myresourcegroup -s mydemoserver```|lista todos os bancos de dados no servidor|
|[AZ postgres DB show](/cli/azure/sql/db#az-mysql-db-show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Mostra mais detalhes do banco de dados|

## <a name="update-admin-password"></a>Atualizar senha do administrador
Você pode alterar a senha da função de administrador com este comando
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Verifique se a senha tem no mínimo 8 caracteres e no máximo 128 caracteres.
> A senha deve conter caracteres de três das seguintes categorias: letras maiúsculas em inglês, letras minúsculas, números e caracteres não alfanuméricos.

## <a name="delete-a-server"></a>Excluir um servidor
Se você quiser apenas excluir o servidor único PostgreSQL, poderá executar o comando [AZ postgres Server Delete](/cli/azure/mysql/server#az-mysql-server-delete) .

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas
- [Reiniciar um servidor](howto-restart-server-cli.md)
- [Restaurar um servidor em um estado inadequado](howto-restore-server-cli.md)
- [Monitorar e ajustar o servidor](concepts-monitoring.md)
