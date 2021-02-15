---
title: FAQ de hiperescala do banco de dados SQL do Azure
description: Respostas a perguntas comuns que os clientes perguntam sobre um banco de dados no banco de dados SQL na camada de serviço de hiperescala – normalmente chamado de banco de dados de hiperescala.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 02/03/2021
ms.openlocfilehash: c6faa1703b0935d66d291281f33027b3a66a59d4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526815"
---
# <a name="azure-sql-database-hyperscale-faq"></a>FAQ de hiperescala do banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece respostas para as perguntas frequentes para os clientes que consideram um banco de dados na camada de serviço de hiperescala do banco de dados SQL do Azure, conhecida como apenas hiperescala no restante das perguntas frequentes. Este artigo descreve os cenários que o hiperscale dá suporte e os recursos que são compatíveis com o hiperscale.

- Esta FAQ destina-se a leitores que tenham uma breve compreensão do nível de serviço Hiperescala e que desejam ter suas dúvidas e preocupações específicas respondidas.
- Essas perguntas frequentes não devem ser uma Guidebook ou responder a perguntas sobre como usar um banco de dados de hiperescala. Para obter uma introdução ao hiperescala, recomendamos que você consulte a documentação do [hiperescala do banco de dados SQL do Azure](service-tier-hyperscale.md) .

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-a-hyperscale-database"></a>O que é um banco de dados Hiperescala?

Um banco de dados de hiperescala é um banco de dados no banco de dados SQL na camada de serviço de hiperescala que é apoiada pela tecnologia de armazenamento de escala horizontal de hiperescala. Um banco de dados Hiperescala suporta até 100 TB de dados e oferece alto rendimento e desempenho, bem como escalonamento rápido para se adaptar aos requisitos de carga de trabalho. O dimensionamento é transparente para o aplicativo – conectividade, processamento de consulta, etc., funciona como qualquer outro banco de dados no banco de dados SQL do Azure.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quais tipos de recursos e modelos de compra suportam a Hiperescala?

A camada de serviço Hiperescala está disponível apenas para bancos de dados individuais usando o modelo de compra baseado em vCore no Banco de Dados SQL do Azure.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Como o nível de serviço da Hiperescala difere dos níveis de serviço de uso geral e de negócios críticos?

As camadas de serviço baseadas em vCore são diferenciadas com base na disponibilidade do banco de dados e no tipo de armazenamento, desempenho e tamanho máximo, conforme descrito na tabela a seguir.

| | Tipo de recurso | Uso Geral |  Hiperescala | Comercialmente Crítico |
|:---:|:---:|:---:|:---:|:---:|
| **Mais adequado para** |Tudo|Oferece opções equilibradas de computação e armazenamento orientadas ao orçamento.|A maioria das cargas de trabalho comerciais. Dimensionamento automático de tamanho de armazenamento de até 100 TB, dimensionamento rápido vertical e horizontal de computação, restauração rápida de banco de dados.|Aplicativos OLTP com alta taxa de transação e baixa latência de e/s. Oferece maior resiliência a falhas e failovers rápidos usando várias réplicas atualizadas de forma síncrona.|
|  **Tipo de recurso** ||Banco de dados SQL/SQL Instância Gerenciada | Banco de dados individual | Banco de dados SQL/SQL Instância Gerenciada |
| **Tamanho da computação**|Banco de dados SQL * | 1 a 80 vCores | 1 a 80 vCores * | 1 a 80 vCores |
| **Tamanho da computação**|Instância Gerenciada de SQL | 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Tipo de armazenamento** | Tudo |Armazenamento remoto Premium (por instância) | Armazenamento desacoplado com cache SSD local (por instância) | Armazenamento SSD local super rápido (por instância) |
| **Tamanho de armazenamento** | Banco de dados SQL *| 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| **Tamanho de armazenamento** | Instância Gerenciada de SQL  | 32 GB A 8 TB | N/D | 32 GB – 4 TB |
| **IOPS** | Banco de dados individual | 500 IOPS por vCore com máximo de 7.000 IOPS | O hiperscale é uma arquitetura de várias camadas com cache em vários níveis. O IOPS efetivo dependerá da carga de trabalho. | 5000 IOPS com 200.000 IOPS máximo|
| **IOPS** | Instância Gerenciada de SQL | Depende do tamanho do arquivo | N/D | 1375 IOPS/vCore |
|**Disponibilidade**|Tudo|1 réplica, sem escala de leitura, sem cache local | Várias réplicas, até 4 expansão de leitura, cache local parcial | 3 réplicas, 1 expansão de leitura, HA com redundância de zona, armazenamento local completo |
|**Backups**|Tudo|RA-GRS, 7-35 dias de retenção (7 dias por padrão)| RA-GRS, 7 dias de retenção, PITR (tempo constante de recuperação pontual) | RA-GRS, 7-35 dias de retenção (7 dias por padrão) |

\* Não há suporte para pools elásticos na camada de serviço de hiperescala

### <a name="who-should-use-the-hyperscale-service-tier"></a>Quem deve usar o nível de serviço Hiperescala

A camada de serviço de hiperescala destina-se a clientes que têm grandes bancos de dados de SQL Server locais e querem modernizar seus aplicativos ao migrar para a nuvem ou para clientes que já estão usando o banco de dados SQL do Azure e que desejam expandir significativamente o potencial para o crescimento do banco de dados. A Hiperescala também é destinado a clientes que buscam alto desempenho e alta escalabilidade. Com em Hiperescala, você obtém:

- Tamanho do banco de dados de até 100 TB
- Backups rápidos de banco de dados, independentemente do tamanho do banco de dados (backups são baseados em instantâneos de armazenamento)
- Restaurações rápidas de banco de dados, independentemente do tamanho do banco de dados (as restaurações são de instantâneos de armazenamento)
- Maior taxa de transferência de log, independentemente do tamanho do banco de dados e do número de vCores
- Escala horizontal de leitura usando uma ou mais réplicas somente leitura, usadas para descarregamento de leitura e em esperas ativas.
- Escalabilidade rápida de computação, em tempo constante, para ser mais poderosa para acomodar a carga de trabalho pesada e, em seguida, reduzir a escala, em tempo constante. Isso é semelhante a escalar verticalmente entre uma P6 e uma P11, por exemplo, mas muito mais rápido, pois esse não é um tamanho de operação de dados.

### <a name="what-regions-currently-support-hyperscale"></a>Quais regiões atualmente dão suporte em Hiperescala

A camada de serviço de hiperescala está disponível atualmente nas regiões listadas em [visão geral do hiperescala do banco de dados SQL do Azure](service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>Posso criar vários bancos de dados de hiperescala por servidor

Sim. Para obter mais informações e limites sobre o número de bancos de dados de hiperescala por servidor, consulte [limites de recursos do banco de dados SQL para bancos de dados individuais e em pool em um servidor](resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quais são as características de desempenho de um banco de dados de hiperescala

A arquitetura de hiperescala fornece alto desempenho e taxa de transferência ao dar suporte a tamanhos de bancos de dados grandes.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>O que é a escalabilidade de um banco de dados em Hiperescala

O hiperscale fornece escalabilidade rápida com base em sua demanda de carga de trabalho.

- **Dimensionamento para cima/para baixo**

  Com o hiperescala, você pode escalar verticalmente o tamanho da computação primária em termos de recursos, como CPU e memória, e, em seguida, reduzir verticalmente, em tempo constante. Como o armazenamento é compartilhado, a ampliação e o dimensionamento não são um tamanho de operação de dados.  
- **Dimensionamento In/Out**

  Com o hiperescala, você também obtém a capacidade de provisionar uma ou mais réplicas de computação adicionais que você pode usar para atender às suas solicitações de leitura. Isso significa que você pode usar essas réplicas de computação adicionais como réplicas somente leitura para descarregar a carga de trabalho de leitura da computação primária. Além de somente leitura, essas réplicas também servem como Hot-standbys no caso de um failover do primário.

  O provisionamento de cada uma dessas réplicas de computação adicionais pode ser feito em tempo constante e é uma operação online. Você pode se conectar a essas réplicas de computação somente leitura, definindo o `ApplicationIntent` argumento na cadeia de conexão como `ReadOnly` . Todas as conexões com a `ReadOnly` tentativa de aplicativo são automaticamente roteadas para uma das réplicas de computação somente leitura adicionais.

## <a name="deep-dive-questions"></a>Perguntas de aprofundamento

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>Posso misturar o hiperescala e os bancos de dados únicos em um único servidor

Sim, você pode.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>O da Hiperescala requer que meu modelo de programação de aplicativo mude

Não, seu modelo de programação de aplicativo permanece como está. Você usa a cadeia de conexão como de costume e as outras maneiras normais de interagir com seu banco de dados de hiperescala.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Qual nível de isolamento da transação é o padrão em um banco de dados de hiperescala

Na réplica primária, o nível de isolamento da transação padrão é RCSI (isolamento de instantâneo de leitura comprometido). Nas réplicas secundárias de expansão de leitura, o nível de isolamento padrão é instantâneo.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Posso colocar minha licença de SQL Server local ou IaaS em hiperescala

Sim, [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) está disponível para em Hiperescala. Cada núcleo do SQL Server Standard pode mapear para 1 Hiperescala vCores. Cada núcleo do SQL Server Enterprise pode mapear para 4 vCores de Hiperescala. Você não precisa de uma licença de SQL para réplicas secundárias. O preço de Benefício Híbrido do Azure será aplicado automaticamente às réplicas de escala horizontal de leitura (secundária).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Para qual tipo de carga de trabalho a hiperescala é projetada

O hiperscale dá suporte a todas as cargas de trabalho de SQL Server, mas é essencialmente otimizado para OLTP. Você também pode colocar cargas de trabalho híbridas (HTAP) e analíticas (data mart).

### <a name="how-can-i-choose-between-azure-synapse-analytics-and-azure-sql-database-hyperscale"></a>Como escolher entre a análise de Synapse do Azure e a hiperescala do banco de dados SQL do Azure

Se você estiver executando consultas de análise interativas usando SQL Server como um data warehouse, o hiperescala é uma ótima opção, pois você pode hospedar data warehouses de pequeno e médio porte (como alguns TB de até 100 TB) a um custo mais baixo e migrar suas cargas de trabalho de SQL Server data warehouse para hiperescala com alterações mínimas de código T-SQL.

Se você estiver executando a análise de dados em uma grande escala com consultas complexas e taxas de ingestão sustentadas maiores que 100 MB/s ou usando o PDW (Parallel data warehouse), Teradata ou outros data warehouses de processamento paralelo (MPP), o Azure Synapse Analytics poderá ser a melhor opção.
  
## <a name="hyperscale-compute-questions"></a>Perguntas de computação de hiperescala

### <a name="can-i-pause-my-compute-at-any-time"></a>Posso pausar meu computação a qualquer momento

Não no momento, no entanto, você pode dimensionar a computação e o número de réplicas para reduzir o custo fora do horário de pico.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Posso provisionar uma réplica de computação com RAM extra para minha carga de trabalho com uso intensivo de memória

Não. Para obter mais RAM, você precisa atualizar para um tamanho da computação maior. Para obter mais informações, consulte [Armazenamento hiper escale e tamanhos da computação](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Posso provisionar várias réplicas de computação de tamanhos diferentes

Não.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Quantas réplicas de expansão de leitura têm suporte

Os bancos de dados de hiperescala são criados com uma réplica de escala horizontal de leitura (duas réplicas, incluindo a primária) por padrão. Você pode dimensionar o número de réplicas somente leitura entre 0 e 4 usando o [portal do Azure](https://portal.azure.com) ou a [API REST](/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Para alta disponibilidade, preciso provisionar réplicas de computação adicionais

Em bancos de dados de hiperescalas, a resiliência é fornecida no nível de armazenamento. Você só precisa de uma réplica para fornecer resiliência. Quando a réplica de cálculo está inativa, uma nova réplica é criada automaticamente sem perda de dados.

No entanto, se houver apenas uma réplica, poderá levar algum tempo para criar o cache local na nova réplica após o failover. Durante a fase de recompilação do cache, o banco de dados busca o dado diretamente dos servidores de página, resultando em maior latência de armazenamento e desempenho de consulta degradado.

Para aplicativos de missão crítica que exigem alta disponibilidade com impacto mínimo sobre o failover, você deve provisionar pelo menos duas réplicas de computação, incluindo a réplica de computação primária. Essa é a configuração padrão. Dessa forma, há uma réplica hot-standby disponível que funciona como um destino de failover.

## <a name="data-size-and-storage-questions"></a>Perguntas sobre o tamanho e o armazenamento de dados

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Qual é o tamanho máximo de banco de dados com suporte com o hiperscale

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Qual é o tamanho do log de transações com a Hiperescala

O log de transações com a Hiperescala é praticamente infinito. Você não precisa se preocupar com a falta de espaço de log em um sistema que tenha um alto throughput de log. No entanto, a taxa de geração de log pode ser limitada para cargas de trabalho de gravação agressivamente contínuas. A taxa de geração de log sustentada de pico é de 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Minha `tempdb` escala conforme meu banco de dados cresce

Seu `tempdb` banco de dados está localizado no armazenamento SSD local e é dimensionado proporcionalmente ao tamanho de computação que você provisiona. Seu `tempdb` é otimizado para fornecer benefícios máximos de desempenho. `tempdb` o tamanho não é configurável e é gerenciado para você.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>O tamanho do meu banco de dados aumenta automaticamente ou preciso gerenciar o tamanho dos arquivos

O tamanho do seu banco de dados aumenta automaticamente à medida que você insere / ingressa mais dados.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Qual é o menor tamanho de banco de dados que o hiperscale dá suporte ou começa com

40 GB. Um banco de dados de hiperescala é criado com um tamanho inicial de 10 GB. Em seguida, ele começa crescendo 10 GB a cada 10 minutos até atingir o tamanho de 40 GB. Cada uma dessas partes de 10 GB é alocada em um servidor de página diferente para fornecer mais IOPS e um paralelismo de e/s maior. Por causa dessa otimização, mesmo se você escolher o tamanho inicial do banco de dados menor que 40 GB, o banco de dados aumentará para pelo menos 40 GB automaticamente.

### <a name="in-what-increments-does-my-database-size-grow"></a>Em que incrementos o tamanho do meu banco de dados aumenta?

Cada arquivo de dados aumenta em 10 GB. Vários arquivos de dados podem crescer ao mesmo tempo.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>O armazenamento está em hiperescala local ou remoto

Na Hiperescala, os arquivos de dados são armazenados no armazenamento padrão do Azure. Os dados são totalmente armazenados em cache no armazenamento SSD local, em servidores de páginas próximos às réplicas de computação. Além disso, as réplicas de computação têm caches de dados no SSD local e na memória, para reduzir a frequência de busca de dados de servidores de páginas remotas.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Posso gerenciar ou definir arquivos ou grupos de arquivos com a Hiperescala

Não. Os arquivos de dados são adicionados automaticamente. Os motivos comuns para a criação de grupos de fileadicionais não se aplicam à arquitetura de armazenamento em hiperescala.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Posso provisionar um limite rígido no crescimento de dados para meu banco de dados

Não.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Como os arquivos de dados são dispostos com hiperescala

Os arquivos de dados são controlados por servidores de página, com um servidor de página por arquivo de dados. Conforme aumenta o tamanho dos dados, os arquivos de dados e os servidores de página associados são adicionados.

### <a name="is-database-shrink-supported"></a>A redução do banco de dados é suportada

Não.

### <a name="is-data-compression-supported"></a>A compactação de dados é suportada

Sim, incluindo a compactação de linha, página e columnstore.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Se eu tiver uma tabela enorme, os dados da minha tabela serão espalhados por vários arquivos de dados

Sim. As páginas de dados associadas a uma determinada tabela podem acabar em vários arquivos de dados, que fazem parte do mesmo grupo de arquivos. SQL Server usa a [estratégia de preenchimento proporcional](/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) para distribuir dados em arquivos de dados.

## <a name="data-migration-questions"></a>Questões de migração de dados

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>Posso mover meus bancos de dados existentes no banco de dados SQL do Azure para a camada de serviço de hiperescala

Sim. Você pode mover seus bancos de dados existentes para o Azure SQL para o subdimensionamento. Essa é uma migração unidirecional. Você não pode mover bancos de dados da Hiperescala para outro nível de serviço. Para provas de conceito (POCs), recomendamos que você faça uma cópia do seu banco de dados e migre a cópia para o hiperescala. 

O tempo necessário para mover um banco de dados existente para o hiperescala consiste no tempo para copiar os dados e no tempo para repetir as alterações feitas no banco de dados de origem enquanto copiava. O tempo de cópia de dados é proporcional ao tamanho dos dados. O tempo de repetição das alterações será menor se a movimentação for feita durante um período de baixa atividade de gravação.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Posso mover meus bancos de dados de hiperescala para outras camadas de serviço

Não. Neste momento, você não pode mover um banco de dados de hiperescala para outra camada de serviço.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perco alguma funcionalidade ou recursos após a migração para a camada de serviço Hiperescala

Sim. Alguns recursos do banco de dados SQL do Azure ainda não são compatíveis com o hiperscale, incluindo, entre outros, a retenção de backup de longo prazo. Depois de migrar seus bancos de dados para o Hiperescala, esses recursos param de funcionar.  Esperamos que essas limitações sejam temporárias.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Posso mover meu banco de dados local SQL Server ou meu banco de dados SQL Server em uma máquina virtual de nuvem para o subdimensionamento

Sim. Você pode usar todas as tecnologias de migração existentes para migrar para o hiperescala, incluindo a replicação transacional e quaisquer outras tecnologias de movimentação de dados (cópia em massa, Azure Data Factory, Azure Databricks, SSIS). Consulte também o [serviço de migração de banco de dados do Azure](../../dms/dms-overview.md), que dá suporte a vários cenários de migração.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Qual é o meu tempo de inatividade durante a migração de um ambiente de máquina virtual ou local para o hiperescala e como posso minimizá-lo

O tempo de inatividade para a migração para o hiperescala é o mesmo que o tempo de inatividade quando você migra seus bancos de dados para outras camadas de serviço do Azure SQL Database. Você pode usar [replicação transacional](replication-to-sql-database.md#data-migration-scenario
) para minimizar a migração do tempo de inatividade para bancos de dados com até poucos TB de tamanho. Para bancos de dados muito grandes (mais de 10 TB), você pode considerar a migração do ADF usando AAD, Spark ou outras tecnologias de movimentação de dados.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Quanto tempo levaria para colocar a quantidade X de dados em hiperescala

O hiperscale é capaz de consumir 100 MB/s de dados novos/alterados, mas o tempo necessário para mover dados para bancos de dado no banco de dados SQL do Azure também é afetado pela taxa de transferência de rede disponível, velocidade de leitura de origem e o objetivo de nível de serviço de banco de dados de destino.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-azure-synapse-analytics"></a>Posso ler dados do armazenamento de BLOBs e fazer o carregamento rápido (como o polybase no Azure Synapse Analytics)

Você pode fazer com que um aplicativo cliente leia dados do armazenamento do Azure e carregue a carga de dados em um banco de dados de hiperescala (assim como você pode com qualquer outro banco de dado no banco de dados SQL do Azure). Atualmente, o polybase não tem suporte no banco de dados SQL do Azure. Como alternativa para fornecer carga rápida, você pode usar [Azure data Factory](../../data-factory/index.yml)ou usar um trabalho do spark no [Azure Databricks](/azure/azure-databricks/) com o [conector do Spark para SQL](spark-connector.md). O conector do Spark SQL dá suporte à inserção em massa.

Também é possível ler em massa dados do armazenamento de BLOBs do Azure usando BULK INSERT ou OPENROWSET: [exemplos de acesso em massa aos dados no armazenamento de BLOBs do Azure](/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

A recuperação simples ou o modelo de log em massa não é suportada na Hiperescala. O modelo de recuperação completa é necessário para fornecer alta disponibilidade e recuperação pontual. No entanto, a arquitetura de log de hiperescala fornece uma melhor taxa de ingestão de dados em comparação com outras camadas de serviço do Azure SQL Database.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>O hiperscale permite o provisionamento de vários nós para a ingestão paralela de grandes quantidades de dados

Não. O hiperscale é uma arquitetura de multi-processamento simétrico (SMP) e não é um processamento paralelo maciço (MPP) ou uma arquitetura de vários mestres. Você só pode criar várias réplicas para dimensionar cargas de trabalho somente leitura.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Qual é a versão mais antiga do SQL Server com suporte para migração para hiperescala

SQL Server 2005. Para obter mais informações, consulte [Migrar para um único banco de dados ou um banco de dados em pool](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database). Para problemas de compatibilidade, consulte [Resolvendo problemas de compatibilidade de migração do banco de dados](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>O hiperscale oferece suporte à migração de outras fontes de dados, como Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 e outras plataformas de banco de dado

Sim. O [serviço de migração de banco de dados do Azure](../../dms/dms-overview.md) dá suporte a vários cenários de

## <a name="business-continuity-and-disaster-recovery-questions"></a>Perguntas sobre continuidade de negócios e recuperação de desastres

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Quais SLAs são fornecidos para um banco de dados de hiperescala

Consulte [SLA para o banco de dados SQL do Azure](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Réplicas de computação secundárias adicionais aumentam a disponibilidade, até 99,99% para um banco de dados com duas ou mais réplicas de computação secundárias.

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>Os backups de banco de dados são gerenciados para mim pelo banco de dados SQL do Azure

Sim.

### <a name="how-often-are-the-database-backups-taken"></a>A frequência com que os backups de banco de dados são feitos

Não há backups completos, diferenciais e de log tradicionais para bancos de dados de hiperescala. Em vez disso, há instantâneos de armazenamento regular de arquivos de dados. O log gerado é simplesmente mantido como está para o período de retenção configurado, permitindo a restauração para qualquer ponto no tempo dentro do período de retenção.

### <a name="does-hyperscale-support-point-in-time-restore"></a>O hiperescala oferece suporte à restauração pontual

Sim.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>O que é o RTO (objetivo de ponto de recuperação)/Recovery tempo (objetivo) para restauração de banco de dados em hiperescala

O RPO é de 0 min. A maioria das operações de restauração é concluída em 60 minutos, independentemente do tamanho do banco de dados. O tempo de restauração pode ser mais longo para bancos de dados maiores e, se o banco de dados tiver experimentado uma atividade de gravação significativa antes e até o ponto de restauração no tempo.

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>O backup de banco de dados afeta o desempenho de computação em minhas réplicas primárias ou secundárias

Não. Os backups são gerenciados pelo subsistema de armazenamento e aproveitam os instantâneos de armazenamento. Eles não afetam as cargas de trabalho do usuário.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Posso executar a restauração geográfica com um banco de dados de hiperescala

Sim. A restauração geográfica tem suporte total. Diferentemente da restauração pontual, a restauração geográfica requer uma operação de tamanho de dados. Os arquivos de dados são copiados em paralelo, de modo que a duração dessa operação depende principalmente do tamanho do maior arquivo no banco de dados, em vez de ter o tamanho total. O tempo de restauração geográfica será significativamente menor se o banco de dados for restaurado na região do Azure [emparelhada](../../best-practices-availability-paired-regions.md) com a região do banco de dados de origem.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Posso configurar a replicação geográfica com o banco de dados de hiperescala

Não no momento.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Posso pegar um backup de banco de dados de hiperescala e restaurá-lo para meu servidor local ou em SQL Server em uma VM

Não. O formato de armazenamento para bancos de dados de hiperescala é diferente de qualquer versão lançada do SQL Server, e você não controla os backups ou tem acesso a eles. Para retirar seus dados de um banco de dado de hiperescala, você pode extrair dados usando qualquer tecnologia de movimentação de dados, ou seja, Azure Data Factory, Azure Databricks, SSIS etc.

## <a name="cross-feature-questions"></a>Perguntas entre recursos

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perco alguma funcionalidade ou recursos após a migração para a camada de serviço Hiperescala

Sim. Alguns recursos do banco de dados SQL do Azure não têm suporte em hiperescala, incluindo, mas não se limitando à retenção de backup de longo prazo. Depois de migrar seus bancos de dados para o Hiperescala, esses recursos param de funcionar.

### <a name="will-polybase-work-with-hyperscale"></a>O polybase trabalhará com o hiperescala

Não. O polybase não tem suporte no banco de dados SQL do Azure.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>O Multiscale tem suporte para R e Python

Não no momento.

### <a name="are-compute-nodes-containerized"></a>Nós de computação são em contêineres

Não. Os processos de hiperescala são executados em nós de [Service Fabric](https://azure.microsoft.com/services/service-fabric/) (VMS), não em contêineres.

## <a name="performance-questions"></a>Perguntas de desempenho

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Quanto taxa de transferência de gravação posso enviar por push em um banco de dados de hiperescala

O limite de produtividade do log de transações é definido como 100 MB/s para qualquer tamanho de computação de hiperescala. A capacidade de atingir essa taxa depende de vários fatores, incluindo, entre outros, o tipo de carga de trabalho, a configuração do cliente e a existência de capacidade de computação suficiente na réplica de computação primária para produzir o log a essa taxa.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Quantos IOPS eu obtenho na maior computação

O IOPS e a latência de e/s variam dependendo dos padrões de carga de trabalho. Se os dados que estão sendo acessados forem armazenados em cache na réplica de computação, você verá o desempenho de e/s semelhante ao SSD local.

### <a name="does-my-throughput-get-affected-by-backups"></a>Meu rendimento é afetado por backups

Não. A computação é dissociada da camada de armazenamento. Isso elimina o impacto no desempenho do backup.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Minha taxa de transferência é afetada ao provisionar réplicas de computação adicionais

Como o armazenamento é compartilhado e não há nenhuma replicação física direta acontecendo entre réplicas de computação primárias e secundárias, a taxa de transferência na réplica primária não será diretamente afetada pela adição de réplicas secundárias. No entanto, podemos limitar a carga de trabalho de forma agressiva no primário para permitir que o log seja aplicado em réplicas secundárias e servidores de página para acompanhar, para evitar um baixo desempenho de leitura em réplicas secundárias.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Como fazer diagnosticar e solucionar problemas de desempenho em um banco de dados de hiperescala

Para a maioria dos problemas de desempenho, particularmente aqueles que não têm raiz no desempenho do armazenamento, as etapas comuns de diagnóstico e solução de problemas do SQL se aplicam. Para diagnóstico de armazenamento específico de hiperescala, consulte [diagnóstico de solução de problemas de desempenho de hiperescala do SQL](hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Questões de escalabilidade

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Quanto tempo levaria para escalar verticalmente uma réplica de computação

O dimensionamento da computação geralmente leva até 2 minutos, independentemente do tamanho dos dados.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Meu banco de dados está off-line enquanto a operação de aumento/redução está em andamento?

Não. A ampliação e a redução estarão online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Devo esperar queda de conexão quando as operações de dimensionamento estão em andamento

A expansão ou redução dos resultados em conexões existentes são descartadas quando ocorre um failover no final da operação de dimensionamento. A adição de réplicas secundárias não resulta em quedas de conexão.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>É a expansão e redução vertical da operação de réplicas de computação automática ou disparada pelo usuário final

Usuário final. Não é automático.  

### <a name="does-the-size-of-my-tempdb-database-and-rbpex-cache-also-grow-as-the-compute-is-scaled-up"></a>O tamanho do meu `tempdb` banco de dados e do cache RBPEX também aumenta à medida que a computação é dimensionada

Sim. O `tempdb` tamanho do [cache RBPEX](service-tier-hyperscale.md#distributed-functions-architecture) e do banco de dados em nós de computação será dimensionado automaticamente conforme o número de núcleos aumentar.

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Posso provisionar várias réplicas de computação primárias, como um sistema de vários mestres, em que vários cabeçotes de computação primários podem impulsionar um nível mais alto de simultaneidade

Não. Somente a réplica de computação primária aceita solicitações de leitura/gravação. Réplicas de computação secundárias só aceitam solicitações somente leitura.

## <a name="read-scale-out-questions"></a>Ler as perguntas de expansão

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Quantas réplicas de computação secundárias posso provisionar

Criamos uma réplica secundária para bancos de dados de hiperescala por padrão. Se você quiser ajustar o número de réplicas, poderá fazer isso usando [portal do Azure](https://portal.azure.com) ou a [API REST](/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Como fazer conectar-se a essas réplicas de computação secundárias

Você pode se conectar a essas réplicas de computação somente leitura, definindo o `ApplicationIntent` argumento na cadeia de conexão como `ReadOnly` . Todas as conexões marcadas com `ReadOnly` são automaticamente roteadas para uma das réplicas de computação somente leitura adicionais. Para obter detalhes, consulte [usar réplicas somente leitura para descarregar cargas de trabalho de consulta somente leitura](read-scale-out.md).

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Como fazer validar se eu me conectasse com êxito à réplica de computação secundária usando o SSMS ou outras ferramentas de cliente?

Você pode executar a seguinte consulta T-SQL: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')` .
O resultado será `READ_ONLY` se você estiver conectado a uma réplica secundária somente leitura e `READ_WRITE` se estiver conectado à réplica primária. Observe que o contexto do banco de dados deve ser definido como o nome do banco de dados de hiperescala, não para o `master` banco de dados.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Posso criar um ponto de extremidade dedicado para uma réplica de expansão de leitura

Não. Você só pode se conectar a réplicas de expansão de leitura especificando `ApplicationIntent=ReadOnly` .

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>O sistema faz o balanceamento de carga inteligente da carga de trabalho de leitura

Não. Uma nova conexão com a intenção somente leitura é redirecionada para uma réplica de expansão de leitura arbitrária.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Posso escalar/reduzir verticalmente as réplicas de computação secundárias independentemente da réplica primária

Não. A réplica de computação secundária também é usada como destinos de failover de alta disponibilidade, portanto, eles precisam ter a mesma configuração que a primária para fornecer o desempenho esperado após o failover.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Obtenho um `tempdb` dimensionamento diferente para minha computação primária e minhas réplicas de computação secundárias adicionais

Não. Seu `tempdb` banco de dados está configurado com base no provisionamento de tamanho de computação, suas réplicas de computação secundárias têm o mesmo tamanho que a computação primária.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Posso adicionar índices e exibições em minhas réplicas de computação secundárias

Não. Os bancos de dados de hiperescala têm armazenamento compartilhado, o que significa que todas as réplicas de computação veem as mesmas tabelas, índices e exibições. Se desejar que índices adicionais sejam otimizados para leituras no secundário, você deverá adicioná-los no primário.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Quanto atraso haverá entre as réplicas de computação primárias e secundárias

Latência de dados do momento em que uma transação é confirmada no primário até o momento em que é legível em um secundário depende da taxa de geração de log atual, do tamanho da transação, da carga na réplica e de outros fatores. A latência de dados típica para transações pequenas é em dezenas de milissegundos, no entanto, não há nenhum limite superior de latência de dados. Os dados em uma determinada réplica secundária são sempre consistentes transacionalmente. No entanto, em uma determinada latência de dados pontual pode ser diferente para réplicas secundárias diferentes. As cargas de trabalho que precisam ler dados confirmados imediatamente devem ser executadas na réplica primária.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a camada de serviço de hiperescala, consulte [camada de serviço de hiperescala](service-tier-hyperscale.md).