---
title: Gerenciar réplicas de leitura-CLI do Azure, API REST-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o CLI do Azure e a API REST.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 70da1e9c70bf80737065362c68781652dd9ab6e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664946"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o CLI do Azure e a API REST

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para o serviço MariaDB usando o CLI do Azure e a API REST.

## <a name="azure-cli"></a>CLI do Azure
Você pode criar e gerenciar réplicas de leitura usando o CLI do Azure.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli)
- Um [banco de dados do Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) que será usado como o servidor de origem. 

> [!IMPORTANT]
> O recurso ler réplica só está disponível para o banco de dados do Azure para servidores MariaDB nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor de origem está em um desses tipos de preço.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando você cria uma réplica para uma fonte que não tem réplicas existentes, a origem será reiniciada primeiro para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

O comando `az mariadb server replica create` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos para o qual o servidor de réplica será criado.  |
| name | mydemoreplicaserver | O nome do novo servidor de réplica criado. |
| source-server | mydemoserver | O nome ou a ID do servidor de origem existente do qual replicar. |

Para criar uma réplica de leitura entre regiões, use o `--location` parâmetro. 

O exemplo de CLI abaixo cria a réplica no oeste dos EUA.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o artigo [conceitos de réplica de leitura](concepts-read-replicas.md). 

> [!NOTE]
> Réplicas de leitura são criadas com a mesma configuração de servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de criada. É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores do que a origem para garantir que a réplica seja capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-source-server"></a>Listar réplicas para um servidor de origem

Para exibir todas as réplicas de um determinado servidor de origem, execute o seguinte comando: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

O comando `az mariadb server replica list` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos para o qual o servidor de réplica será criado.  |
| server-name | mydemoserver | O nome ou a ID do servidor de origem. |

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação para um servidor é irreversível. Depois que a replicação for interrompida entre uma origem e uma réplica, ela não poderá ser desfeita. O servidor de réplica então se torna um servidor autônomo e agora suporta tanto leitura quanto gravação. Este servidor não pode ser transformado em uma réplica novamente.

A replicação para um servidor de réplica de leitura pode ser interrompida usando o seguinte comando:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

O comando `az mariadb server replica stop` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos em que há o servidor de réplica.  |
| name | mydemoreplicaserver | O nome do servidor de réplica para interromper a replicação. |

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A exclusão de um servidor de réplica de leitura pode ser feita executando o comando **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> A exclusão de um servidor de origem interrompe a replicação para todos os servidores de origem e exclui o próprio servidor mestre. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor de origem, você pode executar o comando **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>API REST
Você pode criar e gerenciar réplicas de leitura usando a [API REST do Azure](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Você pode criar uma réplica de leitura usando a [API criar](/rest/api/mariadb/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o artigo [conceitos de réplica de leitura](concepts-read-replicas.md). 

Se você não tiver definido o `azure.replication_support` parâmetro como **réplica** em um uso geral ou servidor de origem com otimização de memória e reiniciado o servidor, você receberá um erro. Conclua essas duas etapas antes de criar uma réplica.

Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor de origem: geração de computação, vCores, armazenamento e período de retenção de backup. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.


> [!IMPORTANT]
> Antes que uma configuração do servidor de origem seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Essa ação ajuda a réplica a acompanhar as alterações feitas no mestre.

### <a name="list-replicas"></a>Listar réplicas
Você pode exibir a lista de réplicas de um servidor de origem usando a [API da lista de réplicas](/rest/api/mariadb/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Você pode interromper a replicação entre um servidor de origem e uma réplica de leitura usando a [API de atualização](/rest/api/mariadb/servers/update).

Depois de parar a replicação em um servidor de origem e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura se torna um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>Excluir um servidor de origem ou de réplica
Para excluir um servidor de origem ou de réplica, use a [API de exclusão](/rest/api/mariadb/servers/delete):

Quando você exclui um servidor de origem, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores independentes que agora têm suporte para leitura e gravação.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)