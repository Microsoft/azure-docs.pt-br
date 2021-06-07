---
title: Cotas de serviço do Azure Cosmos DB
description: Cotas de serviço e limites padrão do Azure Cosmos DB em diferentes tipos de recursos.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: f33490e273104105502ecc8f4f255c354a2d2f3b
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800016"
---
# <a name="azure-cosmos-db-service-quotas"></a>Cotas de serviço do Azure Cosmos DB

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo oferece uma visão geral das cotas padrão oferecidas a diferentes recursos no Azure Cosmos DB.

## <a name="storage-and-database-operations"></a>Operações de armazenamento e banco de dados

Após a criação de uma conta do Azure Cosmos na sua assinatura, é possível gerenciar dados em sua conta [criando bancos de dados, contêineres e itens](account-databases-containers-items.md).

### <a name="provisioned-throughput"></a>Taxa de transferência provisionada

Você pode provisionar a taxa de transferência em um nível de contêiner ou em um nível de banco de dados em termos de [RU/s ou RUs (unidades de solicitação)](request-units.md). A tabela a seguir lista os limites de armazenamento e taxa de transferência por contêiner/banco de dados.

| Recurso | Limite padrão |
| --- | --- |
| Máximo de RUs por contêiner ([modo provisionado de taxa de transferência dedicada](account-databases-containers-items.md#azure-cosmos-containers)) | 1 milhão por padrão. Você pode aumentar isso [abrindo um tíquete de Suporte do Azure](create-support-request-quota-increase.md) |
| Máximo de RUs por banco de dados ([modo provisionado de taxa de transferência compartilhada](account-databases-containers-items.md#azure-cosmos-containers)) | 1 milhão por padrão. Você pode aumentar isso [abrindo um tíquete de Suporte do Azure](create-support-request-quota-increase.md) |
| RUs máxima por partição (lógica) | 10.000 |
| Armazenamento máximo em todos os itens por partição (lógica) | 20 GB |
| Número máximo de chaves de partição (lógica) distintas | Ilimitado |
| Armazenamento máximo por contêiner | Ilimitado |
| Armazenamento máximo por banco de dados | Ilimitado |
| Tamanho máximo de anexo por conta (o recurso de anexo está sendo preterido) | 2 GB |
| Mínimo de RU/s exigidos por 1 GB | 10 RU/s<br>**Observação:** esse mínimo pode ser reduzido se sua conta estiver qualificada para o [programa "alto armazenamento/baixa taxa de transferência"](set-throughput.md#high-storage-low-throughput-program) |

> [!NOTE]
> Para saber mais sobre as práticas recomendadas para o gerenciamento de cargas de trabalho que têm chaves de partição que exigem limites mais altos para armazenamento ou taxa de transferência, confira [Criar uma chave de partição sintética](synthetic-partition-keys.md).

### <a name="minimum-throughput-limits"></a>Limites de taxa de transferência mínima

Um contêiner do Cosmos (ou banco de dados de produtividade compartilhado) deve ter uma taxa de transferência mínima de 400 RU/s. À medida que o contêiner cresce, Cosmos DB requer uma taxa de transferência mínima para garantir que o banco de dados ou o contêiner tenha recursos suficientes para suas operações.

A taxa de transferência atual e mínima de um contêiner ou de um banco de dados pode ser recuperada do portal do Azure ou dos SDKs. Para obter mais informações, confira [Provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md). 

O mínimo de RU/s real pode variar dependendo da configuração da sua conta. Você pode usar [Azure monitor métricas](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) para exibir o histórico de taxa de transferência provisionada (ru/s) e o armazenamento em um recurso. 

#### <a name="minimum-throughput-on-container"></a>Taxa de transferência mínima no contêiner 

Para estimar a taxa de transferência mínima necessária de um contêiner com taxa de transferência manual, encontre o máximo de:

* 400 RU/s 
* Armazenamento atual em GB * 10 RU/s
* RU/s mais alto provisionado no contêiner/100

Exemplo: Suponha que você tenha um contêiner provisionado com o armazenamento de 400 RU/s e 0 GB. Você aumenta a taxa de transferência para 50.000 RU/s e importa 20 GB de dados. O mínimo de RU/s agora é `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500 ru/s. Ao longo do tempo, o armazenamento aumenta para 200 GB. O mínimo de RU/s agora é `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000 ru/s. 

**Observação:** a taxa de transferência mínima de 10 ru/s por GB de armazenamento poderá ser reduzida se sua conta estiver qualificada para o nosso [programa de "alta taxa de armazenamento/baixa produtividade"](set-throughput.md#high-storage-low-throughput-program).

#### <a name="minimum-throughput-on-shared-throughput-database"></a>Taxa de transferência mínima no banco de dados de taxa de transferência compartilhada 
Para estimar a taxa de transferência mínima necessária de um banco de dados de taxa de transferência compartilhado com taxa de transferência manual, encontre o máximo de:

* 400 RU/s 
* Armazenamento atual em GB * 10 RU/s
* RU/s mais alto provisionado no banco de dados/100
* 400 + MAX (contagem de contêineres-25, 0) * 100 RU/s

Exemplo: Suponha que você tenha um banco de dados provisionado com 400 RU/s, 15 GB de armazenamento e 10 contêineres. O mínimo de RU/s é `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400 ru/s. Se houver 30 contêineres no banco de dados, o RU/s mínimo seria `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 ru/s. 

**Observação:** a taxa de transferência mínima de 10 ru/s por GB de armazenamento poderá ser reduzida se sua conta estiver qualificada para o nosso [programa de "alta taxa de armazenamento/baixa produtividade"](set-throughput.md#high-storage-low-throughput-program).

Em resumo, aqui estão os limites mínimos de RU provisionadas. 

| Recurso | Limite padrão |
| --- | --- |
| RUs mínima por contêiner ([modo de taxa de transferência dedicada](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 |
| RUs mínima por banco de dados ([modo provisionado de taxa de transferência compartilhada](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 RU/s para os primeiros 25 contêineres. 100 RU/s adicionais para cada contêiner posteriormente. |

O Cosmos DB dá suporte ao dimensionamento programático de taxa de transferência (RU/s) por contêiner ou banco de dados por meio de SDKs ou Portal.    

Dependendo das configurações atuais de configuração e de corrente de RU/s, cada recurso pode ser dimensionado de forma síncrona e imediatamente entre o mínimo de RU/s até 100x o mínimo de RU/s. Se o valor da taxa de transferência solicitado estiver fora do intervalo, a colocação em escala será executada de modo assíncrono. A colocação em escala assíncrona pode levar de minutos a horas para ser concluída, dependendo da taxa de transferência e do tamanho de armazenamento de dados solicitados no contêiner.  

### <a name="serverless"></a>Sem servidor

O sem [servidor](serverless.md) permite que você use seus recursos de Azure Cosmos DB de uma maneira baseada em consumo. A tabela a seguir lista os limites para armazenamento e intermitência de taxa de transferência por contêiner/banco de dados.

| Recurso | Limite |
| --- | --- |
| Máximo de RU/s por partição (lógica) | 5\.000 |
| Armazenamento máximo em todos os itens por partição (lógica) | 20 GB |
| Número máximo de chaves de partição (lógica) distintas | Ilimitado |
| Armazenamento máximo por contêiner | 50 GB |

## <a name="control-plane-operations"></a>Operações de plano de controle

Você pode [provisionar e gerenciar sua conta do Azure Cosmos](how-to-manage-database-account.md) usando os modelos portal do Azure, Azure PowerShell, CLI do Azure e Azure Resource Manager. A tabela a seguir lista os limites por assinatura, conta e número de operações.

| Recurso | Limite padrão |
| --- | --- |
| Máximo de contas do banco de dados por assinatura | 50 por padrão. Você pode aumentar isso [abrindo um tíquete de Suporte do Azure](create-support-request-quota-increase.md)|
| Número máximo de failovers regionais | 1/hora por padrão. Você pode aumentar isso [abrindo um tíquete de suporte do Azure](create-support-request-quota-increase.md)|

> [!NOTE]
> Failovers regionais só se aplicam a contas de gravações de região única. As contas de gravação de várias regiões não exigem nem têm limites para alterar a região de gravação.

O Cosmos DB faz backups automáticos de seus dados em intervalos regulares. Para obter detalhes sobre intervalos de retenção e janelas de backup, confira [Backup online e restauração de dados sob demanda no Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limites por conta

### <a name="provisioned-throughput"></a>Taxa de transferência provisionada

| Recurso | Limite padrão |
| --- | --- |
| Número máximo de bancos de dados | Ilimitado |
| Número máximo de contêineres por banco de dados com taxa de transferência compartilhada |25 |
| Número máximo de contêineres por banco de dados ou conta com taxa de transferência dedicada  |ilimitado |
| Número máximo de regiões | Sem limite (todas as regiões do Azure) |

### <a name="serverless"></a>Sem servidor

| Recurso | Limite |
| --- | --- |
| Número máximo de bancos de dados | Ilimitado |
| Número máximo de contêineres por conta  | 100 |
| Número máximo de regiões | 1 (qualquer região do Azure) |

## <a name="per-container-limits"></a>Limites por contêiner

Dependendo da API que você usa, um contêiner Cosmos do Azure pode representar uma coleção, uma tabela ou um grafo. Os contêineres dão suporte a configurações para [restrições de chave exclusivas](unique-keys.md), [procedimentos armazenados, gatilhos e UDFs](stored-procedures-triggers-udfs.md) e [política de indexação](how-to-manage-indexing-policy.md). A tabela a seguir lista os limites específicos para as configurações dentro de um contêiner. 

| Recurso | Limite padrão |
| --- | --- |
| Comprimento máximo do banco de dados ou nome do contêiner | 255 |
| Máximo de procedimentos armazenados por contêiner | 100 <sup>*</sup>|
| Máximo de UDFs por contêiner | 50 <sup>*</sup>|
| Número máximo de caminhos na política de indexação| 100 <sup>*</sup>|
| Número máximo de chaves exclusivas por contêiner|10 <sup>*</sup>|
| Número máximo de caminhos por restrição de chave exclusiva|16 <sup>*</sup>|
| Valor de TTL máximo |2147483647|

<sup>*</sup> Você pode aumentar qualquer um desses limites por contêiner criando uma [solicitação de suporte do Azure](create-support-request-quota-increase.md).

## <a name="per-item-limits"></a>Limites por item

Dependendo da API que você usa, um item do Azure Cosmos pode representar um documento em uma coleção, uma linha em uma tabela ou um nó ou borda em um grafo. A tabela a seguir mostra os limites por item no Cosmos DB. 

| Recurso | Limite padrão |
| --- | --- |
| Tamanho máximo de um item | 2 MB (comprimento de UTF-8 da representação JSON) |
| Comprimento máximo do valor de chave de partição | 2\.048 bytes |
| Comprimento máximo do valor da ID | 1\.023 bytes |
| Número máximo de propriedades por item | Sem limite prático |
| Comprimento máximo do nome da propriedade | Sem limite prático |
| Comprimento máximo do valor da propriedade | Sem limite prático |
| Comprimento máximo do valor da propriedade da cadeia de caracteres | Sem limite prático |
| Comprimento máximo do valor da propriedade numérica | IEEE754 de precisão dupla de 64 bits |
| Nível máximo de aninhamento para objetos/matrizes inseridos | 128 |
| Valor de TTL máximo |2147483647|

Não há restrições sobre as cargas de item, como o número de propriedades e a profundidade de aninhamento, exceto as restrições de comprimento nos valores de ID e chave de partição e a restrição de tamanho geral de 2 MB. Talvez seja necessário configurar a política de indexação para contêineres com estruturas de item grandes ou complexas para reduzir o consumo de RU. Confira [Modelar itens no Cosmos DB](how-to-model-partition-example.md) para obter um exemplo real e padrões para gerenciar itens grandes.

## <a name="per-request-limits"></a>Limites por solicitação

O Azure Cosmos DB dá suporte a [operações CRUD e de consulta](/rest/api/cosmos-db/) em relação a recursos como contêineres, itens e bancos de dados. Ele também dá suporte a [solicitações de lote transacionais](/dotnet/api/microsoft.azure.cosmos.transactionalbatch) em vários itens com a mesma chave de partição em um contêiner.

| Recurso | Limite padrão |
| --- | --- |
| Tempo máximo de execução para uma operação (como uma execução de procedimento armazenado ou uma recuperação de página de consulta)| 5 segundos |
| Tamanho máximo da solicitação (por exemplo, procedimento armazenado, CRUD)| 2 MB |
| Tamanho máximo da resposta (por exemplo, consulta paginada) | 4 MB |
| Número máximo de operações em um lote transacional | 100 |

Quando uma operação como consulta atinge o tempo limite de execução ou o limite de tamanho de resposta, ela retorna uma página de resultados e um token de continuação para o cliente para retomar a execução. Não há nenhum limite prático de duração em que uma consulta possa ser executada em páginas/continuações.

O Cosmos DB usa HMAC para autorização. Você pode usar uma chave primária ou [tokens de recurso](secure-access-to-data.md) para controle de acesso refinado a recursos como contêineres, chaves de partição ou itens. A tabela a seguir lista os limites para tokens de autorização no Cosmos DB.

| Recurso | Limite padrão |
| --- | --- |
| Tempo máximo de expiração do token primário | 15 min  |
| Tempo de expiração do token de recurso mínimo | 10 min  |
| Tempo de expiração do token de recurso máximo | 24 h por padrão. Você pode aumentar isso [abrindo um tíquete de Suporte do Azure](create-support-request-quota-increase.md)|
| Distorção máxima de relógio para autorização de token| 15 min |

O Cosmos DB dá suporte à execução de gatilhos durante as gravações. O serviço dá suporte a um máximo de um pré-gatilho e um pós-gatilho por operação de gravação.

## <a name="metadata-request-limits"></a>Limites de solicitação de metadados

Azure Cosmos DB mantém metadados do sistema para cada conta. Esses metadados permitem que você enumere coleções, bancos de dados, outros recursos Azure Cosmos DB e suas configurações gratuitamente.

| Recurso | Limite padrão |
| --- | --- |
|Taxa máxima de criação de coleção por minuto|    100|
|Taxa máxima de criação de banco de dados por minuto|    100|
|Taxa máxima de atualização de produtividade provisionada por minuto|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>Limites para a taxa de transferência provisionada de dimensionamento automático

Confira o artigo [Dimensionamento automático](provision-throughput-autoscale.md#autoscale-limits) e [Perguntas Frequentes](autoscale-faq.md#lowering-the-max-rus) para obter uma explicação mais detalhada dos limites de taxa de transferência e armazenamento com dimensionamento automático.

| Recurso | Limite padrão |
| --- | --- |
| Máximo de RU/s para as quais que o sistema pode dimensionar |  `Tmax`, o máximo de RU/s de dimensionamento automático definido pelo usuário|
| Mínimo de RU/s para as quais que o sistema pode dimensionar | `0.1 * Tmax`|
| RU/s atual para a qual o sistema é dimensionado  |  `0.1*Tmax <= T <= Tmax`, com base no uso|
| Mínimo de RU/s faturáveis por hora| `0.1 * Tmax` <br></br>A cobrança é feita por hora, em que você é cobrado pela RU/s mais alta para a qual o sistema está dimensionado dentro da hora (ou `0.1*Tmax`), o que for maior. |
| Dimensionamento automático máximo de RU/s para um contêiner  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` arredondado para as 1.000 RU/s mais próximas |
| Dimensionamento automático máximo de RU/s para um banco de dados  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))` arredondado para as 1.000 RU/s mais próximas. <br></br>Observação: se o banco de dados tiver mais de 25 contêineres, o sistema incrementará o dimensionamento automático mínimo do máximo de RU/s por 1 mil RU/s por contêiner adicional. Por exemplo, se você tiver 30 contêineres, o menor dimensionamento automático máximo de RU/s que você poderá definir será de 9 mil RU/s (pode dimensionado entre 900 – 9.000 RU/s).

## <a name="sql-query-limits"></a>Limites de consulta SQL

O Cosmos DB dá suporte à consulta de itens usando [SQL](./sql-query-getting-started.md). A tabela a seguir descreve as restrições nas instruções de consulta, por exemplo, em termos de número de cláusulas ou comprimento de consulta.

| Recurso | Limite padrão |
| --- | --- |
| Comprimento máximo da consulta SQL| 256 KB |
| Máximo de JUNÇÕES por consulta| 5 <sup>*</sup>|
| Máximo de UDFs por consulta| 10 <sup>*</sup>|
| Máximo de pontos por polígono| 4096 |
| Máximo de caminhos incluídos por contêiner| 500 |
| Máximo de caminhos excluídos por contêiner| 500 |
| Máximo de propriedades em um índice composto| 8 |

<sup>*</sup> Você pode aumentar qualquer um desses limites de consulta SQL criando uma [solicitação de suporte do Azure](create-support-request-quota-increase.md).

## <a name="mongodb-api-specific-limits"></a>Limites específicos da API do MongoDB

O Cosmos DB dá suporte ao protocolo de transmissão do MongoDB para aplicativos escritos no MongoDB. Você pode encontrar os comandos e as versões de protocolo com suporte em [Recursos e sintaxe compatíveis do MongoDB](mongodb-feature-support.md).

A tabela a seguir lista os limites específicos para o suporte a recursos do MongoDB. Outros limites de serviço mencionados para a API do SQL (núcleo) também se aplicam à API do MongoDB.

| Recurso | Limite padrão |
| --- | --- |
| Tamanho máximo de memória de consulta do MongoDB (essa limitação é apenas para a versão do servidor 3.2) | 40 MB |
|Tempo máximo de execução para operações do MongoDB (para a versão 3,2 do servidor)| 15 s|
|Tempo máximo de execução para operações do MongoDB (para a versão 3,6 do servidor)| 60 segundos|
| Tempo limite de conexão ociosa para encerramento de conexão no lado do servidor* | 30 minutos |

\* Recomendamos que os aplicativos cliente definam o tempo limite de conexão ociosa nas configurações do driver para 2-3 minutos porque o [tempo limite padrão para o Azure Load Balancer é de 4 minutos](../load-balancer/load-balancer-tcp-idle-timeout.md).  Esse tempo limite garantirá que as conexões ociosas não sejam fechadas por um balanceador de carga intermediário entre o computador cliente e o Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Limites da avaliação gratuita do Cosmos DB

A tabela a seguir lista os limites para o [Avaliar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/).

| Recurso | Limite padrão |
| --- | --- |
| A duração da avaliação | 30 dias (uma nova avaliação pode ser solicitada após o término) <br> Depois do término, as informações armazenadas são excluídas. |
| Máximo de contêineres por assinatura (SQL, Gremlin, API de Tabela) | 1 |
| Máximo de contêineres por assinatura (API do MongoDB) | 3 |
| Taxa de transferência máxima por contêiner | 5.000 |
| Taxa de transferência máxima por banco de dados de taxa de transferência compartilhada | 20000 |
| Máximo de armazenamento total por conta | 10 GB |

A avaliação do Cosmos DB dá suporte à distribuição global somente nas regiões EUA Central, Norte da Europa e Sudeste da Ásia. Tíquetes de Suporte do Azure não podem ser criados para contas de avaliação do Azure Cosmos DB. No entanto, o suporte é fornecido para assinantes com planos de suporte existentes.

## <a name="azure-cosmos-db-free-tier-account-limits"></a>Azure Cosmos DB limites de conta de camada gratuita

A tabela a seguir lista os limites para as [contas de camada gratuita do Azure Cosmos DB.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Recurso | Limite padrão |
| --- | --- |
| Número de contas de camada gratuita por assinatura do Azure | 1 |
| Duração do desconto por nível gratuito | Tempo de vida da conta. Deve aceitar durante a criação da conta. |
| Máximo de RU/s gratuitas | 400 RU/s |
| Armazenamento máximo gratuito | 5 GB |
| Número máximo de bancos de dados de taxa de transferência compartilhados | 5 |
| Número máximo de contêineres em um banco de dados de taxa de transferência compartilhado | 25 <br>Em contas de camada gratuita, o mínimo de RU/s para um banco de dados de taxa de transferência compartilhado com até 25 contêineres é 400 RU/s. |

Além do acima, os [limites por conta](#per-account-limits) também se aplicam a contas de camada gratuita.

> [!NOTE]
> Azure Cosmos DB camada gratuita é diferente da conta gratuita do Azure. A conta gratuita do Azure oferece créditos e recursos do Azure gratuitamente por um período limitado. Ao usar Azure Cosmos DB como parte desta conta gratuita, você obtém armazenamento de 25 GB e 400 RU/s de taxa de transferência provisionada por 12 meses.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre a [distribuição global](distribute-data-globally.md) dos conceitos básicos do Cosmos DB, bem como de [particionamento](partitioning-overview.md) e de [taxa de transferência provisionada](request-units.md).

Comece no Azure Cosmos DB com um dos nossos guias de início rápido:

* [Introdução à API de SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introdução à API do MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
