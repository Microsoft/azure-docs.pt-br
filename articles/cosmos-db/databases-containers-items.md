---
title: Trabalhando com bancos de dados, contêineres e itens do Azure Cosmos DB
description: Este artigo descreve como criar e usar bancos de dados, contêineres e itens do Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 8eaca83b7ea89737a63fe56a18505c8df7e93fdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889366"
---
# <a name="work-with-databases-containers-and-items"></a>Como trabalhar com bancos de dados, contêineres e itens

Depois de criar uma [conta do Azure Cosmos](account-overview.md) em sua assinatura do Azure, você pode gerenciar dados em sua conta com a criação de bancos de dados, contêineres e itens. Este artigo descreve cada uma dessas entidades: bancos de dados, contêineres e itens. A imagem a seguir mostra a hierarquia de entidades diferentes em uma conta do Azure Cosmos:

![Entidades de conta do Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bancos de dados do Azure Cosmos

Você pode criar um ou mais bancos de dados do Azure Cosmos em sua conta. Um banco de dados é análogo a um namespace. É a unidade de gerenciamento para um conjunto de contêineres do Cosmos do Azure. A tabela a seguir mostra como um banco de dados do Azure Cosmos é mapeado para várias entidades específicas a uma API:

| **Entidade do Azure Cosmos** | **API do SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- |
|Banco de dados do Azure Cosmos | Banco de dados | Keyspace | Banco de dados | Banco de dados | ND |

> [!NOTE]
> Com contas da API de tabela, ao criar sua primeira tabela, um banco de dados padrão é criado automaticamente em sua conta do Cosmos do Azure.

### <a name="operations-on-an-azure-cosmos-database"></a>Operações em um banco de dados do Azure Cosmos

Você pode interagir com um banco de dados Cosmos do Azure com APIs do Azure Cosmos da seguinte maneira:

| **Operação** | **CLI do Azure**|**API do SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerar todos os bancos de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | ND | ND |
|Ler banco de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | ND | ND |
|Criar novo banco de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | ND | ND |
|Atualizar banco de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | ND | ND |


## <a name="azure-cosmos-containers"></a>Contêineres do Azure Cosmos

Um contêiner do Cosmos do Azure é a unidade de escalabilidade para armazenamento e taxa de transferência provisionada. Um contêiner é particionado horizontalmente e, em seguida, é replicado em várias regiões. Os itens que você adiciona ao contêiner e a taxa de transferência que provisiona nesse contêiner são ambos automaticamente distribuídos em um conjunto de partições lógicas baseado na chave de partição. Para saber mais sobre particionamento e chaves de partição, consulte [isso](partition-data.md) artigo. 

Ao criar um contêiner do Azure Cosmos, você configura a taxa de transferência em um dos seguintes modos:

* **Modo da taxa de transferência do disco**: A taxa de transferência provisionada em um contêiner é exclusivamente reservada para o contêiner e ele é feito por SLAs. Para saber mais, confira [como provisionar a taxa de transferência em um contêiner do Azure Cosmos](how-to-provision-container-throughput.md).

* Modo da **taxa de transferência compartilhada**: Esses contêineres compartilham a taxa de transferência provisionada com outros contêineres no mesmo banco de dados (exceto por esses contêineres que foram configuradas com taxa de transferência provisionada dedicada). Em outras palavras, a taxa de transferência provisionada no banco de dados é compartilhada entre todos os contêineres de "taxa de transferência compartilhada". Para saber mais, confira [como configurar a taxa de transferência provisionada em um banco de dados do Azure Cosmos](how-to-provision-database-throughput.md).

Um contêiner do Azure Cosmos pode dimensionar elasticamente, quer você crie contêineres com o modo de taxa de transferência provisionada "compartilhada" ou "dedicada".

Um contêiner do Azure Cosmos é um contêiner de itens independente de esquema. Itens dentro de um contêiner podem ter esquemas arbitrários. Por exemplo, um item que representa uma pessoa, um item que representa um automóvel pode ser colocado na *mesmo contêiner*. Por padrão, todos os itens que você adiciona a um contêiner são indexados automaticamente, sem a necessidade de nenhum índice explícito ou de gerenciamento de esquema. Você pode personalizar o comportamento de indexação Configurando a [política de indexação](index-overview.md) em um contêiner. 

Você pode definir [tempo de vida (TTL)](time-to-live.md) nos itens selecionados dentro de um contêiner do Cosmos do Azure ou para todo o contêiner normalmente limpar esses itens para fora do sistema. O Azure Cosmos DB excluirá automaticamente os itens quando eles expirarem. Ela também garante que uma consulta executada no contêiner não retorne os itens expirados dentro de um limite fixo. Para saber mais, confira [Como configurar o TTL no contêiner](how-to-time-to-live.md).

Usando [Feed de alteração](change-feed.md), você pode se inscrever para o log de operações que é gerenciado para cada uma das partições lógicas de seu contêiner. O feed de alterações fornece o log de todas as atualizações realizadas no contêiner, juntamente com as imagens de antes e depois dos itens. Ver [como criar aplicativos reativos usando o Feed de alterações](serverless-computing-database.md). Você também pode configurar a duração de retenção para o Feed de alterações usando a política no contêiner de feed de alterações. 

Você pode registrar [procedimentos armazenados, disparadores, funções definidas pelo usuário (UDFs)](stored-procedures-triggers-udfs.md) e [procedimentos de mesclagem](how-to-manage-conflicts.md) com seu contêiner do Azure Cosmos. 

Você pode especificar uma [restrição de chave exclusiva](unique-keys.md) em seu contêiner do Azure Cosmos. Ao criar uma política de chave exclusiva, você garante a exclusividade de um ou mais valores por chave de partição lógica. Depois de um contêiner ter sido criado com uma política de chave exclusiva, será impedida a criação de itens novos ou atualizados com valores que duplicarem os valores especificados pela restrição de chave exclusiva. Para obter mais informações, confira [Restrições de chave exclusivas](unique-keys.md).

Um contêiner do Azure Cosmos é especializado em entidades específicas a uma API da seguinte maneira:

| **Entidade do Azure Cosmos** | **API do SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- |
|Contêiner do Azure Cosmos | Coleção | Tabela | Coleção | Grafo | Tabela |

### <a name="properties-of-an-azure-cosmos-container"></a>Propriedades de um contêiner do Azure Cosmos

Um contêiner do Azure Cosmos tem um conjunto de propriedades definidas pelo sistema. Dependendo da escolha da API, alguns deles podem não ser diretamente expostos. A tabela a seguir descreve a lista de propriedades definidas pelo sistema:

| **Propriedade definida pelo sistema** | **Sistema gerado ou configurável pelo usuário** | **Finalidade** | **API do SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | Gerada pelo sistema | Identificador exclusivo do contêiner | Sim | Não | Não | Não | Não  |
|_etag | Gerada pelo sistema | Marca da entidade usada para controle de simultaneidade otimista | Sim | Não | Não | Não | Não  |
|_ts | Gerada pelo sistema | Último carimbo de data/hora atualizado do contêiner | Sim | Não | Não | Não | Não  |
|_self | Gerada pelo sistema | URI endereçável do contêiner | Sim | Não | Não | Não | Não  |
|ID | Configurável pelo usuário | Nome exclusivo do contêiner definido pelo usuário | Sim | sim | sim | sim | Sim |
|indexingPolicy | Configurável pelo usuário | Fornece a capacidade de alterar o caminho de índice, o tipo de índice e o modo de índice. | Sim | Não | Não | Não  | Sim |
|TimeToLive | Configurável pelo usuário | Fornece a capacidade de excluir itens automaticamente de um contêiner após um período de tempo determinado. Para obter mais detalhes, confira o artigo [Vida útil](time-to-live.md). | Sim | Não | Não | Não  | Sim |
|changeFeedPolicy | Configurável pelo usuário | Usado para ler as alterações feitas a itens em um contêiner. Para obter mais detalhes, consulte o [Feed de alteração](change-feed.md) artigo. | Sim | Não | Não | Não  | Sim |
|uniqueKeyPolicy | Configurável pelo usuário | Usado para garantir a exclusividade de um ou mais valores dentro de uma partição lógica. Para obter mais informações, consulte o [restrições de chave exclusiva](unique-keys.md) artigo. | Sim | Não | Não | Não  | Sim |

### <a name="operations-on-an-azure-cosmos-container"></a>Operações em um contêiner do Azure Cosmos

Um contêiner do Azure Cosmos dá suporte às operações a seguir, usando qualquer uma das APIs do Azure Cosmos.

| **Operação** | **CLI do Azure** | **API do SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerar os contêineres em um banco de dados | Sim | sim | sim | Sim | ND | ND |
| Ler um contêiner | Sim | sim | sim | Sim | ND | ND |
| Criar um novo contêiner | Sim | sim | sim | Sim | ND | ND |
| Atualizar um contêiner | Sim | sim | sim | Sim | ND | ND |
| Excluir um contêiner | Sim | sim | sim | Sim | ND | ND |

## <a name="azure-cosmos-items"></a>Itens do Azure Cosmos

Dependendo da escolha da API, um item do Azure Cosmos pode representar um documento em uma coleção, uma linha em uma tabela ou um nó/borda em um grafo. A tabela a seguir mostra o mapeamento entre entidades específicas a uma API para um item do Azure Cosmos:

| **Entidade do Cosmos** | **API do SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- |
|Item do Azure Cosmos | Documento | Linha | Documento | Nó ou Borda | item |

### <a name="properties-of-an-item"></a>Propriedades de um item

Cada item do Azure Cosmos tem as propriedades definidas pelo sistema listadas a seguir. Dependendo da escolha da API, alguns deles podem não ser diretamente expostos.

|**Propriedade definida pelo sistema** | **Sistema gerado ou configurável pelo usuário**| **Finalidade** | **API do SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | Gerada pelo sistema | Identificador exclusivo do item | Sim | Não | Não | Não | Não  |
|_etag | Gerada pelo sistema | Marca da entidade usada para controle de simultaneidade otimista | Sim | Não | Não | Não | Não  |
|_ts | Gerada pelo sistema | O carimbo de hora da última atualização do item | Sim | Não | Não | Não | Não  |
|_self | Gerada pelo sistema | URI endereçável do item | Sim | Não | Não | Não | Não  |
|ID | Você pode usar o | Nome exclusivo definido pelo usuário dentro de uma partição lógica. Se o usuário não especifica a ID, o sistema gerará automaticamente um. | Sim | sim | sim | sim | Sim |
|Propriedades arbitrárias definidas pelo usuário | Definido pelo usuário | Propriedades definidas pelo usuário, representadas na representação nativa de API (JSON, BSON, CQL, etc.) | Sim | sim | sim | sim | Sim |

### <a name="operations-on-items"></a>Operações em itens

Um item do Azure Cosmos dá suporte às operações a seguir, que podem ser realizadas usando qualquer uma das APIs do Azure Cosmos.

| **Operação** | **CLI do Azure** | **API do SQL** | **API do Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| Inserir, Substituir, Excluir, Upsert, Ler | Não  | sim | sim | sim | sim | Sim |

## <a name="next-steps"></a>Próximas etapas

Agora, você pode prosseguir para aprender os conceitos a seguir:

* [Como configurar a taxa de transferência provisionada em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md)
* [Como configurar a taxa de transferência provisionada em um contêiner do Azure Cosmos](how-to-provision-container-throughput.md)
* [Partições lógicas](partition-data.md)
* [Como configurar o TTL no contêiner do Azure Cosmos](how-to-time-to-live.md)
* [Como criar aplicativos reativos usando o feed de alterações](change-feed.md)
* [Como configurar a restrição de chave exclusiva no seu contêiner do Azure Cosmos](unique-keys.md)
