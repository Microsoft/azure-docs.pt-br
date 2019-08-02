---
title: Em Hiperescala do banco de dados SQL do Azure perguntas Frequentes | Microsoft Docs
description: Respostas para perguntas comuns que os clientes fazem sobre um banco de dados SQL do Azure na camada de serviço da Hiperescala - comumente chamado de banco de dados da Hiperescala.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 49d1e171d4d4b2210a98c59332f4842e23a2f2b9
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537838"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Perguntas frequentes sobre bancos de dados SQL do Azure da Hiperescala

Este artigo fornece respostas para perguntas frequentes para clientes que consideram um banco de dados na camada de serviço hiperescala de banco de dados SQL do Azure, normalmente chamado de um banco de dados em hiperescala. Este artigo descreve os cenários que o Hyperscale suporta e os serviços entre recursos são compatíveis com o Banco de Dados SQL da Hiperescala em geral.

- Esta FAQ destina-se a leitores que tenham uma breve compreensão da camada de serviço Hiperescala e que desejam ter suas dúvidas e preocupações específicas respondidas.
- Esta FAQ não pretende ser um guia ou responder a perguntas sobre como usar um banco de dados do Banco de Dados SQL da Hiperescala. Para isso, recomendamos que você consulte a documentação do [Azure Banco de Dados SQL da Hiperescala](sql-database-service-tier-hyperscale.md).

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-a-hyperscale-database"></a>O que é um banco de dados Hiperescala?

Um banco de dados da Hiperescala é um banco de dados SQL do Azure na camada de serviço da Hiperescala que é apoiado pela tecnologia de armazenamento scale-out da Hiperescala. Um banco de dados Hiperescala suporta até 100 TB de dados e oferece alto rendimento e desempenho, bem como escalonamento rápido para se adaptar aos requisitos de carga de trabalho. O dimensionamento é transparente para o aplicativo - conectividade, processamento de consultas e assim por diante, funciona como qualquer outro banco de dados SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quais tipos de recursos e modelos de compra suportam a Hiperescala?

A camada de serviço Hiperescala está disponível apenas para bancos de dados individuais usando o modelo de compra baseado em vCore no Banco de Dados SQL do Azure.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Como o nível de serviço Hiperescala difere das camada de serviço de uso geral e de negócios críticos?

O serviço baseado em vCore as camadas são diferenciadas principalmente com base em disponibilidade, o tipo de armazenamento e IOPs.

- A camada de serviço de Propósito Geral é apropriada para a maioria das cargas de trabalho de negócios, oferecendo um conjunto equilibrado de opções de computação e armazenamento em que a latência de I/O ou tempos de failover não são a prioridade.
- A camada de serviço da Hiperescala é otimizada para cargas de trabalho de banco de dados muito grandes.
- A camada de serviço Business Critical é apropriada para cargas de trabalho de negócios em que a latência de I/O é uma prioridade.

| | Tipo de recurso | Uso geral |  Hiperescala | Comercialmente Crítico |
|:---|:---:|:---:|:---:|:---:|:---:|
| **Mais adequado para** |Todos|  A maioria das cargas de trabalho comerciais. Oferece opções equilibradas de computação e armazenamento orientadas ao orçamento. | Aplicativos de dados com grandes requisitos de capacidade de dados e a capacidade de dimensionar e armazenar em escala automática os recursos de computação. | Aplicativos OLTP com alta taxa de transação e menor I/O de latência. Oferece maior resiliência a falhas usando várias réplicas isoladas.|
|  **Tipo de recurso** ||Banco de dados único / Elástico pool / instância gerenciada | Banco de dados individual | Banco de dados único / Elástico pool / instância gerenciada |
| **Tamanho de computação**|Banco de dados único / Elástico pool * | 1 a 80 vCores | 1 a 80 vCores * | 1 a 80 vCores |
| |Instância gerenciada | 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Tipo de armazenamento** | Todos |Armazenamento remoto Premium (por instância) | Armazenamento desacoplado com cache SSD local (por instância) | Armazenamento SSD local super rápido (por instância) |
| **Tamanho de armazenamento** | Banco de dados único / Elástico pool | 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância gerenciada  | 32 GB A 8 TB | N/D | 32 GB – 4 TB |
| **Taxa de transferência de e/s** | Banco de dados único ** | 500 IOPS por vCore com máximo de 7.000 IOPS | Hiperescala é uma arquitetura de várias camadas com o cache em vários níveis. IOPs efetivas dependerá da carga de trabalho. | 5000 IOPS com 200.000 IOPS máximo|
| | Instância gerenciada | Depende do tamanho do arquivo | N/D | Instância Gerenciada: Depende do tamanho do arquivo|
|**Disponibilidade**|Todos|1 réplica, sem escala de leitura, sem cache local | Múltiplas réplicas, até 15 cache local parcial em escala de leitura | 3 réplicas, 1 HA de escala de leitura, com redundância de zona, cache local completo |
|**Backups**|Todos|RA-GRS, 7-35 dias (7 dias por padrão)| RA-GRS, 7-35 dias (7 dias por padrão), recuperação point-in-time de tempo constante (PITR) | RA-GRS, 7-35 dias (7 dias por padrão) |

\* Pools Elásticos não tem suportados na camada de serviço em hiper escala

### <a name="who-should-use-the-hyperscale-service-tier"></a>Quem deve usar a camada de serviço Hiperescala

A camada de serviço Hiperescala destina-se principalmente a clientes que possuem grandes bancos de dados SQL Server locais e desejam modernizar seus aplicativos migrando para a nuvem ou para clientes que já usam o Banco de Dados SQL do Azure e desejam expandir significativamente o potencial de crescimento do banco de dados. A Hiperescala também é destinado a clientes que buscam alto desempenho e alta escalabilidade. Com em Hiperescala, você obtém:

- Suporte para até 100 TB de tamanho de banco de dados
- Backups rápidos de banco de dados, independentemente do tamanho do banco de dados (os backups são baseados em instantâneos de arquivos)
- O banco de dados rápido é restaurado independentemente do tamanho do banco de dados (as restaurações são de instantâneos de arquivos)
- Maior taxa de transferência de log resulta em tempos rápidos de confirmação de transação, independentemente do tamanho do banco de dados
- Leia scale-out para um ou mais nós somente leitura para descarregar sua carga de trabalho de leitura e para hot-standbys.
- Escalabilidade rápida de computação, em tempo constante, para ser mais poderosa para acomodar a carga de trabalho pesada e, em seguida, reduzir a escala, em tempo constante. Isso é semelhante a escalonar para cima e para baixo entre um P6 e um P11, por exemplo, mas muito mais rápido, já que esse não é um tamanho de operação de dados.

### <a name="what-regions-currently-support-hyperscale"></a>Quais regiões atualmente dão suporte em Hiperescala

A camada de hiperescala de banco de dados SQL do Azure está atualmente disponível nas regiões listadas sob [visão geral de hiperescala de banco de dados SQL do Azure](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Posso criar vários bancos de dados de Hiperescala por servidor lógico

Sim. Para obter mais informações e limites sobre o número de bancos de dados Hiperescala por servidor lógico, consulte [Limites de recursos do Banco de Dados SQL para bancos de dados únicos e agrupados em um servidor lógico](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quais são as características de desempenho de um banco de dados em hiperescala

A arquitetura Hiperescala do Banco de Dados SQL oferece alto desempenho e rendimento, suportando grandes tamanhos de banco de dados. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>O que é a escalabilidade de um banco de dados em Hiperescala

O Banco de Dados SQL da Hiperescala fornece escalabilidade rápida com base na sua demanda de carga de trabalho.

- **Dimensionamento para cima/para baixo**

  Com a Hiperescala, você pode dimensionar o tamanho de computação principal em termos de recursos como CPU, memória e, em seguida, reduzir a escala, em tempo constante. Como o armazenamento é compartilhado, a ampliação e o dimensionamento não são um tamanho de operação de dados.  
- **Dimensionamento In/Out**

  Com a Hiperescala, você também tem a capacidade de provisionar um ou mais nós de computação adicionais que você pode usar para atender às suas solicitações de leitura. Isso significa que você pode usar esses nós de computação adicionais como nós somente leitura para descarregar sua carga de trabalho de leitura da computação primária. No além a fim de somente leitura, que esses nós também servem como hot-em espera do caso de um failover do primário.

  O provisionamento de cada um desses nós de computação adicionais pode ser feito em tempo constante e é uma operação on-line. Você pode conectar-se a esses nós de computação adicionais somente leitura configurando o argumento `ApplicationIntent` na cadeia de conexão para `readonly`. Todas as conexões marcadas com `readonly` são roteadas automaticamente para um dos nós de computação adicionais somente leitura.

## <a name="deep-dive-questions"></a>Perguntas de aprofundamento

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Posso combinar bancos de dados individuais em um único servidor lógico e hiperescala

Sim, pode.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>O da Hiperescala requer que meu modelo de programação de aplicativo mude

Não, seu modelo de programação de aplicativo permanece como está. Você usa sua cadeia de conexão como de costume e os outros modos regulares para interagir com o banco de dados SQL do Azure.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Quais níveis de isolamento de transação serão padrão no banco de dados Banco de Dados SQL da Hiperescala?

No nó primário, o nível de isolamento da transação é RCSI (Read Committed Snapshot Isolation). Nos nós secundários da escala de leitura, o nível de isolamento é Instantâneo.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Posso trazer minha licença local ou do IaaS SQL Server para o Banco de Dados SQL da Hiperescala

Sim, [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) está disponível para em Hiperescala. Cada núcleo do SQL Server Standard pode mapear para 1 Hiperescala vCores. Cada núcleo do SQL Server Enterprise pode mapear para 4 vCores de Hiperescala. Você não precisa de uma licença de SQL para réplicas secundárias. O preço do Benefício Híbrido do Azure será aplicado automaticamente a réplicas de leitura de escala (secundária).

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Que tipo de cargas de trabalho é o Banco de Dados SQL da Hiperescala projetado para

O Banco de Dados SQL da Hiperescala suporta todas as cargas de trabalho do SQL Server, mas é otimizado principalmente para OLTP. Você pode colocar híbrido (HTAP) e analítico (armazém de dados) cargas de trabalho.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Como posso escolher entre o SQL Data Warehouse do Azure e a Hiperescala do banco de dados SQL

Se você estiver executando consultas de análises interativas usando o SQL Server como um data warehouse, em hiperescala do banco de dados SQL é uma ótima opção porque você pode hospedar os armazéns de dados relativamente pequeno (por exemplo, alguns TB até 10 segundos de TB) a um custo menor e você pode migrar seu dados w arehouse carga de trabalho em hiperescala do banco de dados SQL sem alterações de código T-SQL.

Se você estiver executando análise de dados em grande escala com consultas complexas e usando armazéns de dados Parallel Data Warehouse (PDW), Teradata ou outro MPP (Massively Parallel Processor), o SQL Data Warehouse poderá ser a melhor opção.
  
## <a name="sql-database-hyperscale-compute-questions"></a>Perguntas de cálculo da Hiperescala do Banco de Dados SQL

### <a name="can-i-pause-my-compute-at-any-time"></a>Posso pausar meu computação a qualquer momento

Não desta vez, no entanto, você pode dimensionar sua computação e o número de réplicas para reduzir o custo durante horários de pico.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Posso provisionar uma computação com RAM extra para minha carga de trabalho com uso intensivo de memória

Não. Para obter mais RAM, você precisa atualizar para um tamanho da computação maior. Para obter mais informações, consulte [Armazenamento Hiperescala e tamanhos de computação](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Posso provisionar vários nós de computação de tamanhos diferentes

Não.

### <a name="how-many-read-scale-replicas-are-supported"></a>Quantas réplicas de escala de leitura são suportadas

Por padrão, os bancos de dados em hiperescala são criados com uma réplica de escala de leitura (duas réplicas no total). Você pode dimensionar o número de réplicas somente leitura entre 0 e 4 usando o [portal do Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Para alta disponibilidade, preciso provisionar nós de computação adicionais

Em bancos de dados em hiperescala, a resiliência é fornecida no nível de armazenamento. Você só precisa de uma réplica para fornecer resiliência. Quando a réplica de cálculo está inativa, uma nova réplica é criada automaticamente sem perda de dados.

No entanto, se houver apenas uma réplica, poderá levar algum tempo para criar o cache local na nova réplica após o failover. Durante a fase de recriação do cache, o banco de dados busca dados diretamente dos servidores de páginas, resultando em IOPS e desempenho de consulta degradados.

Para aplicativos de missão crítica que exigem alta disponibilidade, você deve provisionar pelo menos dois nós de computação, incluindo o nó de computação principal (padrão). Dessa forma, há um hot-standby disponível no caso de um failover.

## <a name="data-size-and-storage-questions"></a>Perguntas sobre o tamanho e o armazenamento de dados

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Qual é o tamanho máximo do banco de dados suportado pelo Banco de Dados SQL da Hiperescala

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Qual é o tamanho do log de transações com a Hiperescala

O log de transações com a Hiperescala é praticamente infinito. Você não precisa se preocupar com a falta de espaço de log em um sistema que tenha um alto throughput de log. No entanto, a taxa de geração de logs pode ser reduzida para cargas de trabalho agressivas contínuas. A taxa de geração de log prolongada de pico é de aproximadamente 100 MB/s.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Minha escala de banco de dados temp como meu banco de dados cresce

Seu banco de dados `tempdb` está localizado no armazenamento SSD local e é configurado com base no tamanho da computação que você provisiona. Seu `tempdb` é otimizado e definido para fornecer benefícios máximos de desempenho. O tamanho `tempdb` não é configurável e é gerenciado para você pelo subsistema de armazenamento.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>O tamanho do meu banco de dados aumenta automaticamente ou eu preciso gerenciar o tamanho dos arquivos de dados

O tamanho do seu banco de dados aumenta automaticamente à medida que você insere / ingressa mais dados.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Qual é o menor tamanho de banco de dados que o Banco de Dados SQL da Hiperescala suporta ou inicia com

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>Em que incrementos o tamanho do meu banco de dados aumenta?

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>O armazenamento da Hiperescala de Banco de Dados SQL é local ou remota

Na Hiperescala, os arquivos de dados são armazenados no armazenamento padrão do Azure. Os dados são muito armazenados em cache no armazenamento SSD local, em máquinas próximas aos nós de computação. Além disso, os nós de cálculo possuem um cache no SSD local e na memória (Buffer Pool e assim por diante) para reduzir a frequência de busca de dados de nós remotos.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Posso gerenciar ou definir arquivos ou grupos de arquivos com a Hiperescala

Não
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Posso provisionar um limite rígido no crescimento de dados para meu banco de dados

Não

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Como os arquivos de dados são definidos com o Banco de Dados SQL da Hiperescala

Os arquivos de dados são controlados pelos servidores da página. À medida que o tamanho dos dados aumenta, os arquivos de dados e os nós do servidor de páginas associados são adicionados.

### <a name="is-database-shrink-supported"></a>A redução do banco de dados é suportada

Não

### <a name="is-database-compression-supported"></a>A compactação de banco de dados é suportada

Sim

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Se eu tiver uma tabela enorme, os dados da minha tabela serão espalhados por vários arquivos de dados

Sim. As páginas de dados associadas a uma determinada tabela podem acabar em vários arquivos de dados, que fazem parte do mesmo grupo de arquivos. O SQL Server usa uma [estratégia de preenchimento proporcional](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) para distribuir dados sobre arquivos de dados.

## <a name="data-migration-questions"></a>Questões de migração de dados

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Posso mover meus bancos de dados SQL do Azure existentes para a camada de serviço de Hiperescala

Sim. Você pode mover seus bancos de dados SQL do Azure existentes para a Hiperescala. Essa é uma migração unidirecional. Você não pode mover bancos de dados da Hiper escala para outra camada de serviço. Recomendamos que você faça uma cópia de seus bancos de dados de produção e migre para a Hiperescala para prova de conceitos (POCs).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Posso mover meus bancos de dados Hiperescala para outras edições?

Não. Neste momento, você não pode mover um banco de dados em hiperescala para outra camada de serviço.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perco alguma funcionalidade ou recursos após a migração para a camada de serviço Hiperescala

Sim. Alguns dos recursos de banco de dados SQL ainda não são compatíveis em hiperescala, incluindo mas não se limitando, backup de retenção de longo prazo. Depois de migrar seus bancos de dados para o Hiperescala, esses recursos param de funcionar.  Esperamos que essas limitações ser temporária.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Posso mover meu banco de dados do SQL Server local ou meu banco de dados de máquina virtual do SQL Server para a Hiperescala

Sim. Você pode usar todas as tecnologias de migração existentes para migrar para a Hiperescala, incluindo BACPAC, replicação transacional, carregamento de dados lógicos. Consulte também o [Serviço de Migração de Banco de Dados do Azure](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Qual é o meu tempo de inatividade durante a migração de um ambiente de máquina local ou virtual para a Hiperescala e como posso minimizá-lo

O tempo de inatividade é o mesmo que o tempo de inatividade quando você migra seus bancos de dados para um único banco de dados no Banco de Dados SQL do Azure. Você pode usar [replicação transacional](replication-to-sql-database.md#data-migration-scenario
) para minimizar a migração do tempo de inatividade para bancos de dados com até poucos TB de tamanho. Para bancos de dados muito grandes (10+ TB), você pode migrar dados usando ADF, Spark ou outras tecnologias de movimentação de dados.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Quanto tempo levaria para trazer X quantidade de dados para o banco de dados SQL Hiperescala

Hiperescala é capaz de consumir 100 MB/s de dados novo/alterado.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Posso ler dados do armazenamento de blob e fazer uma carga rápida (como o Polybase e o SQL Data Warehouse)

Você pode ler dados do Armazenamento do Azure e carregar a carga de dados em um banco de dados Hiperescala (assim como você pode fazer com um único banco de dados comum). Atualmente, o Polybase não é suportado no Banco de Dados SQL do Azure. Você pode fazer o Polybase usando o [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) ou executando um trabalho do Spark em [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) com o conector [Spark do SQL](sql-database-spark-connector.md). O conector do Spark SQL dá suporte à inserção em massa.

A recuperação simples ou o modelo de log em massa não é suportada na Hiperescala. O modelo de recuperação total é necessário para fornecer alta disponibilidade. No entanto, a Hiperescala fornece uma taxa de entrada de dados melhor em comparação a um único banco de dados SQL do Azure devido à nova arquitetura de log.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>O Banco de Dados SQL da Hiperescala permite o provisionamento de vários nós para ingerir grandes quantidades de dados

Não. O banco de dados SQL da Hiperescala é uma arquitetura SMP e não é um multiprocessamento assimétrico ou uma arquitetura multi-master. Você só pode criar várias réplicas para dimensionar cargas de trabalho somente leitura.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Qual é a versão mais antiga do SQL Server? A migração do suporte do Banco de Dados SQL da Hiperescala

SQL Server 2005. Para obter mais informações, consulte [Migrar para um único banco de dados ou um banco de dados em pool](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Para problemas de compatibilidade, consulte [Resolvendo problemas de compatibilidade de migração do banco de dados](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>O Banco de Dados SQL da Hiperescala oferece suporte à migração de outras fontes de dados, como Aurora, MySQL, Oracle, DB2 e outras plataformas de banco de dados?

Sim. Vindo de diferentes fontes de dados diferentes do SQL Server requer migração lógica. Você pode usar o [Serviço de Migração de Banco de Dados do Azure](../dms/dms-overview.md) para uma migração lógica.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Perguntas sobre continuidade de negócios e recuperação de desastres

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Quais SLAs são fornecidos para um banco de dados da Hiperescala

Com o padrão, as principais e um secundário legível 1, o SLA é 99,95% de disponibilidade.  Com mais de réplicas, o SLA vai até 99,99%.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Os backups de banco de dados são gerenciados para mim pelo serviço Banco de Dados SQL do Azure

Sim

### <a name="how-often-are-the-database-backups-taken"></a>A frequência com que os backups de banco de dados são feitos

Não há backups tradicionais completos, diferenciais e de log para bancos de dados Banco de Dados SQL da Hiperescala. Em vez disso, há regularmente instantâneos dos arquivos de dados e log gerado simplesmente será mantida como está para o período de retenção configurado ou disponível para você.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>O suporte do Banco de Dados SQL da Hiperescala é uma restauração pontual?

Sim

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>O que é o objetivo de ponto de recuperação (RPO)/objetivo de tempo de recuperação (RTO) com backup/restauração no banco de dados SQL Hiperescala

O RPO é 0 min. O objetivo do RTO é menos de 10 minutos, independentemente do tamanho do banco de dados. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Os backups de grandes bancos de dados afetam o desempenho de computação em meu primário

Não. Os backups são gerenciados pelo subsistema de armazenamento e aproveitam os instantâneos de arquivos. Eles não afetam a carga de trabalho do usuário no primário.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Posso executar a restauração geográfica com um banco de dados Hiperescala do Banco de Dados SQL

Sim.  A restauração geográfica tem suporte total.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Posso configurar a Geo-Replicação com o banco de dados Hiperescala do Banco de Dados SQL

Não no momento.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Meus nós de computação secundários são replicados geograficamente com o Banco de Dados SQL da Hiperescala

Não no momento.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Posso fazer um backup do banco de dados Banco de Dados SQL da Hiperescala e restaurá-lo no meu servidor local ou no SQL Server na VM

Não. O formato de armazenamento dos bancos de dados da Hiperescala é diferente do SQL Server tradicional, e você não controla backups nem tem acesso a eles. Para retirar seus dados de um banco de dados Hiperescala do Banco de Dados SQL, use o serviço de exportação ou use o script mais o BCP.

## <a name="cross-feature-questions"></a>Entre as perguntas de recurso

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perco alguma funcionalidade ou recursos após a migração para a camada de serviço Hiperescala

Sim. Não há suporte para alguns dos recursos de banco de dados SQL em hiperescala, incluindo, mas o backup de retenção de longo prazo não limitada. Depois de migrar seus bancos de dados para o Hiperescala, esses recursos param de funcionar.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>O Polybase trabalhará com o Banco de Dados SQL da Hiperescala?

Não. O Polybase não é compatível com o banco de dados SQL do Azure.

### <a name="does-the-compute-have-support-for-r-and-python"></a>O computador tem suporte para R e python?

Não. R e Python não são suportados no Banco de Dados SQL do Azure.

### <a name="are-the-compute-nodes-containerized"></a>Os nós de computação são contêinerizados

Não. Seu banco de dados reside em uma VM de computação e não em um contêiner.

## <a name="performance-questions"></a>Perguntas de desempenho

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Que tamanho de taxa de transferência posso enviar na maior computação em Hiperescala do Banco de Dados SQL

Temos visto 100 MB/s consistente dos dados de alteração (geração de dados de log de transações)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Quantas IOPS recebo na maior base de dados da Hiperescala do Banco de Dados SQL

IOPS e latência de e/s irão variar dependendo dos padrões de carga de trabalho.  Se os dados que precisam ser acessados são locais para o cache da computação, ele será os mesmos padrões de e/s do SSD local.   

### <a name="does-my-throughput-get-affected-by-backups"></a>Meu rendimento é afetado por backups

Não. A computação é desacoplada da camada de armazenamento para evitar impacto na computação.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Meu rendimento é afetado à medida que provisiono nós de computação adicionais?

Porque o armazenamento é compartilhado e não há nenhuma replicação física direta acontecendo entre nós de computação primários e secundários, tecnicamente, a taxa de transferência no nó primário não será afetada pela adição de nós de escala de leitura. No entanto, podemos limitar a carga de trabalho agressiva contínua para permitir que o log se aplique em nós secundários e servidores de paginação para recuperar o atraso e evitar desempenho de leitura ruim em nós secundários.

## <a name="scalability-questions"></a>Questões de escalabilidade

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Quanto tempo levaria para aumentar e diminuir um nó de computação

Escalonando a computação para cima ou para baixo deve levar de 5 a 10 minutos, independentemente do tamanho de dados.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Meu banco de dados está off-line enquanto a operação de aumento/redução está em andamento?

Não. A ampliação e a redução estarão online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Devo esperar queda de conexão quando as operações de dimensionamento estão em andamento

Aumentar ou diminuir o escalonamento resulta em conexões existentes sendo descartadas quando o failover acontece com o nó de cálculo com o tamanho de destino. Adicionar réplicas de leitura não resulta em quedas de conexão.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>É a ampliação e desativação de nós de computação automáticos ou operação acionada pelo usuário final

Usuário final. Não é automático.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>O meu `tempb` também cresce à medida que o computador é ampliado

Sim. O banco de dados de temperatura aumentará automaticamente conforme o cálculo cresce.  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Posso provisionar vários nós de computação principal como um sistema de vários mestre em que várias cabeças de computação principal podem orientar um nível mais alto de simultaneidade

Não. Apenas o nó de computação principal aceita solicitações de leitura / gravação. Os nós de computação secundários aceitam somente solicitações somente leitura.

## <a name="read-scale-questions"></a>Leia as perguntas da escala

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Quantos nós de computação secundária pode provisionar

Por padrão, criamos 2 réplicas para bancos de dados em hiperescala. Se você quiser ajustar o número de réplicas, você pode fazer isso usando [portal do Azure](https://portal.azure.com).

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Como eu me conecto a esses nós de computação secundários?

Você pode conectar-se a esses nós de computação adicionais somente leitura configurando o argumento `ApplicationIntent` na cadeia de conexão para `readonly`. Todas as conexões marcadas com `readonly` são roteadas automaticamente para um dos nós de computação adicionais somente leitura.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Posso criar um endpoint dedicado para a réplica em escala de leitura

Não. Você pode conectar somente a réplica de escala de leitura, especificando `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>O sistema faz o balanceamento de carga inteligente da carga de trabalho de leitura

Não. A carga de trabalho somente leitura é redirecionada para uma réplica aleatória da escala de leitura.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Posso aumentar ou diminuir os nós de computação secundários independentemente do cálculo principal

Não. Os nós de computação secundário também são usados para alta disponibilidade, para que eles precisem ser a mesma configuração que o primário, no caso de um failover.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Eu obtenho um tamanho de banco de dados diferente para meu computador principal e meus nós de computação secundários adicionais?

Não. Seu `tempdb` é configurado com base em como o provisionamento de tamanho de computação, os nós de computação secundária são do mesmo tamanho que a computação primário.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Posso adicionar índices e exibições nos meus nós de computação secundários?

Não. Os bancos de dados de Hiperescala têm armazenamento compartilhado, o que significa que todos os nós de computação veem as mesmas tabelas, índices e visualizações. Se você quiser índices adicionais otimizados para leituras no secundário - você deve adicioná-los no primeiro primário.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Quanto atraso haverá entre o nó de computação principal e secundário

A partir do momento em que uma transação é confirmada no primário, dependendo da taxa de geração de logs, ela pode ser instantânea ou em milissegundos baixos.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a camada de serviço em hiperescala, consulte [camada de serviço em hiperescala](sql-database-service-tier-hyperscale.md).
