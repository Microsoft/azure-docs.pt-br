---
title: Mover uma conta de Azure Cosmos DB para outra região
description: Saiba como mover uma conta de Azure Cosmos DB para outra região.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93342021"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Mover uma conta de Azure Cosmos DB para outra região
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve como:

- Mova uma região onde os dados são replicados em Azure Cosmos DB.
- Migre dados e metadados de conta (Azure Resource Manager) de uma região para outra.

## <a name="move-data-from-one-region-to-another"></a>Mover dados de uma região para outra

O Azure Cosmos DB dá suporte à replicação de dados nativamente, portanto, é simples mover dados de uma região para outra. Você pode fazer isso usando o portal do Azure, Azure PowerShell ou o CLI do Azure. Ele envolve as seguintes etapas:

1. Adicione uma nova região à conta.

    Para adicionar uma nova região a uma conta de Azure Cosmos DB, consulte [Adicionar/remover regiões a uma conta de Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

1. Execute um failover manual para a nova região.

    Quando a região que está sendo removida for atualmente a região de gravação para a conta, você precisará iniciar um failover para a nova região adicionada na etapa anterior. Essa é uma operação sem tempo de inatividade. Se você estiver movendo uma região de leitura em uma conta de várias regiões, poderá ignorar esta etapa. 
    
    Para iniciar um failover, consulte [executar failover manual em uma conta do Azure Cosmos](how-to-manage-database-account.md#manual-failover).

1. Remova a região original.

    Para remover uma região de uma conta de Azure Cosmos DB, consulte [Adicionar/remover regiões de sua conta de Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Migrar metadados da conta de Azure Cosmos DB

O Azure Cosmos DB não dá suporte nativo à migração de metadados da conta de uma região para outra. Para migrar os metadados da conta e os dados do cliente de uma região para outra, você deve criar uma nova conta na região desejada e, em seguida, copiar os dados manualmente. 

Uma migração quase sem tempo de inatividade para a API do SQL requer o uso do [feed de alterações](change-feed.md) ou de uma ferramenta que o utiliza. Se você estiver migrando a API do MongoDB, a API do Cassandra ou outra API, ou para saber mais sobre as opções de migração de dados entre contas, consulte [opções para migrar seus dados locais ou na nuvem para Azure Cosmos DB](cosmosdb-migrationchoices.md). 

As etapas a seguir demonstram como migrar uma conta de Azure Cosmos DB para a API do SQL e seus dados de uma região para outra:

1. Crie uma nova conta de Azure Cosmos DB na região desejada.

    Para criar uma nova conta por meio do portal do Azure, do PowerShell ou do CLI do Azure, consulte [criar uma conta de Azure Cosmos DB](how-to-manage-database-account.md#create-an-account).

1. Crie um novo banco de dados e contêiner.

    Para criar um novo banco de dados e contêiner, consulte [criar um contêiner Cosmos do Azure](how-to-create-container.md).

1. Migre dados usando a ferramenta Azure Cosmos DB Live Data Migrator.

    Para migrar dados com quase zero tempo de inatividade, consulte a [ferramenta Azure Cosmos DB Live Data Migrator](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator).

1. Atualize a cadeia de conexão do aplicativo.

    Com a ferramenta do Live Data Migrator ainda em execução, atualize as informações de conexão na nova implantação do seu aplicativo. Você pode recuperar os pontos de extremidade e as chaves do seu aplicativo do portal do Azure.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Controle de acesso no portal do Azure, demonstrando a segurança do banco de dados NoSQL.":::

1. Redirecionar solicitações para o novo aplicativo.

    Depois que o novo aplicativo estiver conectado a Azure Cosmos DB, você poderá redirecionar as solicitações de cliente para a nova implantação.

1. Exclua todos os recursos que você não precisa mais.

    Com as solicitações agora redirecionadas completamente para a nova instância, você pode excluir a conta de Azure Cosmos DB antiga e a ferramenta do Live Data Migrator.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos sobre como gerenciar a conta do Azure Cosmos, bem como bancos de dados e contêineres, leia os seguintes artigos:

* [Gerenciar uma conta do Azure Cosmos](how-to-manage-database-account.md)
* [Alterar feed no Azure Cosmos DB](change-feed.md)
