---
title: Introdução à API do Azure Cosmos DB para MongoDB
description: Saiba como você pode usar o Azure Cosmos DB para armazenar e consultar grandes quantidades de dados usando a API do Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: dfaa04077a5148f19f06aa5e1257b986377ea8c0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307122"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O [Azure Cosmos DB](introduction.md) é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. O Azure Cosmos DB fornece [distribuição global imediata](distribute-data-globally.md), [dimensionamento elástico da taxa de transferência e do armazenamento](partitioning-overview.md) mundialmente, latências de milissegundos de um dígito no 99º percentil e garantia de alta disponibilidade, tudo isso com suporte de [SLAs líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa dados automaticamente](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem a necessidade de lidar com o gerenciamento do esquema e do índice. Ele tem vários modelos e dá suporte a modelos de dados de colunas, grafos, valores-chave e documentos. O serviço Azure Cosmos DB implementa protocolos de transmissão para APIs NoSQL comuns incluindo o Cassandra, o MongoDB, o Gremlin e o Armazenamento de Tabelas do Azure. Isso permite que você use suas ferramentas e drivers de cliente NoSQL familiares para interagir com o banco de dados Cosmos.

> [!NOTE]
> O [modo de capacidade sem servidor](serverless.md) já está disponível na API do Azure Cosmos DB para MongoDB.

## <a name="wire-protocol-compatibility"></a>Compatibilidade de protocolo de transmissão

O Azure Cosmos DB implementa o protocolo de transmissão para MongoDB. Essa implementação permite compatibilidade transparente com SDKs, drivers e ferramentas de cliente MongoDB nativos. O Azure Cosmos DB não hospeda o mecanismo de banco de dados MongoDB. Os detalhes dos recursos com suporte do MongoDB podem ser encontrados aqui: 
- [API do Azure Cosmos DB para MongoDB versão 4.0](mongodb-feature-support-40.md)
- [API do Azure Cosmos DB para MongoDB versão 3.6](mongodb-feature-support-36.md)

Por padrão, as novas contas criadas com a API do Azure Cosmos DB para MongoDB são compatíveis com a versão 3.6 do protocolo de transmissão do MongoDB. Qualquer driver de cliente do MongoDB que entenda essa versão de protocolo deve conseguir se conectar nativamente ao Cosmos DB.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="API do Azure Cosmos DB para MongoDB" border="false":::

## <a name="key-benefits"></a>Principais benefícios

Os principais benefícios do Cosmos DB como um banco de dados como serviço distribuído globalmente e totalmente gerenciado estão descritos [aqui](introduction.md). Além disso, com a implementação nativa de protocolos de transmissão de APIs NoSQL populares, o Cosmos DB oferece os seguintes benefícios:

* Migrar facilmente o aplicativo para o Cosmos DB, preservando as partes significativas da lógica do aplicativo.
* Manter o aplicativo portátil e independente de fornecedor de nuvem.
* Obter os SLAs com suporte financeiro líderes do mercado para as APIs NoSQL comuns da plataforma Cosmos DB.
* Dimensionar elasticamente a produtividade e armazenamento provisionados para seus bancos de dados Cosmos com base na sua necessidade e pagar somente pela produtividade e o armazenamento que você precisa. Isso resulta em economia de custo significativa.
* Distribuição global, pronta para uso, com a replicação de várias regiões.

## <a name="cosmos-dbs-api-for-mongodb"></a>API do Cosmos DB para MongoDB

Siga os inícios rápidos para criar uma conta do Azure Cosmos e migrar seu aplicativo existente do MongoDB para usar o Azure Cosmos DB ou criar outro aplicativo:

* [Migrar um aplicativo Web Node.js existente do MongoDB](create-mongodb-nodejs.md).
* [Criar um aplicativo Web usando a API do Azure Cosmos DB para MongoDB e o SDK do .NET](create-mongodb-dotnet.md)
* [Criar um aplicativo de console usando a API do Azure Cosmos DB para MongoDB e o SDK do Java](create-mongodb-java.md)

## <a name="next-steps"></a>Próximas etapas

Aqui estão algumas dicas para começar:

* Siga o tutorial [Conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) para saber como obter informações sobre a cadeia de conexão da conta.
* Siga o tutorial [Usar o Studio 3T com o Azure Cosmos DB](mongodb-mongochef.md) para saber como criar uma conexão entre o banco de dados Cosmos DB e o aplicativo do MongoDB no Studio 3T.
* Siga o tutorial [Importar dados do MongoDB no Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) para importar os dados existentes para um banco de dados Cosmos DB.
* Conectar-se a uma conta do Cosmos usando o [Robo 3T](mongodb-robomongo.md).
* Saiba como [Configurar as preferências de leitura para aplicativos distribuídos globalmente](../cosmos-db/tutorial-global-distribution-mongodb.md).
* Encontre as soluções para erros comumente encontrados em nosso [guia de solução de problemas](mongodb-troubleshoot.md)


<sup>Observação: este artigo descreve um recurso do Azure Cosmos DB que fornece a compatibilidade de protocolo de transmissão com bancos de dados MongoDB. A Microsoft não executa bancos de dados MongoDB para fornecer esse serviço. O Azure Cosmos DB não está afiliado à MongoDB, Inc.</sup>
