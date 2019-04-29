---
title: Criar e gerenciar réplicas de leitura no Banco de Dados do Azure para MySQL
description: Este artigo descreve como configurar e gerenciar réplicas de leitura no Banco de Dados do Azure para MySQL usando a CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: e291cb46b5f8cb8722348bd8fcd6031ed29beb9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61423439"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Como criar e gerenciar réplicas de leitura no Banco de Dados do Azure para MySQL usando a CLI do Azure

Neste artigo, você aprenderá como criar e gerenciar réplicas de leitura na mesma região do Azure que o mestre no serviço Banco de Dados do Azure para MySQL usando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Um [Banco de Dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será usado como servidor mestre. 

> [!IMPORTANT]
> O recurso de réplica de leitura está disponível apenas para bancos de dados do Azure para servidores MySQL nas camadas de preços de uso geral ou de otimização de memória. Assegure-se de que o servidor principal esteja em um desses níveis de preços.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

O comando `az mysql server replica create` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | DESCRIÇÃO  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos para o qual o servidor de réplica será criado.  |
| Nome | mydemoreplicaserver | O nome do novo servidor de réplica criado. |
| source-server | mydemoserver | O nome ou a ID do servidor mestre existente para replicar. |

> [!NOTE]
> Réplicas de leitura são criadas com a mesma configuração de servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de criada. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou maiores que o mestre para garantir que a réplica seja capaz de acompanhar o mestre.

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação para um servidor é irreversível. Depois que a replicação tiver parado entre um mestre e uma réplica, ela não poderá ser desfeita. O servidor de réplica então se torna um servidor autônomo e agora suporta tanto leitura quanto gravação. Este servidor não pode ser transformado em uma réplica novamente.

A replicação para um servidor de réplica de leitura pode ser interrompida usando o seguinte comando:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

O comando `az mysql server replica stop` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | DESCRIÇÃO  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos em que há o servidor de réplica.  |
| Nome | mydemoreplicaserver | O nome do servidor de réplica para interromper a replicação. |

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A exclusão de um servidor de réplica de leitura pode ser feita executando o comando **[az mysql server delete](/cli/azure/mysql/server)**.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Excluir um servidor mestre

> [!IMPORTANT]
> A exclusão de um servidor mestre interrompe a replicação para todos os servidores de réplica e exclui o próprio servidor mestre. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor mestre, você poderá executar o comando **[az mysql server delete](/cli/azure/mysql/server)**.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Listar réplicas para um servidor mestre

Para exibir todas as réplicas de um determinado servidor mestre, execute o seguinte comando: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

O comando `az mysql server replica list` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | DESCRIÇÃO  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos para o qual o servidor de réplica será criado.  |
| server-name | mydemoserver | O nome ou a ID do servidor mestre. |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)
