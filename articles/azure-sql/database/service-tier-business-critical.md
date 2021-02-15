---
title: Camada de serviço comercialmente crítica
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba mais sobre a camada de serviço comercialmente crítica para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 830ecc44d0def13e51cb06704bef429bb8860cd6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780216"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Camada de Comercialmente Crítico-banco de dados SQL do Azure e SQL Instância Gerenciada do Azure 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Comercialmente Crítico camada é chamada Premium no modelo de compra de DTU. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [Modelos e recursos de compra do Banco de Dados SQL do Azure](purchasing-models.md).

O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada são baseados na arquitetura do mecanismo de banco de dados SQL Server que é ajustada para o ambiente de nuvem a fim de garantir a disponibilidade de 99,99%, mesmo nos casos de falhas de infraestrutura. Há três modelos arquitetônicos que são usados:
- Uso Geral/Padrão 
- Comercialmente Crítico/Premium
- Hiperescala

O modelo da camada de serviço Premium/Comercialmente Crítico que se baseia em um cluster de processos do mecanismo de banco de dados. Esse modelo de arquitetura se baseia no fato de que há sempre um quorum de nós de mecanismo de banco de dados disponíveis e tem impacto mínimo no desempenho em sua carga de trabalho, mesmo durante as atividades de manutenção. Atualmente, a camada de serviço de hiperescala está disponível apenas para o banco de dados SQL do Azure (não o SQL Instância Gerenciada) e é um nível de desempenho de computação e armazenamento altamente escalonável que aproveita a arquitetura do Azure para escalar horizontalmente os recursos de armazenamento e de computação de um banco de dados no banco de dados SQL do Azure, muito além dos limites disponíveis para as camadas de serviço Comercialmente Crítico Uso Geral

O Azure atualiza e corrige o sistema operacional subjacente, os drivers e o mecanismo de banco de dados SQL Server de forma transparente com o mínimo de tempo de inatividade para os usuários finais. 

A disponibilidade Premium é habilitada nas camadas de serviço Premium e Comercialmente Crítico e é projetada para cargas de trabalho intensivas que não podem tolerar nenhum impacto no desempenho devido às operações de manutenção contínuas.

A computação e o armazenamento são integrados em um único nó no modelo Premium. A alta disponibilidade nesse modelo de arquitetura é obtida pela replicação de computação (SQL Server processo de mecanismo de banco de dados) e armazenamento (SSD anexado localmente) implantado em um cluster de quatro nós, usando uma tecnologia semelhante à [grupos de disponibilidade Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)SQL Server.

![Cluster de nós de mecanismo de banco de dados](./media/service-tier-business-critical/business-critical-service-tier.png)

O processo do mecanismo de banco de dados SQL Server e os arquivos. MDF/. ldf subjacentes são colocados no mesmo nó com o armazenamento SSD anexado localmente, fornecendo baixa latência à carga de trabalho. A alta disponibilidade é implementada usando uma tecnologia semelhante à SQL Server [grupos de disponibilidade Always on](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada banco de dados é um cluster de nós de banco de dados com um banco de dados primário que pode ser acessado para cargas de trabalho do cliente e um dos três processos secundários que contêm cópias de data. O nó primário envia constantemente as alterações para os nós secundários para garantir que os dados estejam disponíveis em réplicas secundárias se o nó primário falhar por algum motivo. O failover é manipulado pelo mecanismo de banco de dados SQL Server – uma réplica secundária torna-se o nó primário e uma nova réplica secundária é criada para garantir que haja nós suficientes no cluster. A carga de trabalho é automaticamente redirecionada para o novo nó primário.

Além disso, o cluster Business Critical possui o recurso interno [Expansão Scale-Out](read-scale-out.md) que fornece um nó somente leitura interno gratuito que pode ser usado para executar consultas somente leitura (por exemplo, relatórios) não deve afetar o desempenho de sua carga de trabalho principal.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher essa camada de serviço

Comercialmente Crítico camada de serviço foi projetada para aplicativos que exigem respostas de baixa latência do armazenamento SSD subjacente (1-2 ms em média), recuperação rápida se a infraestrutura subjacente falhar, ou se for necessário descarregar relatórios, análises e consultas somente leitura para a réplica secundária legível livre de encargos do banco de dados primário.

Os principais motivos pelos quais você deve escolher Comercialmente Crítico camada de serviço em vez de Uso Geral camada são:
-   **Requisitos de latência de e/s baixa** – cargas de trabalho que precisam de uma resposta rápida da camada de armazenamento (1-2 milissegundos em média) devem usar a camada de comercialmente crítico. 
-   **Comunicação frequente entre o aplicativo e o banco de dados** . Os aplicativos que não podem aproveitar o cache da camada de aplicativo ou solicitar o envio [em lote](../performance-improve-use-batching.md) e precisam enviar muitas consultas SQL que devem ser processadas rapidamente são bons candidatos para a camada de comercialmente crítico.
-   **Grande número de atualizações** – as operações de inserção, atualização e exclusão modificam as páginas de dados na memória (página suja) que devem ser salvas em arquivos de dados com a `CHECKPOINT` operação. A falha do processo do mecanismo de banco de dados potencial ou um failover do banco de dados com um grande número de páginas sujas pode aumentar o tempo de recuperação na camada Uso Geral. Use Comercialmente Crítico camada se você tiver uma carga de trabalho que cause muitas alterações na memória. 
-   **Transações de longa execução que modificam dados** . As transações que são abertas por um tempo maior impedem o truncamento do arquivo de log, o que pode aumentar o tamanho do log e o número de [arquivos de log virtuais (VLF)](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). O alto número de VLFs pode retardar a recuperação do banco de dados após o failover.
-   **Carga de trabalho com consultas analíticas e de relatório** que podem ser redirecionadas para a réplica somente leitura secundária de liberação de custo.
- **Maior resiliência e recuperação mais rápida de falhas** . Em caso de falha do sistema, o banco de dados na instância primária será desabilitado e uma das réplicas secundárias se tornará imediatamente o novo banco de dados primário de leitura/gravação que está pronto para processar consultas. O mecanismo de banco de dados não precisa analisar e refazer transações do arquivo de log e carregar todos os dados no buffer de memória.
- **Proteção de corrupção de dados avançada** . A camada de Comercialmente Crítico aproveita as réplicas de banco de dados nos bastidores para fins de continuidade dos negócios e, portanto, o serviço também utiliza o reparo automático de página, que é a mesma tecnologia usada para SQL Server espelhamento de banco de dados [e grupos de disponibilidade](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). Caso uma réplica não possa ler uma página devido a um problema de integridade de dados, uma cópia nova da página será recuperada de outra réplica, substituindo a página ilegível sem perda de dados ou tempo de inatividade do cliente. Essa funcionalidade será aplicável na camada Uso Geral se o banco de dados tiver uma réplica geográfica-secundária.
- **Maior disponibilidade** -a camada de comercialmente crítico na configuração multi-AZ garante 99,995% de disponibilidade, em comparação com 99,99% da camada uso geral.
- A Comercialmente Crítico camada de **recuperação geográfica rápida** configurada com replicação geográfica tem um RPO (objetivo de ponto de recuperação) garantido de 5 s e RTO (objetivo de tempo de recuperação) de 30 segundos para 100% de horas implantadas.

## <a name="next-steps"></a>Próximas etapas

- Encontre características de recursos (número de núcleos, e/s, memória) da camada de Comercialmente Crítico no [SQL instância gerenciada](../managed-instance/resource-limits.md#service-tier-characteristics), banco de dados individual no modelo de [VCORE](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) ou [modelo de DTU](resource-limits-dtu-single-databases.md#premium-service-tier)ou pool elástico no modelo de [VCORE](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) e [modelo de DTU](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits).
- Saiba mais sobre as camadas [Uso Geral](service-tier-general-purpose.md) e [Hiperescala](service-tier-hyperscale.md).
- Saiba mais sobre [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [continuidade de negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md).