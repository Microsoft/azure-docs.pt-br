---
title: 'Início rápido: Criar um pool de SQL do Synapse com a CLI do Azure'
description: Crie rapidamente um pool de SQL do Synapse com uma regra de firewall no nível do servidor usando a CLI do Azure.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.custom: azure-synapse, devx-track-azurecli
ms.openlocfilehash: 532c06cc49f9fddab09c6678ee5fff9fe32347cb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565926"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Início rápido: Criar um pool de SQL do Synapse com a CLI do Azure

Crie um pool de SQL do Synapse (data warehouse) no Azure Synapse Analytics usando a CLI do Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Introdução

Use estes comandos para fazer logon no Azure e configurar um grupo de recursos.

1. Se você estiver usando uma instalação local, execute o comando [az login](/cli/azure/reference-index#az_login) para entrar no Azure:

   ```azurecli
   az login
   ```

1. Se necessário, use o comando [az account set](/cli/azure/account#az_account_set) para selecionar sua assinatura:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Execute o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Crie um [servidor SQL lógico](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) usando o comando [az sql server create](/cli/azure/sql/server#az_sql_server_create):

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   Um servidor contém um grupo de bancos de dados gerenciados conjuntamente.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

Crie uma [regra de firewall no nível de servidor](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Uma regra de firewall no nível de servidor permite que um aplicativo externo, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte ao pool de SQL por meio do firewall do pool de SQL.

Execute o comando [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) para criar uma regra de firewall:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

Neste exemplo, o firewall está aberto somente para os outros recursos do Azure. Para habilitar a conectividade externa, altere o endereço IP para um endereço apropriado para seu ambiente. Para abrir todos os endereços IP, use 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

> [!NOTE]
> Os pontos de extremidade SQL comunicam-se pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isso acontecer, você não conseguirá se conectar ao servidor, a menos que o departamento de TI abra a porta 1433.
>

## <a name="create-and-manage-your-sql-pool"></a>Criar e gerenciar seu pool de SQL

Crie o pool de SQL. Este exemplo usa o DW100c como o objetivo de serviço, que é um ponto de partida de baixo custo para seu pool de SQL.

> [!NOTE]
> Você precisa de um workspace criado anteriormente. Para saber mais, confira [Início Rápido: Criar um workspace do Azure Synapse com a CLI do Azure](../quickstart-create-workspace-cli.md).

Use o comando [az synapse sql pool create](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) para criar o pool de SQL:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Para obter mais informações sobre as opções de parâmetro, confira [az synapse sql pool](/cli/azure/ext/synapse/synapse/sql/pool).

Você pode ver seus pools de SQL usando o comando [az synapse sql pool list](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list):

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Use o comando [az synapse sql pool update](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) para atualizar um pool existente:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Use o comando [az synapse sql pool pause](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) para pausar o pool:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Use o comando [az synapse sql pool resume](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) para iniciar um pool em pausa:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Para remover um pool de SQL existente, use o comando [az synapse sql pool delete](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete):

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais nesta coleção aproveitam este início rápido.

> [!TIP]
> Se você planeja continuar trabalhando com os tutoriais de início rápido mais recentes, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use o comando [az group delete](/cli/azure/group#az_group_delete) para excluir todos os recursos criados por este início rápido.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Você acabou de criar um pool de SQL, criar uma regra de firewall e se conectar ao pool de SQL. Para saber mais, prossiga para o artigo [Carregar dados para o pool de SQL](./load-data-from-azure-blob-storage-using-copy.md).
