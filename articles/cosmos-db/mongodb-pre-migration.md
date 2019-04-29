---
title: Etapas de antes da migração para migrações de dados do MongoDB à API do Azure Cosmos DB para MongoDB
description: Este documento fornece uma visão geral dos pré-requisitos para uma migração de dados do MongoDB para o Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330733"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de antes da migração para migrações de dados do MongoDB à API do Azure Cosmos DB para MongoDB

Antes de migrar seus dados do MongoDB (localmente ou na nuvem (IaaS)) para a API do Azure Cosmos DB para MongoDB, você deve:

1. [Criar uma conta do BD Cosmos do Azure](#create-account)
2. [Estimar a taxa de transferência necessária para suas cargas de trabalho](#estimate-throughput)
3. [Escolher uma chave de partição ideal para seus dados](#partitioning)
4. [Entender a política de indexação que podem ser definidas em seus dados](#indexing)

Se você já concluiu os pré-requisitos acima para a migração, consulte o [dados do MongoDB de migrar para a API do Azure Cosmos DB para MongoDB](../dms/tutorial-mongodb-cosmos-db.md) para as instruções de migração de dados reais. Caso contrário, este documento fornece instruções para lidar com esses pré-requisitos. 

## <a id="create-account"></a> Criar uma conta do Azure Cosmos DB 

Antes de iniciar a migração, você precisará [criar uma conta de Cosmos do Azure usando a API do Azure Cosmos DB para MongoDB](create-mongodb-dotnet.md). 

A criação de conta, você pode escolher configurações a serem [distribuir globalmente](distribute-data-globally.md) seus dados. Você também tem a opção para habilitar gravações de várias regiões (ou configuração de vários mestre), que permite que cada uma das suas regiões ser ambos uma gravação e a região de leitura.

![Criação da conta](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> Estimar a necessidade de taxa de transferência para suas cargas de trabalho

Antes de iniciar a migração usando o [serviço de migração de banco de dados (DMS)](../dms/dms-overview.md), você deve estimar a quantidade de taxa de transferência para provisão para seus bancos de dados Cosmos do Azure e coleções.

Taxa de transferência pode ser provisionada em qualquer um:

- Coleção

- Banco de dados

> [!NOTE]
> Você também pode ter uma combinação dos itens acima, em que algumas coleções em um banco de dados podem ter dedicados a taxa de transferência provisionada e outras pessoas podem compartilhar a taxa de transferência. Para obter detalhes, consulte o [definir a taxa de transferência em um banco de dados e um contêiner](set-throughput.md) página.
>

Primeiro, você deve decidir se deseja provisionar o banco de dados ou a produtividade de nível de coleção ou uma combinação de ambos. Em geral, é recomendável configurar uma taxa de transferência dedicada no nível da coleção. Provisionando a produtividade no nível do banco de dados permite que as coleções no banco de dados para compartilhar a taxa de transferência provisionada. Com taxa de transferência compartilhada, no entanto, não há nenhuma garantia para uma taxa de transferência específica em cada coleção individual e você não obtiver um desempenho previsível em qualquer coleção específica.

Se você não tiver certeza sobre quanta taxa de transferência deve ser dedicada a cada coleção individual, você pode escolher o nível de banco de dados de taxa de transferência. Você pode pensar na produtividade provisionada configuradas em seu banco de dados Cosmos do Azure como um equivalente lógico para que a capacidade de computação de uma VM do MongoDB ou um servidor físico, mas é mais econômico com a capacidade de dimensionar de forma elástica. Para obter mais informações, consulte [provisionar a produtividade em bancos de dados e contêineres do Azure Cosmos](set-throughput.md).

Se você provisionar a taxa de transferência no nível do banco de dados, todas as coleções criadas dentro desse banco de dados devem ser criadas com uma chave de partição/fragmentação. Para obter mais informações sobre particionamento, consulte [particionamento e escala horizontal no Azure Cosmos DB](partition-data.md). Se você não especificar uma chave de partição/fragmentação durante a migração, o serviço de migração de banco de dados do Azure preenche automaticamente o campo de chave de fragmento com um *ID* atributo que é gerado automaticamente para cada documento.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Número ideal de unidades de solicitação (RUs) para provisionar

No Azure Cosmos DB, a taxa de transferência é provisionada com antecedência e é medida em unidades de solicitação (RU) por segundo. Se você tiver cargas de trabalho que executar o MongoDB em uma VM ou local, considere RUS como uma abstração simple para recursos físicos, como o tamanho de uma VM ou servidor-um local e os recursos que eles possuem, por exemplo, memória, CPU, o IOPs. 

Ao contrário de VMs ou servidores locais, RUs são fáceis de escalar verticalmente a qualquer momento. Você pode alterar o número de RUs provisionadas em segundos, e você será cobrado somente para o número máximo de RUs provisionar para um determinado período de uma hora. Para obter mais informações, confira [Unidades de solicitação no Azure Cosmos DB](request-units.md).

Estes são os principais fatores que afetam o número de RUs necessários:
- **Tamanho do item (ou seja, o documento)**: À medida que aumenta o tamanho de um item/documento, o número de RUs consumidas para ler ou gravar o documento de item/também aumenta.
- **Contagem de propriedades do item**: Supondo que o [indexação padrão](index-overview.md) em todas as propriedades, o número de RUs consumidas para gravar a um item aumenta à medida que os aumentos de contagem de propriedade do item. Você pode reduzir o consumo de unidades de solicitação para operações de gravação por [limitando o número de propriedades indexadas](index-policy.md).
- **Operações simultâneas**: Solicite unidades consumidas também depende da frequência com que operações CRUD diferentes (como gravações, leituras, atualizações e exclusões) e consultas mais complexas são executadas. Você pode usar [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) para as necessidades simultâneas de seus dados atuais do MongoDB de saída.
- **Padrões de consulta**: A complexidade de uma consulta afeta a quantidade de unidades de solicitação é consumidos pela consulta.

Se você exportar arquivos JSON usando [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) e entender quantas gravações, leituras, atualizações e exclusões que ocorrem por segundo, você pode usar o [Planejador de capacidade do Azure Cosmos DB](https://www.documentdb.com/capacityplanner) para estimar o número inicial de RUs para provisionar. O Planejador de capacidade não considerar o custo das consultas mais complexas. Portanto, se você tiver consultas complexas em seus dados, RUs adicionais serão consumidos. A Calculadora também pressupõe que todos os campos são indexados, e a consistência session é usada. A melhor maneira de entender o custo de consultas é migrar seus dados (ou dados de exemplo) para o Azure Cosmos DB, [conectar-se ao ponto de extremidade do Cosmos DB](connect-mongodb-account.md) e execute uma consulta de exemplo do MongoDB Shell usando o `getLastRequestStastistics` comando para obter o encargo de solicitação, que emitirá o número de RUs consumidas:

`db.runCommand({getLastRequestStatistics: 1})`

Esse comando retornará um documento JSON semelhante ao seguinte:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Depois que você entende o número de RUs consumidas por uma consulta e as necessidades de simultaneidade para a consulta, você pode ajustar o número de RUs provisionadas. Otimizar o RUs não é um evento único - continuamente, você deve otimizar ou expandir as RUs provisionadas, dependendo se você não estiver esperando um tráfego intenso e, em vez de uma carga de trabalho pesada ou importação de dados.

## <a id="partitioning"></a>Escolha sua chave de partição
O particionamento é um ponto importante consideração antes de migrar para um banco de dados globalmente distribuído como o Azure Cosmos DB. O Azure Cosmos DB usa o particionamento para dimensionar contêineres individuais em um banco de dados para atender às necessidades de desempenho e escalabilidade do seu aplicativo. Particionamento, os itens em um contêiner são divididos em subconjuntos distintos chamados partições lógicas. Para obter detalhes e recomendações sobre como escolher a chave de partição correta para seus dados, consulte o [escolhendo uma seção de chave de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Os dados de índice
Por padrão, o Azure Cosmos DB indexa todos os seus campos de dados após a ingestão. Você pode modificar os [política de indexação](index-policy.md) no Azure Cosmos DB a qualquer momento. Na verdade, geralmente é recomendável desativar a indexação durante a migração de dados e, em seguida, ativá-lo novamente quando os dados já estão no Cosmos DB. Para obter mais detalhes sobre a indexação, você pode ler mais sobre ela na [indexação no Azure Cosmos DB](index-overview.md) seção. 

[Serviço de migração de banco de dados do Azure](../dms/tutorial-mongodb-cosmos-db.md) migra automaticamente as coleções do MongoDB com índices exclusivos. No entanto, os índices exclusivos devem ser criados antes da migração. O Azure Cosmos DB não suporta a criação de índices exclusivos, quando já houver dados em suas coleções. Para obter mais informações, veja [Chaves exclusivas no Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Próximas etapas
* [Migre os dados do MongoDB para o Cosmos DB usando o serviço de migração de banco de dados.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Provisionar a produtividade em contêineres do Cosmos do Azure e bancos de dados](set-throughput.md)
* [Particionamento no Azure Cosmos DB](partition-data.md)
* [Distribuição global no Azure Cosmos DB](distribute-data-globally.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
