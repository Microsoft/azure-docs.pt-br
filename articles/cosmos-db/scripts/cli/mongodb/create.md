---
title: Criar um banco de dados e uma coleção para a API do MongoDB para o Azure Cosmos DB
description: Criar um banco de dados e uma coleção para a API do MongoDB para o Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 183ba81ffc6ecbe58a35758f09075abe1c514a29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566003"
---
# <a name="create-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>Criar um banco de dados e uma coleção para a API do MongoDB para Azure Cosmos DB usando a CLI do Azure
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.9.1 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/create.sh "Create an Azure Cosmos DB MongoDB API account, database, and collection.")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Cria uma conta do Banco de Dados Cosmos do Azure. |
| [az cosmosdb mongodb database create](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Cria um banco de dados da API do MongoDB do Azure Cosmos. |
| [az cosmosdb mongodb collection create](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Cria uma coleção de API do MongoDB do Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure Cosmos DB, confira [documentação da CLI do Azure Cosmos DB](/cli/azure/cosmosdb).

Todos os exemplos de scripts da CLI do Azure Cosmos DB podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
