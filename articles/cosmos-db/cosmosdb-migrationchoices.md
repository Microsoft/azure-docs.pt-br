---
title: Opções de migração de Cosmos DB
description: Este documento descreve as várias opções para migrar seus dados locais ou na nuvem para Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 8721c0eb728f568521e86baecb658dc9c869a7f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097576"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opções para migrar seus dados locais ou na nuvem para Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Você pode carregar dados de várias fontes de dados para Azure Cosmos DB. Como Azure Cosmos DB dá suporte a várias APIs, os destinos podem ser qualquer uma das APIs existentes. A seguir estão alguns cenários em que você migra dados para Azure Cosmos DB:

* Mova os dados de um contêiner Cosmos do Azure para outro contêiner no mesmo banco de dados ou em um outro.
* Movendo dados entre contêineres dedicados para contêineres de banco de dados compartilhados.
* Mova dados de uma conta do Azure Cosmos localizada em region1 para outra conta do Azure Cosmos na mesma região ou em outra.
* Mova dados de uma fonte como o armazenamento de BLOBs do Azure, um arquivo JSON, um banco de dados Oracle, Couchbase, DynamoDB para Azure Cosmos DB.

Para oferecer suporte a caminhos de migração de várias fontes para as diferentes APIs de Azure Cosmos DB, há várias soluções que fornecem tratamento especializado para cada caminho de migração. Este documento lista as soluções disponíveis e descreve suas vantagens e limitações.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Fatores que afetam a escolha da ferramenta de migração

Os seguintes fatores determinam a escolha da ferramenta de migração:

* **Migração online ou offline**: Muitas ferramentas de migração fornecem um caminho para fazer apenas migração única. Isso significa que os aplicativos que acessam o banco de dados podem apresentar tempo de inatividade. Algumas soluções de migração fornecem uma maneira de fazer uma migração em tempo real, em que há um pipeline de replicação configurado entre a origem e o destino.

* **Fonte de dados**: os dados existentes podem estar em várias fontes de dados, como Oracle DB2, Datastax Cassanda, banco de dados SQL do Azure, PostgreSQL, etc. Os dados também podem estar em uma conta de Azure Cosmos DB existente e a intenção de migração pode ser alterar o modelo de dados ou reparticionar os dados em um contêiner com uma chave de partição diferente.

* **API do Azure Cosmos DB**: Para a API do SQL na Azure Cosmos DB, há uma variedade de ferramentas desenvolvidas pela equipe do Azure Cosmos DB que auxiliam nos diferentes cenários de migração. Todas as outras APIs têm seu próprio conjunto especializado de ferramentas desenvolvidas e mantidas pela comunidade. Como o Azure Cosmos DB dá suporte a essas APIs em um nível de protocolo de conexão, essas ferramentas devem funcionar como estão enquanto migram dados para o Azure Cosmos DB também. No entanto, elas podem exigir manipulação personalizada para restrições, pois esse conceito é específico do Azure Cosmos DB.

* **Tamanho dos dados**: A maioria das ferramentas de migração funciona muito bem para conjuntos de valores menores. Quando o conjunto de dados exceder algumas centenas de gigabytes, as opções de ferramentas de migração ficarão limitadas. 

* **Duração prevista da migração**: As migrações podem ser configuradas para ocorrer em um ritmo lento e incremental que consome menos taxa de transferência ou pode consumir toda a taxa de transferência provisionada no contêiner do Azure Cosmos DB de destino e concluir a migração em menos tempo.

## <a name="azure-cosmos-db-sql-api"></a>API do SQL do Azure Cosmos DB

|Tipo de migração|Solução|Fontes compatíveis|Destinos com suporte|Considerações|
|---------|---------|---------|---------|---------|
|Offline|[Ferramenta de migração de dados](import-data.md)| &bull;Arquivos JSON/CSV<br/>&bull;API do SQL do Azure Cosmos DB<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Armazenamento de Tabelas<br/>&bull;AWS DynamoDB<br/>&bull;Armazenamento do Blobs do Azure|&bull;API do SQL do Azure Cosmos DB<br/>&bull;API de tabelas Azure Cosmos DB<br/>&bull;Arquivos JSON |&bull; Fácil de configurar e dar suporte a várias fontes. <br/>&bull; Não é adequada para grandes conjuntos de dados.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;Arquivos JSON/CSV<br/>&bull;API do SQL do Azure Cosmos DB<br/>&bull;API do Azure Cosmos DB para MongoDB<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Armazenamento de Tabelas<br/>&bull;Armazenamento do Blobs do Azure <br/> <br/>Consulte o artigo [Azure data Factory](../data-factory/connector-overview.md) para obter outras fontes com suporte.|&bull;API do SQL do Azure Cosmos DB<br/>&bull;API do Azure Cosmos DB para MongoDB<br/>&bull;Arquivos JSON <br/><br/> Consulte o artigo [Azure data Factory](../data-factory/connector-overview.md) para outros destinos com suporte. |&bull; Fácil de configurar e dar suporte a várias fontes.<br/>&bull; Usa a biblioteca de executores em massa Azure Cosmos DB. <br/>&bull; Adequado para grandes conjuntos de altos. <br/>&bull; Falta de ponto de verificação-isso significa que, se ocorrer um problema durante a migração, você precisará reiniciar todo o processo de migração.<br/>&bull; Falta de uma fila de mensagens mortas-isso significa que alguns arquivos errados podem parar todo o processo de migração.|
|Offline|[Azure Cosmos DB conector do Spark](spark-connector.md)|Azure Cosmos DB API do SQL. <br/><br/>Você pode usar outras fontes com conectores adicionais do ecossistema do Spark.| Azure Cosmos DB API do SQL. <br/><br/>Você pode usar outros destinos com conectores adicionais do ecossistema do Spark.| &bull; Usa a biblioteca de executores em massa Azure Cosmos DB. <br/>&bull; Adequado para grandes conjuntos de altos. <br/>&bull; Precisa de uma instalação personalizada do Spark. <br/>&bull; O Spark é sensível a inconsistências de esquema e pode ser um problema durante a migração. |
|Offline|[Ferramenta personalizada com Cosmos DB biblioteca de executores em massa](migrate-cosmosdb-data.md)| A origem depende do seu código personalizado | API do SQL do Azure Cosmos DB| &bull; Fornece recursos de ponto de verificação e mensagens mortas que aumentam a resiliência da migração. <br/>&bull; Adequado para conjuntos de grandes volumes de valores (10 TB +).  <br/>&bull; Requer a instalação personalizada dessa ferramenta em execução como um serviço de aplicativo. |
|Online|[Funções de Cosmos DB + API ChangeFeed](change-feed-functions.md)| API do SQL do Azure Cosmos DB | API do SQL do Azure Cosmos DB| &bull; Fácil de configurar. <br/>&bull; Funciona somente se a origem for um contêiner Azure Cosmos DB. <br/>&bull; Não é adequada para grandes conjuntos de dados. <br/>&bull; Não captura exclusões do contêiner de origem. |
|Online|[Serviço de migração personalizado usando ChangeFeed](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| API do SQL do Azure Cosmos DB | API do SQL do Azure Cosmos DB| &bull; Fornece rastreamento de progresso. <br/>&bull; Funciona somente se a origem for um contêiner Azure Cosmos DB. <br/>&bull; Também funciona para conjuntos de valores maiores.<br/>&bull; Exige que o usuário configure um serviço de aplicativo para hospedar o processador do feed de alterações. <br/>&bull; Não captura exclusões do contêiner de origem.|
|Online|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Consulte o [site do Striim](https://www.striim.com/sources-and-targets/) para obter outras fontes com suporte. |&bull;API do SQL do Azure Cosmos DB <br/>&bull; Azure Cosmos DB API do Cassandra<br/><br/> Consulte o [site do Striim](https://www.striim.com/sources-and-targets/) para obter outros destinos com suporte. | &bull; Funciona com uma grande variedade de fontes, como Oracle, DB2 SQL Server.<br/>&bull; É fácil criar pipelines ETL e fornece um painel para monitoramento. <br/>&bull; Dá suporte a conjuntos de grandes maiores. <br/>&bull; Como essa é uma ferramenta de terceiros, ela precisa ser adquirida no Marketplace e instalada no ambiente do usuário.|

## <a name="azure-cosmos-db-mongo-api"></a>API do Mongo Azure Cosmos DB

|Tipo de migração|Solução|Fontes compatíveis|Destinos com suporte|Considerações|
|---------|---------|---------|---------|---------|
|Online|[Serviço de Migração de Banco de Dados do Azure](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|API do Azure Cosmos DB para MongoDB |&bull; Usa a biblioteca de executores em massa Azure Cosmos DB. <br/>&bull; Adequado para grandes conjuntos de altos e cuida da replicação de alterações dinâmicas. <br/>&bull; Funciona somente com outras fontes do MongoDB.|
|Offline|[Serviço de Migração de Banco de Dados do Azure](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| API do Azure Cosmos DB para MongoDB| &bull; Usa a biblioteca de executores em massa Azure Cosmos DB. <br/>&bull; Adequado para grandes conjuntos de altos e cuida da replicação de alterações dinâmicas. <br/>&bull; Funciona somente com outras fontes do MongoDB.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;Arquivos JSON/CSV<br/>&bull;API do SQL do Azure Cosmos DB<br/>&bull;API do Azure Cosmos DB para MongoDB <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Armazenamento de Tabelas<br/>&bull;Armazenamento do Blobs do Azure <br/><br/> Consulte o artigo [Azure data Factory](../data-factory/connector-overview.md) para obter outras fontes com suporte. | &bull;API do SQL do Azure Cosmos DB<br/>&bull;API do Azure Cosmos DB para MongoDB <br/>&bull; Arquivos JSON <br/><br/> Consulte o artigo [Azure data Factory](../data-factory/connector-overview.md) para outros destinos com suporte.| &bull; Fácil de configurar e dar suporte a várias fontes. <br/>&bull; Usa a biblioteca de executores em massa Azure Cosmos DB. <br/>&bull; Adequado para grandes conjuntos de altos. <br/>&bull; A falta de ponto de verificação significa que qualquer problema durante o curso de migração exigiria uma reinicialização do processo de migração inteiro.<br/>&bull; A falta de uma fila de mensagens mortas significa que alguns arquivos errados poderiam parar todo o processo de migração. <br/>&bull; Precisa de código personalizado para aumentar a taxa de transferência de leitura para determinadas fontes de dados.|
|Offline|[Ferramentas do Mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | API do Azure Cosmos DB para MongoDB| &bull; Fácil de configurar e de integrar. <br/>&bull; Precisa de manipulação personalizada para limitação.|

## <a name="azure-cosmos-db-cassandra-api"></a>API do Cassandra do Azure Cosmos DB

|Tipo de migração|Solução|Fontes compatíveis|Destinos com suporte|Considerações|
|---------|---------|---------|---------|---------|
|Offline|[comando de cópia cqlsh](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|Arquivos CSV | API do Cassandra do Azure Cosmos DB| &bull; Fácil de configurar. <br/>&bull; Não é adequada para grandes conjuntos de dados. <br/>&bull; Funciona somente quando a origem é uma tabela Cassandra.|
|Offline|[Copiar tabela com Spark](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;API do Cassandra do Azure Cosmos DB| API do Cassandra do Azure Cosmos DB | &bull; Pode fazer uso de recursos do Spark para paralelizar a transformação e a ingestão. <br/>&bull; Precisa de configuração com uma política de repetição personalizada para manipular limitadores.|
|Online|[Striim (de Oracle DB/Apache Cassandra)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Consulte o [site do Striim](https://www.striim.com/sources-and-targets/) para obter outras fontes com suporte.|&bull;API do SQL do Azure Cosmos DB<br/>&bull;API do Cassandra do Azure Cosmos DB <br/><br/> Consulte o [site do Striim](https://www.striim.com/sources-and-targets/) para obter outros destinos com suporte.| &bull; Funciona com uma grande variedade de fontes, como Oracle, DB2 SQL Server. <br/>&bull; É fácil criar pipelines ETL e fornece um painel para monitoramento. <br/>&bull; Dá suporte a conjuntos de grandes maiores. <br/>&bull; Como essa é uma ferramenta de terceiros, ela precisa ser adquirida no Marketplace e instalada no ambiente do usuário.|
|Online|[Blitzz (de Oracle DB/Apache Cassandra)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Consulte o [site do Blitzz](https://www.blitzz.io/) para obter outras fontes com suporte. |Azure Cosmos DB API do Cassandra. <br/><br/>Consulte o [site do Blitzz](https://www.blitzz.io/) para obter outros destinos com suporte. | &bull; Dá suporte a conjuntos de grandes maiores. <br/>&bull; Como essa é uma ferramenta de terceiros, ela precisa ser adquirida no Marketplace e instalada no ambiente do usuário.|

## <a name="other-apis"></a>Outras APIs

Para APIs que não sejam a API do SQL, a API Mongo e a API do Cassandra, há várias ferramentas com suporte em cada um dos ecossistemas existentes da API. 

**API de Tabela** 

* [Ferramenta de Migração de Dados](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**API do Gremlin**

* [Biblioteca de executor em massa do grafo](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais experimentando os aplicativos de exemplo que consomem a biblioteca de executores em massa no [.net](bulk-executor-dot-net.md) e no [Java](bulk-executor-java.md). 
* A biblioteca de executores em massa é integrada ao conector do Cosmos DB Spark, para saber mais, consulte o artigo [Azure Cosmos DB conector do Spark](spark-connector.md) .  
* Entre em contato com a equipe de produto Azure Cosmos DB abrindo um tíquete de suporte no tipo de problema "consultoria geral" e no subtipo de problema "migrações grandes (TB +)" para obter ajuda adicional com migrações em larga escala.
