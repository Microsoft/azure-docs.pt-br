---
title: Exemplos da CLI do Azure para a API do Gremlin do Azure Cosmos DB
description: Exemplos da CLI do Azure para a API do Gremlin do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ff557e5ca0769b68cb74f4b790e3678da7c3c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342106"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-gremlin-api"></a>Exemplos de CLI do Azure para a API do Gremlin do Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

A tabela a seguir inclui links para exemplos de scripts de CLI do Azure para o Azure Cosmos DB. Use os links à direita para navegar para exemplos específicos de API. Exemplos comuns são os mesmos em todas as APIs. As páginas de referência de todos os comandos do Azure Cosmos DB CLI estão disponíveis na [Referência de CLI do Azure](/cli/azure/cosmosdb). Exemplos de scripts da CLI do Azure Cosmos DB também podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Esses exemplos exigem a CLI do Azure versão 2.12.1 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Exemplos comuns

Esses exemplos se aplicam a todas as APIs do Azure Cosmos DB

|Tarefa | Descrição |
|---|---|
| [Adicionar ou fazer failover de regiões](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Adicione uma região, altere a prioridade do failover, dispare um failover manual.|
| [Chaves da conta e cadeias de conexão](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Liste chaves da conta, chaves somente leitura, regenerar chaves e listar cadeias de conexão.|
| [Proteger com o firewall do IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta do Cosmos com o firewall do IP configurado.|
| [Proteger a nova conta com pontos de extremidade de serviço](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta do Cosmos e proteja com pontos de extremidade de serviço.|
| [Proteger a conta existente com pontos de extremidade de serviço](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Atualize uma conta do Cosmos para ser protegida com pontos de extremidade de serviço quando a sub-rede estiver finalmente configurada.|
|||

## <a name="gremlin-api-samples"></a>Exemplos de API do Gremlin

|Tarefa | Descrição |
|---|---|
| [Criar uma conta, um banco de dados e um grafo do Azure Cosmos](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e um grafo do Azure Cosmos DB para a API do Gremlin. |
| [Criar uma conta, um banco de dados e um grafo do Azure Cosmos com dimensionamento automático](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e um grafo do Azure Cosmos DB com dimensionamento automático para a API do Gremlin. |
| [Operações de taxa de transferência](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Leia, atualize e migre entre o dimensionamento automático e a taxa de transferência padrão em um banco de dados e um grafo.|
| [Bloquear recursos da exclusão](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos.|
|||
