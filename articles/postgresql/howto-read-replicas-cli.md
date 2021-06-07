---
title: Gerenciar réplicas de leitura-CLI do Azure, API REST-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL-servidor único do CLI do Azure e da API REST
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7e74a58a14bdcc2a6fe1e9f86305aae415c6abf7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674507"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Criar e gerenciar réplicas de leitura do CLI do Azure, API REST

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL usando o CLI do Azure e a API REST. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Suporte à replicação do Azure
As [réplicas de leitura](concepts-read-replicas.md) e a [decodificação lógica](concepts-logical.md) dependem do postgres write ahead log (WAL) para obter informações. Esses dois recursos precisam de diferentes níveis de registro em log do Postgres. A decodificação lógica precisa de um nível mais alto de log do que as réplicas de leitura.

Para configurar o nível certo de registro em log, use o parâmetro de suporte de replicação do Azure. O suporte à replicação do Azure tem três opções de configuração:

* **Off** -coloca as informações mínimas no Wal. Essa configuração não está disponível na maioria dos servidores do banco de dados do Azure para PostgreSQL.  
* **Réplica** -mais detalhada do que **desativado**. Esse é o nível mínimo de log necessário para que as [réplicas de leitura](concepts-read-replicas.md) funcionem. Essa configuração é o padrão na maioria dos servidores.
* **Logical** -mais detalhado que a **réplica**. Este é o nível mínimo de registro em log para que a decodificação lógica funcione. As réplicas de leitura também funcionam nessa configuração.


> [!NOTE]
> Ao implantar réplicas de leitura para cargas de trabalho primárias com uso intensivo de demandas persistentes, o retardo de replicação pode continuar crescendo e talvez nunca seja capaz de se deslocar com o primário. Isso também pode aumentar o uso de armazenamento no primário, pois os arquivos WAL não são excluídos até que sejam recebidos na réplica.

## <a name="azure-cli"></a>CLI do Azure
Você pode criar e gerenciar réplicas de leitura usando o CLI do Azure.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli)
- Um [banco de dados do Azure para o servidor PostgreSQL](quickstart-create-server-up-azure-cli.md) ser o servidor primário.


### <a name="prepare-the-primary-server"></a>Preparar o servidor primário

1. Verifique o valor do servidor primário `azure.replication_support` . Ele deve ser pelo menos réplica para que réplicas de leitura funcionem.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Se `azure.replication_support` não for pelo menos a réplica, defina-a. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Reinicie o servidor para aplicar a alteração.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

O comando [AZ postgres Server Replica Create](/cli/azure/postgres/server/replica#az-postgres-server-replica-create) requer os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group | myresourcegroup |  O grupo de recursos em que o servidor de réplica será criado.  |
| name | mydemoserver-réplica | O nome do novo servidor de réplica criado. |
| source-server | mydemoserver | O nome ou a ID de recurso do servidor primário existente do qual replicar. Use a ID do recurso se desejar que os grupos de recursos da réplica e do mestre sejam diferentes. |

No exemplo de CLI abaixo, a réplica é criada na mesma região que o mestre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Para criar uma réplica de leitura entre regiões, use o `--location` parâmetro. O exemplo de CLI abaixo cria a réplica no oeste dos EUA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o artigo [conceitos de réplica de leitura](concepts-read-replicas.md). 

Se você não tiver definido o `azure.replication_support` parâmetro como **réplica** em um uso geral ou servidor primário com otimização de memória e reiniciado o servidor, receberá um erro. Conclua essas duas etapas antes de criar uma réplica.

> [!IMPORTANT]
> Examine a [seção considerações da visão geral da réplica de leitura](concepts-read-replicas.md#considerations).
>
> Antes que uma configuração do servidor primário seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Essa ação ajuda a réplica a acompanhar as alterações feitas no mestre.

### <a name="list-replicas"></a>Listar réplicas
Você pode exibir a lista de réplicas de um servidor primário usando o comando [AZ postgres Server da lista de réplicas](/cli/azure/postgres/server/replica#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Você pode interromper a replicação entre um servidor primário e uma réplica de leitura usando o comando [AZ postgres Server Replica Stop](/cli/azure/postgres/server/replica#az-postgres-server-replica-stop) .

Depois de parar a replicação em um servidor primário e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura se torna um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Excluir um servidor primário ou de réplica
Para excluir um servidor de réplica ou primário, use o comando [AZ postgres Server Delete](/cli/azure/postgres/server#az-postgres-server-delete) .

Quando você exclui um servidor primário, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores independentes que agora têm suporte para leitura e gravação.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
Você pode criar e gerenciar réplicas de leitura usando a [API REST do Azure](/rest/api/azure/).

### <a name="prepare-the-primary-server"></a>Preparar o servidor primário

1. Verifique o valor do servidor primário `azure.replication_support` . Ele deve ser pelo menos réplica para que réplicas de leitura funcionem.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Se `azure.replication_support` não for pelo menos a réplica, defina-a.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Reinicie o servidor](/rest/api/postgresql/servers/restart) para aplicar a alteração.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Você pode criar uma réplica de leitura usando a [API criar](/rest/api/postgresql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o artigo [conceitos de réplica de leitura](concepts-read-replicas.md). 

Se você não tiver definido o `azure.replication_support` parâmetro como **réplica** em um uso geral ou servidor primário com otimização de memória e reiniciado o servidor, receberá um erro. Conclua essas duas etapas antes de criar uma réplica.

Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor primário: geração de computação, vCores, armazenamento e período de retenção de backup. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.


> [!IMPORTANT]
> Antes que uma configuração do servidor primário seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Essa ação ajuda a réplica a acompanhar as alterações feitas no mestre.

### <a name="list-replicas"></a>Listar réplicas
Você pode exibir a lista de réplicas de um servidor primário usando a [API da lista de réplicas](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Você pode interromper a replicação entre um servidor primário e uma réplica de leitura usando a [API de atualização](/rest/api/postgresql/servers/update).

Depois de parar a replicação em um servidor primário e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura se torna um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Excluir um servidor primário ou de réplica
Para excluir um servidor de réplica ou primário, use a [API de exclusão](/rest/api/postgresql/servers/delete):

Quando você exclui um servidor primário, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores independentes que agora têm suporte para leitura e gravação.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [réplicas de leitura no Banco de Dados do Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).