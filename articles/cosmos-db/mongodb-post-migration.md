---
title: Etapas de otimização após a migração ao usar a API do Azure Cosmos DB para MongoDB
description: Este documento fornece as técnicas de otimização após a migração do MongoDB à APi do Azure Cosmos DB para Mongo DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331190"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de otimização após a migração ao usar a API do Azure Cosmos DB para MongoDB 

Depois de migrar os dados armazenados no banco de dados do MongoDB para a API do Azure Cosmos DB para MongoDB, você pode se conectar ao Azure Cosmos DB e gerenciar os dados. Este guia fornece as etapas que você deve considerar após a migração. Consulte a [MongoDB migrar para a API do Azure Cosmos DB para o tutorial de MongoDB](../dms/tutorial-mongodb-cosmos-db.md) para as etapas de migração.

Neste guia, você aprenderá a:
- [Conectar seu aplicativo](#connect-account)
- [Otimizar a política de indexação](#indexing)
- [Configurar a distribuição global para a API do Azure Cosmos DB para MongoDB](#distribute-data)
- [Definir o nível de consistência](#consistency)

> [!NOTE]
> A etapa pós-migração obrigatória somente em seu nível de aplicativo está mudando a cadeia de caracteres de conexão em seu aplicativo para apontar para sua nova conta do Azure Cosmos DB. Todas as outras etapas de migração são recomendadas as otimizações.
>

## <a id="connect-account"></a>Conectar seu aplicativo 

1. Em uma nova janela de entrada no [portal do Azure](https://www.portal.azure.com/)
2. Do [portal do Azure](https://www.portal.azure.com/), no painel esquerdo abrir o **todos os recursos** menu e localizar a conta do Azure Cosmos DB para o qual você migrou seus dados.
3. Abra o **cadeia de caracteres de Conexão** folha. O painel direito contém todas as informações necessárias para conectar-se à sua conta com êxito.
4. Use as informações de conexão na configuração do seu aplicativo (ou outros locais relevantes) para refletir a API do Azure Cosmos DB para a conexão do MongoDB em seu aplicativo. 
![Cadeia de caracteres de Conexão](./media/mongodb-post-migration/connection-string.png)

Para obter mais detalhes, consulte o [conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) página.

## <a id="indexing"></a>Otimizar a política de indexação

Todos os campos de dados são indexados automaticamente, por padrão, durante a migração de dados para o Azure Cosmos DB. Em muitos casos, essa política de indexação padrão é aceitável. Em geral, removendo índices otimiza as solicitações de gravação e ter a política (ou seja, a indexação automática) de indexação padrão otimiza a solicitações de leitura.

Para obter mais informações sobre indexação, consulte [indexação de dados na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md) , bem como a [indexação no Azure Cosmos DB](index-overview.md) artigos.

## <a id="distribute-data"></a>Distribui globalmente seus dados

O Azure Cosmos DB está disponível em todos os [regiões do Azure](https://azure.microsoft.com/regions/#services) em todo o mundo. Depois de selecionar o nível de consistência padrão para sua conta do Azure Cosmos DB, você pode associar uma ou mais regiões do Azure (dependendo das suas necessidades de distribuição global). Para alta disponibilidade e continuidade dos negócios, recomendamos sempre em execução em pelo menos 2 regiões. Você pode examinar as dicas para [otimiza o custo das implantações de várias regiões no Azure Cosmos DB](optimize-cost-regions.md).

Para distribuir globalmente seus dados, consulte [distribuir dados globalmente na API do Azure Cosmos DB para MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Definir o nível de consistência
Azure Cosmos DB oferece 5 bem-definidos [níveis de consistência](consistency-levels.md). Para ler sobre o mapeamento entre os níveis de consistência do MongoDB e o Azure Cosmos DB, leia [níveis de consistência e APIs do Azure Cosmos DB](consistency-levels-across-apis.md). O nível de consistência padrão é o nível de consistência de sessão. Alterar o nível de consistência é opcional e pode otimizá-lo para seu aplicativo. Para alterar o nível de consistência usando o portal do Azure:

1. Vá para o **consistência padrão** folha em configurações.
2. Selecione seu [nível de consistência](consistency-levels.md)

A maioria dos usuários deixe seu nível de consistência na configuração de consistência de sessão padrão. No entanto, há [compensações de disponibilidade e desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Próximas etapas

* [Conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md)
* [Conectar-se à conta do Azure Cosmos DB usando o Studio 3T](mongodb-mongochef.md)
* [Como distribuir globalmente leituras usando a API do Azure Cosmos DB para MongoDB](mongodb-readpreference.md)
* [Expirar os dados com a API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Níveis de coerência no Azure Cosmos DB](consistency-levels.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)





