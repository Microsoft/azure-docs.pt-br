---
title: Introdução ao Azure Cosmos DB
description: Saiba mais sobre o Azure Cosmos DB. Este banco de dados multimodelo distribuído globalmente foi criado para alta disponibilidade, escalabilidade elástica e baixa latência e oferece suporte nativo para dados do NoSQL.
author: rimman
ms.service: cosmos-db
ms.topic: overview
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: ee863b6003444ec099753a6488f3fe4078338e60
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384129"
---
# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao Azure Cosmos DB

As aplicações de hoje precisam ser altamente responsivas e sempre on-line. Para obter baixa latência e alta disponibilidade, instâncias desses aplicativos precisam ser implantadas em datacenters próximos aos seus usuários. Aplicativos precisam responder em tempo real a grandes alterações no uso no horário de pico, armazenar volumes de dados crescentes e disponibilizar esses dados aos usuários em milissegundos.

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Com um clique de um botão, o Cosmos DB permite que você dimensione a taxa de transferência e o armazenamento de maneira elástica e independente em qualquer número de regiões do Azure. Você pode dimensionar elasticamente a taxa de transferência e armazenamento, além de se beneficiar do acesso a dados rápido, em um valor de milissegundos de dígito único, incluindo sua API de favoritos entre o SQL, o MongoDB, o Cassandra, as Tabelas ou o Gremlin. O Cosmos DB fornece SLAs [contratos de nível de serviço](https://aka.ms/acdbsla) abrangentes para taxa de transferência, latência, disponibilidade e consistência, algo que nenhum outro serviço de banco de dados pode oferecer.

Você pode [Experimentar o Azure Cosmos DB Gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, gratuitamente e sem compromisso.

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB Gratuitamente](https://azure.microsoft.com/try/cosmosdb/)

![O Azure Cosmos DB é serviço de banco de dados distribuído globalmente da Microsoft com escala horizontal elástica, baixa latência garantida, cinco modelos de consistência e SLAs de garantia abrangente](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Principais benefícios

### <a name="turnkey-global-distribution"></a>Distribuição global turnkey

O Cosmos DB permite que você crie aplicativos altamente responsivos e altamente disponíveis em todo o mundo. O Cosmos DB replica seus dados de forma transparente onde quer que seus usuários estejam, de modo que os usuários possam interagir com uma réplica dos dados mais próximos deles.

O Cosmos DB permite que você adicione ou remova qualquer uma das regiões do Azure à sua conta do Cosmos a qualquer momento, com um clique de um botão. O Cosmos DB replicará perfeitamente os dados para todas as regiões associadas à sua conta do Cosmos, enquanto seu aplicativo continuará a ser altamente disponível graças às funcionalidades de *hospedagem múltipla* do serviço. Para obter mais informações, confira o artigo [distribuição global](distribute-data-globally.md).

### <a name="always-on"></a>Always On

Em virtude da profunda integração à infraestrutura do Azure e a [replicação de vários mestres transparente](global-dist-under-the-hood.md), o Cosmos DB fornece 99,999% [de alta disponibilidade](high-availability.md) para leituras e gravações. O Cosmos DB também fornece a capacidade de invocar programaticamente (ou por meio do portal) o failover regional de sua conta do Cosmos. Esse recurso ajuda a garantir que seu aplicativo é criado para o failover no caso de desastre regional.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>A escalabilidade de taxa de transferência e armazenamento em todo o mundo

Projetado com o particionamento horizontal transparente e a replicação de vários mestres, o Cosmos DB oferece escalabilidade elástica sem precedentes para suas gravações e leituras, no mundo todo. Elasticamente, você pode expandir de milhares a centenas de milhões de solicitações/s em todo o mundo, com uma única chamada à API, pagando apenas pela taxa de transferência (e armazenamento) necessários. Essa funcionalidade ajuda você a lidar com picos inesperados em suas cargas de trabalho sem a necessidade de sobreprovisionar para o horário de pico. Para obter mais informações, consulte [particionamento no Cosmos DB](partitioning-overview.md), [taxa de transferência provisionada em contêineres e bancos de dados](set-throughput.md) e [dimensionar a taxa de transferência provisionada globalmente](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Baixa latência garantida no 99º percentil, no mundo todo

Usando o Cosmos DB, você pode criar aplicativos de escala mundial altamente dinâmicos. Com seu novo protocolo de replicação de vários mestres e o [mecanismo de banco de dados otimizado para gravação](index-policy.md) e sem trava, o Cosmos DB garante latências inferiores a 10 ms para gravações e leituras (indexadas) no 99º percentil, no mundo todo. Essa funcionalidade permite a ingestão prolongada de dados e consultas extremamente rápidas para aplicativos altamente responsivos.

### <a name="precisely-defined-multiple-consistency-choices"></a>Precisamente definidas, várias opções de consistência

Ao criar aplicativos distribuídos globalmente no Cosmos DB, você não precisa fazer [compensações extremas entre consistência, disponibilidade, latência e taxa de transferência](consistency-levels-tradeoffs.md). O protocolo de replicação de vários mestres do Cosmos DB foi cuidadosamente projetado para oferecer [cinco opções de consistência bem definidas](consistency-levels.md) -  *– forte*, *desatualização limitada*, *sessão*, *prefixo consistente*, e *eventual* – para um modelo de programação intuitivo com baixa latência e alta disponibilidade para seu aplicativo globalmente distribuído.

### <a name="no-schema-or-index-management"></a>Sem gerenciamento de esquema ou de índice

Manter o esquema de banco de dados e índices em sincronia com o esquema de um aplicativo é especialmente difícil para aplicativos distribuídos globalmente. Com o Cosmos DB, não é necessário lidar com gerenciamento de esquemas ou de índices. O mecanismo de banco de dados é completamente independente de esquemas.  Já que nenhum gerenciamento de esquema e de índice é necessário, você também não precisa se preocupar com tempo de inatividade do aplicativo ao migrar esquemas. O Cosmos DB [indexa automaticamente todos os dados](index-policy.md) e serve consultas rapidamente.

### <a name="battle-tested-database-service"></a>Serviço de banco de dados testado na prática

O Cosmos DB é um serviço básico no Azure. Por quase uma década, o Cosmos DB foi usado por muitos dos produtos da Microsoft para aplicativos críticos em escala global, incluindo Skype, Xbox, Office 365, Azure e muitos outros. Atualmente, o Cosmos DB é um dos serviços de crescimento mais rápido no Azure, usado por muitos clientes externos e aplicativos de missão crítica que exigem escala elástica, distribuição global de turnkey, replicação de vários mestres para latência baixa e disponibilidade alta de leituras e gravações.

### <a name="ubiquitous-regional-presence"></a>Presença regional em todos os lugares

O Cosmos DB está disponível em todas as regiões do Azure em todo o mundo, incluindo 54+ regiões na nuvem pública, [Azure China 21Vianet](https://www.azure.cn/en-us/), Azure Alemanha, Azure Governamental e Azure Governamental para o DoD (Departamento de Defesa). Confira [Presença regional do Cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Seguro por padrão e pronto para empresas

O Cosmos DB é certificado para uma [vasta gama de padrões de conformidade](compliance.md). Além disso, todos os dados no Cosmos DB são criptografados em repouso e em movimento. O Cosmos DB fornece autorização de nível de linha e obedece a padrões de segurança rígidos.

### <a name="significant-tco-savings"></a>Economias de TCO significativas

Como o Cosmos DB é um serviço totalmente gerenciado, você não precisa gerenciar e operar implantações de vários datacenters complexos e atualizações do seu software de banco de dados, pagar pelo suporte licenciamento ou operações para fornecer seu banco de dados para a carga de trabalho de pico. Para obter mais informações, consulte [Otimizar custo com o Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>SLAs abrangentes líderes do setor

O Cosmos DB é o primeiro e único serviço a oferecer [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/), incluindo alta disponibilidade de 99,999%, latência de leitura e de gravação no 99º percentil, taxa de transferência garantida e consistência.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Análise operacional distribuída globalmente e IA com Apache Spark interno nativo

É possível executar o [Spark](spark-connector.md) diretamente em dados armazenados no Cosmos DB. Essa funcionalidade permite que você realize a análise operacional de baixa latência em escala global sem afetar a cargas de trabalho transacionais, operando diretamente com o Cosmos DB. Para obter mais informações, consulte [Análise operacional distribuída globalmente](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Desenvolver aplicativos no Cosmos DB usando APIs populares de OSS (Software Livre)

O Cosmos DB oferece uma escolha de APIs para atualizar e consultar os dados armazenados em seu banco de dados do Cosmos. Por padrão, [você pode usar o SQL](how-to-sql-query.md) (uma API principal) para consultar seu banco de dados do Cosmos. O Cosmos DB também implementa APIs para [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) e [Armazenamento de Tabelas do Azure](table-introduction.md). Você pode apontar drivers de cliente (e ferramentas) para o NoSQL comumente usado (por exemplo, o MongoDB, Cassandra, Gremlin) diretamente para seu banco de dados do Cosmos. Oferecer suporte aos protocolos de transmissão de APIs de NoSQL comumente usadas, o Cosmos DB permite:

* Migrar facilmente o aplicativo para o Cosmos DB, preservando as partes significativas da lógica do aplicativo.
* Manter o aplicativo portátil e independente de fornecedor de nuvem.
* Obter o serviço de nuvem completamente gerenciado com o líder do setor, SLAs apoiadas financeiramente para as APIs de NoSQL. 
* Dimensione elasticamente a taxa de transferência provisionada e o armazenamento para seus bancos de dados com base na sua necessidade e pague somente pela taxa de transferência e o armazenamento que você precisa. Isso resulta em economia de custo significativa.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluções que se beneficiam do Azure Cosmos DB

Qualquer aplicativo [Web, móvel, de jogos e IoT](use-cases.md) que precisa lidar com grandes quantidades de dados lê e escreve em uma escala [global](distribute-data-globally.md) com tempos de resposta quase reais para uma variedade de dados, se beneficiarão da alta disponibilidade, alta taxa de transferência, baixa latência e consistência ajustável [garantidas](https://azure.microsoft.com/support/legal/sla/cosmos-db/) do Cosmos DB. Saiba como o Azure Cosmos DB pode ser usado para criar [IoT e telemática](use-cases.md#iot-and-telematics), [varejo e marketing](use-cases.md#retail-and-marketing), [jogos](use-cases.md#gaming), [aplicativos Web e móveis](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre a [distribuição global turnkey](distribute-data-globally.md) do Cosmos DB e [particionamento](partitioning-overview.md) e [taxa de transferência provisionada](request-units.md).

Comece no Azure Cosmos DB com um dos nossos guias de início rápido:

* [Introdução à API de SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introdução à API do MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
