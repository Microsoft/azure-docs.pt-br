---
title: Gerenciar réplicas de leitura no banco de dados do Azure para servidor flexível MySQL usando o CLI do Azure.
description: Saiba como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL servidor flexível usando o CLI do Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a7d63fd76a88430495c9f55200308f63b11c89d4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494300"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL servidor flexível usando o CLI do Azure

> [!IMPORTANT]
> Ler réplicas no banco de dados do Azure para MySQL-o servidor flexível está em versão prévia.

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para o servidor flexível do MySQL usando o CLI do Azure. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).

> [!Note]
> Não há suporte para a réplica no servidor habilitado para alta disponibilidade. 

## <a name="azure-cli"></a>CLI do Azure
Você pode criar e gerenciar réplicas de leitura usando o CLI do Azure.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli)
- Um [banco de dados do Azure para MySQL servidor flexível](quickstart-create-server-cli.md) que será usado como o servidor de origem.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando você cria uma réplica para uma fonte que não tem réplicas existentes, a origem será reiniciada primeiro para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração de servidor que a origem. A configuração do servidor de réplica pode ser alterada depois de criada. O servidor de réplica é sempre criado no mesmo grupo de recursos, no mesmo local e na mesma assinatura que o servidor de origem. Se você quiser criar um servidor de réplica para um grupo de recursos diferente ou uma assinatura diferente, poderá [mover o servidor de réplica](../../azure-resource-manager/management/move-resource-group-and-subscription.md) após a criação. É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores do que a origem para garantir que a réplica seja capaz de acompanhar a origem.


### <a name="list-replicas-for-a-source-server"></a>Listar réplicas para um servidor de origem

Para exibir todas as réplicas de um determinado servidor de origem, execute o seguinte comando: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação para um servidor é irreversível. Depois que a replicação for interrompida entre uma origem e uma réplica, ela não poderá ser desfeita. O servidor de réplica então se torna um servidor autônomo e agora suporta tanto leitura quanto gravação. Este servidor não pode ser transformado em uma réplica novamente.

A replicação para um servidor de réplica de leitura pode ser interrompida usando o seguinte comando:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A exclusão de um servidor de réplica de leitura pode ser feita executando o comando **[az mysql server delete](/cli/azure/mysql/server)**.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> A exclusão de um servidor de origem interrompe a replicação para todos os servidores de origem e exclui o próprio servidor mestre. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor de origem, você pode executar o comando **[AZ MySQL exflexíveis-Server Delete](/cli/azure/mysql/flexible-server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)