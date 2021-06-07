---
title: Modelo de recurso do Azure Cosmos DB
description: Este artigo descreve Azure Cosmos DB modelo de recurso que inclui a conta do Azure Cosmos, o banco de dados, o contêiner e os itens. Ele também aborda a hierarquia desses elementos em uma conta do Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 4ed881b74f240946d98d9868344c898d3e9a9dad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627290"
---
# <a name="azure-cosmos-db-resource-model"></a>Modelo de recurso do Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB é uma plataforma como serviço (PaaS) totalmente gerenciada. Para começar a usar Azure Cosmos DB, inicialmente você deve criar uma conta do Azure Cosmos em sua assinatura do Azure e bancos de dados, contêineres, itens sob ele. Este artigo descreve o modelo de recurso Azure Cosmos DB e entidades diferentes na hierarquia do modelo de recurso.

A conta do Azure Cosmos é a unidade fundamental de distribuição global e alta disponibilidade. Sua conta do Azure Cosmos contém um nome DNS exclusivo e você pode gerenciar uma conta usando o portal do Azure ou o CLI do Azure, ou usando diferentes SDKs específicos do idioma. Para mais informações, consulte [como gerenciar sua conta do Azure Cosmos](how-to-manage-database-account.md). Para distribuir globalmente seus dados e a taxa de transferência em várias regiões do Azure, você pode adicionar e remover regiões do Azure para sua conta a qualquer momento. Você pode configurar sua conta para ter uma única região ou várias regiões de gravação. Para obter mais informações, consulte [como adicionar e remover regiões do Azure para sua conta](how-to-manage-database-account.md). Você pode configurar o nível de [consistência padrão](consistency-levels.md) em uma conta.

## <a name="elements-in-an-azure-cosmos-account"></a>Elementos em uma conta do Azure Cosmos

Um contêiner Cosmos do Azure é a unidade fundamental de escalabilidade. Você pode virtualmente ter uma taxa de transferência provisionada ilimitada (RU/s) e armazenamento em um contêiner. O Azure Cosmos DB particiona de forma transparente seu contêiner usando a chave de partição lógica que você especifica para dimensionar de forma elástica sua taxa de transferência e armazenamento provisionados.

No momento, você pode criar no máximo 50 contas do Azure Cosmos em uma assinatura do Azure (esse é um limite flexível que pode ser aumentado por meio da solicitação de suporte). Uma única conta do Azure Cosmos pode praticamente gerenciar uma quantidade ilimitada de dados e uma taxa de transferência provisionada. Para gerenciar seus dados e a taxa de transferência provisionada, você pode criar um ou mais bancos de dados do Azure Cosmos em sua conta e, nesse banco de dados, pode criar um ou mais contêineres. A imagem a seguir mostra a hierarquia de elementos em uma conta do Azure Cosmos:

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Hierarquia de uma conta do Azure Cosmos" border="false":::

Depois de criar uma conta em sua assinatura do Azure, você pode gerenciar os dados em sua conta Criando bancos de dados, contêineres e itens. 

A imagem a seguir mostra a hierarquia de entidades diferentes em uma conta de Azure Cosmos DB:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entidades de conta do Azure Cosmos" border="false":::

## <a name="azure-cosmos-databases"></a>Bancos de dados do Azure Cosmos

Você pode criar um ou vários bancos de dados do Azure Cosmos em sua conta. Um banco de dados é análogo a um namespace. Um banco de dados é a unidade de gerenciamento para um conjunto de contêineres de Cosmos do Azure. A tabela a seguir mostra como um banco de dados é mapeado para várias entidades específicas de API:

| Entidade do Azure Cosmos | API do SQL | API Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Banco de dados do Azure Cosmos | Banco de dados | Keyspace | Banco de dados | Banco de dados | NA |

> [!NOTE]
> Com contas de API de Tabela, quando você cria sua primeira tabela, um banco de dados padrão é criado automaticamente em sua conta do Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operações em um banco de dados do Azure Cosmos

Você pode interagir com um banco de dados Cosmos do Azure com as APIs Cosmos do Azure, conforme descrito na tabela a seguir:

| Operação | CLI do Azure | API do SQL | API Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerar todos os bancos de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | NA | NA |
|Ler banco de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | NA | NA |
|Criar novo banco de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | NA | NA |
|Atualizar banco de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | NA | NA |

## <a name="azure-cosmos-containers"></a>Contêineres do Azure Cosmos

Um contêiner Cosmos do Azure é a unidade de escalabilidade para taxa de transferência e armazenamento provisionados. Um contêiner é particionado horizontalmente e, em seguida, é replicado em várias regiões. Os itens que você adiciona ao contêiner são agrupados automaticamente em partições lógicas, que são distribuídas entre partições físicas, com base na chave de partição. A taxa de transferência em um contêiner é distribuída uniformemente entre as partições físicas. Para saber mais sobre particionamento e chaves de partição, consulte [Particionar dados](partitioning-overview.md). 

Ao criar um contêiner, você configura a taxa de transferência em um dos seguintes modos:

* **Modo de taxa de transferência provisionado dedicado**: a taxa de transferência provisionada em um contêiner é exclusivamente reservada para esse contêiner e é apoiada pelos SLAs. Para saber mais, confira [como provisionar a taxa de transferência em um contêiner](how-to-provision-container-throughput.md).

* **Modo de produtividade provisionada compartilhada**: esses contêineres compartilham a taxa de transferência provisionada com os outros contêineres no mesmo banco de dados (excluindo contêineres que foram configurados com taxa de transferência provisionada dedicada). Em outras palavras, a taxa de transferência provisionada no banco de dados é compartilhada entre todos os contêineres de "produtividade compartilhada". Para saber mais, confira [como provisionar a taxa de transferência em um banco de dados](how-to-provision-database-throughput.md).

> [!NOTE]
> Você pode configurar a taxa de transferência compartilhada e dedicada somente ao criar o banco de dados e o contêiner. Para alternar do modo de taxa de transferência dedicada para o modo de taxa de transferência compartilhada (e vice-versa) depois que o contêiner for criado, você precisará criar um novo contêiner e migrar os dados para o novo contêiner. Você pode migrar os dados usando o Azure Cosmos DB recurso de feed de alterações.

Um contêiner Cosmos do Azure pode ser dimensionado de forma elástica, independentemente de você criar contêineres usando modos de taxa de transferência provisionada ou compartilhadas.

Um contêiner é um contêiner de itens independente de esquema. Os itens em um contêiner podem ter esquemas arbitrários. Por exemplo, um item que representa uma pessoa e um item que representa um automóvel pode ser colocado no *mesmo contêiner*. Por padrão, todos os itens que você adiciona a um contêiner são indexados automaticamente sem a necessidade de um índice explícito ou gerenciamento de esquema. Você pode personalizar o comportamento de indexação Configurando a [política de indexação](index-overview.md) em um contêiner. 

Você pode definir [TTL (vida útil)](time-to-live.md) em itens selecionados em um contêiner ou para todo o contêiner para limpar normalmente esses itens do sistema. Azure Cosmos DB exclui automaticamente os itens quando eles expiram. Ele também garante que uma consulta executada no contêiner não retorne os itens expirados em um limite fixo. Para saber mais, consulte [Configurar TTL em seu contêiner](how-to-time-to-live.md).

Você pode usar o [feed de alterações](change-feed.md) para assinar o log de operações que é gerenciado para cada partição lógica do seu contêiner. O feed de alterações fornece o log de todas as atualizações executadas no contêiner, juntamente com as imagens anteriores e posteriores dos itens. Para obter mais informações, consulte [criar aplicativos reativos usando o feed de alterações](serverless-computing-database.md). Você também pode configurar a duração da retenção para o feed de alterações usando a política do feed de alterações no contêiner.

Você pode registrar [procedimentos armazenados, gatilhos, UDFs (funções definidas pelo usuário)](stored-procedures-triggers-udfs.md)e [procedimentos de mesclagem](how-to-manage-conflicts.md) para seu contêiner.

Você pode especificar uma [restrição de chave exclusiva](unique-keys.md) em seu contêiner Cosmos do Azure. Ao criar uma política de chave exclusiva, você garante a exclusividade de um ou mais valores por chave de partição lógica. Se você criar um contêiner usando uma política de chave exclusiva, nenhum item novo ou atualizado com valores que duplicar os valores especificados pela restrição UNIQUE KEY poderá ser criado. Para obter mais informações, confira [Restrições de chave exclusivas](unique-keys.md).

Um contêiner é especializado em entidades específicas de API, conforme mostrado na tabela a seguir:

| Entidade do Azure Cosmos | API do SQL | API Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Contêiner do Azure Cosmos | Contêiner | Tabela | Coleção | Grafo | Tabela |

> [!NOTE]
> Ao criar contêineres, não crie dois contêineres com o mesmo nome, mas com maiúsculas e minúsculas diferentes. Isso porque algumas partes da plataforma Azure não diferenciam maiúsculas de minúsculas, o que pode resultar em confusão/colisão de telemetria e ações em contêineres com tais nomes.

### <a name="properties-of-an-azure-cosmos-container"></a>Propriedades de um contêiner do Azure Cosmos

Um contêiner Cosmos do Azure tem um conjunto de propriedades definidas pelo sistema. Dependendo de qual API você usa, algumas propriedades podem não ser diretamente expostas. A tabela a seguir descreve a lista de propriedades definidas pelo sistema:

| Propriedade definida pelo sistema | Gerado pelo sistema ou configurável pelo usuário | Finalidade | API do SQL | API Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_eliminá | Gerado pelo sistema | Identificador exclusivo do contêiner | Sim | Não | Não | Não | Não |
|\_ETag | Gerado pelo sistema | Marca da entidade usada para controle de simultaneidade otimista | Sim | Não | Não | Não | Não |
|\_CALs | Gerado pelo sistema | Último carimbo de data/hora atualizado do contêiner | Sim | Não | Não | Não | Não |
|\_auto-restauração | Gerado pelo sistema | URI endereçável do contêiner | Sim | Não | Não | Não | Não |
|id | Configurável pelo usuário | Nome exclusivo do contêiner definido pelo usuário | Sim | Sim | Sim | Sim | Sim |
|indexingPolicy | Configurável pelo usuário | Fornece a capacidade de alterar o caminho do índice, o tipo de índice e o modo de índice | Sim | Não | Não | Não | Sim |
|timeToLive | Configurável pelo usuário | Fornece a capacidade de excluir itens automaticamente de um contêiner após um período de tempo definido. Para obter detalhes, consulte [vida útil](time-to-live.md). | Sim | Não | Não | Não | Sim |
|changeFeedPolicy | Configurável pelo usuário | Usado para ler as alterações feitas a itens em um contêiner. Para obter detalhes, consulte o [feed de alterações](change-feed.md). | Sim | Não | Não | Não | Sim |
|uniqueKeyPolicy | Configurável pelo usuário | Usado para garantir a exclusividade de um ou mais valores em uma partição lógica. Para obter mais informações, consulte [Unique Key Constraints](unique-keys.md). | Sim | Não | Não | Não | Sim |

### <a name="operations-on-an-azure-cosmos-container"></a>Operações em um contêiner do Azure Cosmos

Um contêiner Cosmos do Azure dá suporte às seguintes operações quando você usa qualquer uma das APIs Cosmos do Azure:

| Operação | CLI do Azure | API do SQL | API Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerar os contêineres em um banco de dados | Sim | Sim | Sim | Sim | NA | NA |
| Ler um contêiner | Sim | Sim | Sim | Sim | NA | NA |
| Criar um novo contêiner | Sim | Sim | Sim | Sim | NA | NA |
| Atualizar um contêiner | Sim | Sim | Sim | Sim | NA | NA |
| Excluir um contêiner | Sim | Sim | Sim | Sim | NA | NA |

## <a name="azure-cosmos-items"></a>Itens do Azure Cosmos

Dependendo da API que você usa, um item do Azure Cosmos pode representar um documento em uma coleção, uma linha em uma tabela ou um nó ou borda em um grafo. A tabela a seguir mostra o mapeamento de entidades específicas de API para um item Cosmos do Azure:

| Entidade do Cosmos | API do SQL | API Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Item do Azure Cosmos | Item | Linha | Documento | Nó ou borda | Item |

### <a name="properties-of-an-item"></a>Propriedades de um item

Cada item Cosmos do Azure tem as seguintes propriedades definidas pelo sistema. Dependendo de qual API você usa, alguns deles podem não ser expostos diretamente.

| Propriedade definida pelo sistema | Gerado pelo sistema ou configurável pelo usuário| Finalidade | API do SQL | API Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_eliminá | Gerado pelo sistema | Identificador exclusivo do item | Sim | Não | Não | Não | Não |
|\_ETag | Gerado pelo sistema | Marca da entidade usada para controle de simultaneidade otimista | Sim | Não | Não | Não | Não |
|\_CALs | Gerado pelo sistema | Carimbo de data/hora da última atualização do item | Sim | Não | Não | Não | Não |
|\_auto-restauração | Gerado pelo sistema | URI endereçável do item | Sim | Não | Não | Não | Não |
|id | Você pode usar o | Nome exclusivo definido pelo usuário em uma partição lógica. | Sim | Sim | Sim | Sim | Sim |
|Propriedades arbitrárias definidas pelo usuário | Definido pelo usuário | Propriedades definidas pelo usuário representadas na representação nativa da API (incluindo JSON, BSON e CQL) | Sim | Sim | Sim | Sim | Sim |

> [!NOTE]
> A exclusividade da `id` propriedade é imposta somente dentro de cada partição lógica. Vários documentos podem ter a mesma `id` propriedade com valores de chave de partição diferentes.

### <a name="operations-on-items"></a>Operações em itens

Os itens Cosmos do Azure dão suporte às seguintes operações. Você pode usar qualquer uma das APIs do Azure Cosmos para executar as operações.

| Operação | CLI do Azure | API do SQL | API Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Inserir, Substituir, Excluir, Upsert, Ler | Não | Sim | Sim | Sim | Sim | Sim |

## <a name="next-steps"></a>Próximas etapas

Saiba como gerenciar sua conta do Azure Cosmos e outros conceitos:

* [Como gerenciar sua conta do Azure Cosmos](how-to-manage-database-account.md)
* [Distribuição global](distribute-data-globally.md)
* [Níveis de consistência](consistency-levels.md)
* [Ponto de extremidade de serviço de rede virtual para sua conta do Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
* [Firewall de IP para sua conta do Azure Cosmos](how-to-configure-firewall.md)
* [Como adicionar e remover regiões do Azure para sua conta do Azure Cosmos](how-to-manage-database-account.md)
* [SLAs do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
