---
title: Cotas de serviço do Azure Cosmos DB
description: Cotas de serviço do Azure Cosmos DB e limites padrão em diferentes tipos de recursos.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 74df0038676e8459028084890da569ed3b75a682
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797295"
---
# <a name="azure-cosmos-db-service-quotas"></a>Cotas de serviço do Azure Cosmos DB

Este artigo fornece uma visão geral das cotas padrão oferecido para recursos diferentes no Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Armazenamento e taxa de transferência

Depois de criar uma conta de Cosmos do Azure em sua assinatura, você pode gerenciar dados em sua conta por [criação de bancos de dados, contêineres e itens](databases-containers-items.md). Você pode provisionar a taxa de transferência em um nível de contêiner ou um nível de banco de dados em termos de [(RU/s ou RUs) de unidades de solicitação](request-units.md). A tabela a seguir lista os limites de armazenamento e taxa de transferência por contêiner/banco de dados.

| Recurso | Limite padrão |
| --- | --- |
| RUs máximo por contêiner ([modo de taxa de transferência dedicada provisionado](databases-containers-items.md#azure-cosmos-containers)) | 1\.000.000 por padrão. Você pode aumentá-lo por [preenchendo um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nem entrar em contato conosco por meio de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| RUs máximo por banco de dados ([modo de taxa de transferência compartilhada provisionado](databases-containers-items.md#azure-cosmos-containers)) | 1\.000.000 por padrão. Você pode aumentá-lo por [preenchendo um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nem entrar em contato conosco por meio de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| RUs máximo por chave de partição (lógico) | 10.000 |
| Armazenamento máximo entre todos os itens por chave de partição (lógico)| 10 GB |
| Número máximo de chaves de partição distinta de (lógico) | Ilimitado |
| Armazenamento máximo por contêiner | Ilimitado |
| Armazenamento máximo por banco de dados | Ilimitado |

> [!NOTE]
> Para obter práticas recomendadas gerenciar cargas de trabalho que tenham chaves de partição que precisam de limites mais altos para armazenamento ou taxa de transferência, consulte [projetar para chaves de partição ativa](synthetic-partition-keys.md)
>

Um contêiner do Cosmos (ou banco de dados de taxa de transferência compartilhada) deve ter uma taxa de transferência mínima de 400 RUs. À medida que cresce o contêiner, a taxa de transferência mínima com suporte também depende dos seguintes fatores:

* A taxa de transferência máxima já provisionada no contêiner. O serviço dá suporte a taxa de transferência na redução de um contêiner para 10% do máximo provisionado. Por exemplo, se a taxa de transferência foi aumentada para 10000 RUs, em seguida, mais baixa possível taxa de transferência seria 1000 RUs
* O número total de contêineres que você já criou um banco de dados de taxa de transferência compartilhada, medido em 100 RUs por contêiner. Por exemplo, se você tiver criado cinco contêineres dentro de um banco de dados de taxa de transferência compartilhada, em seguida, a taxa de transferência deve ser pelo menos 500 RUs

A taxa de transferência atual e o mínima de um contêiner ou um banco de dados pode ser recuperada do portal do Azure ou os SDKs. Para obter mais informações, consulte [provisionar a produtividade em contêineres e bancos de dados](set-throughput.md). Em resumo, aqui estão os limites de RU provisionados mínimo. 

| Recurso | Limite padrão |
| --- | --- |
| RUs mínimo por contêiner ([modo de taxa de transferência dedicada provisionado](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| RUs mínimo por banco de dados ([modo de taxa de transferência compartilhada provisionado](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| RUs mínimo por contêiner dentro de um banco de dados de taxa de transferência compartilhada | 100 |

O cosmos DB dá suporte a dimensionamento Elástico da taxa de transferência (RUs) por contêiner ou banco de dados por meio do portal ou SDKs. Cada contêiner pode dimensionar de forma síncrona e imediatamente dentro de um intervalo de escala de 10 a 100 vezes, entre os valores mínimos e máximo. Se o valor de taxa de transferência solicitada está fora do intervalo, dimensionamento é executado de forma assíncrona. Dimensionamento assíncrona pode levar minutos a horas para ser concluída dependendo do tamanho do armazenamento de dados no contêiner e produtividade solicitada.  

## <a name="control-plane-operations"></a>Operações do plano de controle

Você pode [provisionar e gerenciar sua conta do Azure Cosmos](how-to-manage-database-account.md) usando o portal do Azure, Azure PowerShell, CLI do Azure e modelos do Azure Resource Manager. A tabela a seguir lista os limites por assinatura, a conta e o número de operações.

| Recurso | Limite padrão |
| --- | --- |
| Contas de banco de dados máximo por assinatura | 50 por padrão. Você pode aumentá-lo por [preenchendo um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nem entrar em contato conosco por meio de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Número máximo de failovers regionais | 1 hora por padrão. Você pode aumentá-lo por [preenchendo um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nem entrar em contato conosco por meio de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> Failovers regionais se aplicam somente a contas de gravações de região única. Contas de gravação de várias regiões não exigem nem têm limites sobre como alterar a região de gravação.

O cosmos DB automaticamente faz backup de seus dados em intervalos regulares. Para obter detalhes sobre os intervalos de retenção de backup e do windows, consulte [restauração do backup Online e dados sob demanda no Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-container-limits"></a>Limites por contêiner

Dependendo de qual API você usar, um contêiner do Azure Cosmos pode representar em uma coleção, uma tabela ou gráfico. Configurações para dar suporte a contêineres [restrições de chave exclusivas](unique-keys.md), [procedimentos armazenados, gatilhos e UDFs](stored-procedures-triggers-udfs.md), e [política de indexação](how-to-manage-indexing-policy.md). A tabela a seguir lista os limites específicos para as configurações de dentro de um contêiner. 

| Recurso | Limite padrão |
| --- | --- |
| Comprimento máximo do nome de contêiner ou banco de dados | 255 |
| Máximo de procedimentos armazenados por contêiner | 100 <sup>*</sup>|
| UDFs máximo por contêiner | 25 <sup>*</sup>|
| Número máximo de caminhos na política de indexação| 100 <sup>*</sup>|
| Número máximo de chaves exclusivas por contêiner|10 <sup>*</sup>|
| Número máximo de caminhos por restrição de chave exclusiva|16 <sup>*</sup>|

<sup>*</sup> É possível aumentar qualquer um desses limites por contêiner, entrando em contato com o suporte do Azure ou entrar em contato conosco por meio [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="per-item-limits"></a>Limites por item

Dependendo de qual API você usar, um item de Cosmos do Azure pode representar a um documento em uma coleção, uma linha em uma tabela, ou um nó ou borda em um gráfico. A tabela a seguir mostra os limites por item no Cosmos DB. 

| Recurso | Limite padrão |
| --- | --- |
| Tamanho máximo de um item | 2 MB (UTF-8 comprimento da representação JSON) |
| Comprimento máximo do valor de chave de partição | 2048 bytes |
| Comprimento máximo do valor de id | 1024 bytes |
| Número máximo de propriedades por item | Nenhum limite prático |
| Profundidade de aninhamento máximo | Nenhum limite prático |
| Comprimento máximo do nome de propriedade | Nenhum limite prático |
| Comprimento máximo do valor da propriedade | Nenhum limite prático |
| Comprimento máximo do valor da propriedade de cadeia de caracteres | Nenhum limite prático |
| Comprimento máximo do valor da propriedade numérico | IEEE754 precisão dupla de 64 bits |

Não há nenhuma restrição sobre as cargas de item, como o número de profundidade de aninhamento e propriedades, exceto para as restrições de comprimento, em geral e valores de id e chave de partição a restrição de 2 MB de tamanho. Talvez você precise configurar a política de indexação para contêineres com estruturas de item de grandes ou complexos para reduzir o consumo de RU. Ver [modelagem de itens no Cosmos DB](how-to-model-partition-example.md) para obter um exemplo do mundo real e padrões para gerenciar itens grandes.

## <a name="per-request-limits"></a>Limites por solicitação

O cosmos DB suporta [operações CRUD e consulta](https://docs.microsoft.com/rest/api/cosmos-db/) em relação a recursos como contêineres, itens e bancos de dados.  

| Recurso | Limite padrão |
| --- | --- |
| Tempo de execução máximo para uma única operação (como uma execução de procedimento armazenado ou uma recuperação de página única consulta)| 5 segundos |
| Tamanho máximo de solicitação (procedimento armazenado, CRUD)| 2 MB |
| Tamanho máximo de resposta (por exemplo, consulta paginada) | 4 MB |

Uma vez uma operação, como consulta atinge o limite de tamanho de resposta ou tempo limite de execução, ele retorna uma página de resultados e um token de continuação para o cliente para retomar a execução. Não há nenhum limite prático na duração de que uma única consulta pode executar em páginas/continuações.

O cosmos DB usa o HMAC para autorização. Você pode usar uma chave mestre, ou um [tokens de recurso](secure-access-to-data.md) para controle de acesso refinado a recursos como contêineres, partição, chaves ou itens. A tabela a seguir lista os limites para tokens de autorização no Cosmos DB.

| Recurso | Limite padrão |
| --- | --- |
| Tempo máximo de expiração do token mestre | 15 min  |
| Tempo de expiração do token de recurso mínima | 10 min  |
| Tempo de expiração do token de recurso máximo | 24 horas por padrão. Você pode aumentá-lo por [preenchendo um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nem entrar em contato conosco por meio de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Distorção máxima do relógio para autorização de token| 15 min |

O cosmos DB dá suporte à execução de gatilhos durante gravações. O serviço suporta um máximo de um pré-gatilho e um pós-gatilho por operação de gravação. 

## <a name="sql-query-limits"></a>Limites de consulta SQL

Suporta o cosmos DB consultar itens usando [SQL](how-to-sql-query.md). A tabela a seguir descreve as restrições nas instruções de consulta, por exemplo em termos de número de cláusulas ou tamanho de consulta.

| Recurso | Limite padrão |
| --- | --- |
| Comprimento máximo de consulta SQL| 256 KB <sup>*</sup>|
| Junções máximas por consulta| 5 <sup>*</sup>|
| ANDs máximas por consulta| 2000 <sup>*</sup>|
| ORs máximas por consulta| 2000 <sup>*</sup>|
| UDFs máximas por consulta| 10 <sup>*</sup>|
| Máximo de argumentos na expressão| 6000 <sup>*</sup>|
| Máximo de pontos por polígono| 4096 <sup>*</sup>|

<sup>*</sup> É possível aumentar qualquer um desses limites de consulta SQL, entrando em contato com o suporte do Azure ou entrar em contato conosco por meio [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="mongodb-api-specific-limits"></a>Limites específicos de API do MongoDB

O cosmos DB suporta o protocolo de transmissão do MongoDB para aplicativos escritos para MongoDB. Você pode localizar os comandos com suporte e versões em de protocolo [recursos de suporte para MongoDB e sintaxe](mongodb-feature-support.md).

A tabela a seguir lista os limites específicos para dar suporte ao recurso do MongoDB. Outros limites de serviço mencionados para o SQL API (principal) também se aplicam à API do MongoDB.

| Recurso | Limite padrão |
| --- | --- |
| Tamanho máximo da memória de consulta do MongoDB | 40 MB |
| Tempo de execução máximo para operações do MongoDB| 30 s |

## <a name="try-cosmos-db-free-limits"></a>Tente limites gratuitos do Cosmos DB

A tabela a seguir lista os limites para o [Experimente gratuitamente o Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) avaliação.

| Recurso | Limite padrão |
| --- | --- |
| Duração do período de avaliação | 30 dias (pode ser renovado a qualquer número de vezes) |
| Máximo de contêiners por assinatura (API de tabela do SQL, Gremlin) | 1 |
| Máximo de contêiners por assinatura (API do MongoDB) | 3 |
| Taxa de transferência máxima por contêiner | 5\.000 |
| Taxa de transferência máxima por banco de dados de taxa de transferência compartilhada | 20000 |
| Máximo de armazenamento total por conta | 10 GB |

Experimente o Cosmos DB dá suporte à distribuição global somente as regiões dos EUA, Europa Setentrional e sudeste asiático. Não não possível criar tíquetes de suporte do Azure para contas Experimente o Azure Cosmos DB. No entanto, o suporte é fornecido para os assinantes com planos de suporte existentes.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre o Cosmos DB principais conceitos [distribuição global](distribute-data-globally.md) e [particionamento](partitioning-overview.md) e [taxa de transferência provisionada](request-units.md).

Comece no Azure Cosmos DB com um dos nossos guias de início rápido:

* [Introdução à API de SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introdução à API do MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
