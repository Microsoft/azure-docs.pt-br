---
title: Monitoramento e ajuste de desempenho
description: Uma visão geral dos recursos de monitoramento e ajuste de desempenho e a metodologia no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 09/30/2020
ms.openlocfilehash: 3d28cf1a89ff455cfbed26aad234607ec8cd7e63
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490560"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitoramento e ajuste de desempenho no Banco de Dados SQL do Azure e da Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Para monitorar o desempenho de um banco de dados no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada, comece monitorando os recursos de CPU e e/s usados pela sua carga de trabalho em relação ao nível de desempenho do banco de dados escolhido na seleção de uma camada de serviço e nível de desempenho específicos. Para fazer isso, o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada emitem métricas de recursos que podem ser exibidas no portal do Azure ou usando uma destas SQL Server ferramentas de gerenciamento: [Azure Data Studio](/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

O banco de dados SQL do Azure fornece vários consultores de banco de dados para fornecer recomendações de ajuste de desempenho inteligente e opções de ajuste automático para melhorar o desempenho. Além disso, Análise de Desempenho de Consultas mostra detalhes sobre as consultas responsáveis pela maior parte da CPU e do uso de e/s para bancos de dados individuais e em pool.

O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada fornecem recursos avançados de monitoramento e ajuste apoiados por inteligência artificial para ajudá-lo a solucionar problemas e maximizar o desempenho de seus bancos de dados e soluções. Você pode optar por configurar a [exportação de streaming](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) desses [Intelligent insights](intelligent-insights-overview.md) e outros logs de recursos de banco de dados e métricas para um dos vários destinos para consumo e análise, particularmente usando a [análise de SQL](../../azure-monitor/insights/azure-sql.md). O Análise de SQL do Azure é uma solução de monitoramento de nuvem avançada para monitorar o desempenho de todos os seus bancos de dados em escala e em várias assinaturas em uma única exibição. Para obter uma lista dos logs e das métricas que você pode exportar, confira [telemetria de diagnóstico para exportação](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

SQL Server tem seus próprios recursos de monitoramento e diagnóstico que o banco de dados SQL e o SQL Instância Gerenciada utilizam, como [repositório de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) e [DMVs (exibições de gerenciamento dinâmico)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Consulte [monitoramento usando DMVs](monitoring-with-dmvs.md) para ver scripts para monitorar uma variedade de problemas de desempenho.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Recursos de monitoramento e ajuste no portal do Azure

No portal do Azure, o banco de dados SQL do Azure e o SQL Azure Instância Gerenciada fornecem monitoramento de métricas de recursos. O banco de dados SQL do Azure fornece consultores de banco de dados e Análise de Desempenho de Consultas fornece recomendações de ajuste de consulta e análise de desempenho de consulta. No portal do Azure, você pode habilitar o ajuste automático para [servidores SQL lógicos](logical-servers.md) e seus bancos de dados individuais e em pool.

> [!NOTE]
> Os bancos de dados com uso extremamente baixo podem ser mostrados no portal com uso menor que o real. Devido à maneira como a telemetria é emitida ao converter um valor duplo para o inteiro mais próximo, determinados valores de uso menores que 0,5 serão arredondados para 0, o que causa uma perda na granularidade da telemetria emitida. Para obter detalhes, consulte [baixo banco de dados e métricas de pool elástico arredondando para zero](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Monitoramento de recursos do banco de dados SQL do Azure e do Azure SQL Instância Gerenciada

Você pode monitorar rapidamente uma variedade de métricas de recursos no portal do Azure na exibição de **métricas** . Essas métricas permitem que você veja se um banco de dados está atingindo 100% de recursos de processador, memória ou e/s. Alta DTU ou porcentagem de processador, bem como alta porcentagem de e/s, indica que sua carga de trabalho pode precisar de mais recursos de CPU ou de e/s. Ele também pode indicar consultas que precisam ser otimizadas.

  ![Métricas do recurso](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Consultores de banco de dados no banco de dados SQL do Azure

O banco de dados SQL do Azure inclui [consultores de banco de dados](database-advisor-implement-performance-recommendations.md) que fornecem recomendações de ajuste de desempenho para bancos de dados individuais e em pool. Essas recomendações estão disponíveis na portal do Azure, bem como usando o [PowerShell](/powershell/module/az.sql/get-azsqldatabaseadvisor). Você também pode habilitar o [ajuste automático](automatic-tuning-overview.md) para que o banco de dados SQL do Azure possa implementar automaticamente essas recomendações de ajuste.

### <a name="query-performance-insight-in-azure-sql-database"></a>Análise de Desempenho de Consultas no banco de dados SQL do Azure

[Análise de desempenho de consultas](query-performance-insight-use.md) mostra o desempenho na portal do Azure de consultas mais longas e de execução mais longa para bancos de dados individuais e em pool.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>Métricas baixas do banco de dados e do pool elástico arredondando para zero

A partir de setembro de 2020, os bancos de dados com uso extremamente baixo podem ser mostrados no portal com uso menor que o real. Devido à maneira como a telemetria é emitida ao converter um valor duplo para o inteiro mais próximo, determinados valores de uso menores que 0,5 serão arredondados para 0, o que causará uma perda na granularidade da telemetria emitida.

Por exemplo: considere uma janela de 1 minuto com os quatro pontos de dados a seguir: 0,1, 0,1, 0,1, 0,1, esses valores baixos são arredondados para baixo para 0, 0, 0, 0 e apresentam uma média de 0. Se qualquer um dos pontos de dados for maior que 0,5, por exemplo: 0,1, 0,1, 0,9, 0,1, eles serão arredondados para 0, 0, 1, 0 e mostrar uma média de 0,25.

Métricas de banco de dados afetadas:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Métricas do pool elástico afetado:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Gerar avaliações inteligentes de problemas de desempenho

[Intelligent insights](intelligent-insights-overview.md) para banco de dados SQL do Azure e azure SQL instância gerenciada usa inteligência interna para monitorar continuamente o uso do banco de dados por meio de inteligência artificial e detectar eventos de interrupção que causam baixo desempenho. Intelligent Insights detecta automaticamente problemas de desempenho com bancos de dados com base em tempos de espera de execução de consulta, erros ou tempos limite. Depois de detectada, é executada uma análise detalhada que gera um log de recursos (chamado sqlsights) com uma [avaliação inteligente dos problemas](intelligent-insights-troubleshoot-performance.md). Essa avaliação consiste em uma análise da causa raiz do problema de desempenho do banco de dados e possíveis recomendações para melhorias de desempenho.

O Intelligent Insights é uma capacidade exclusiva de inteligência interna do Azure e fornece o seguinte valor:

- Monitoramento proativo
- Insights de desempenho personalizado
- Detecção prévia de degradação de desempenho do banco de dados
- Análise da causa raiz dos problemas detectados
- Recomendações de melhoria de desempenho
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Habilitar a exportação de streaming de métricas e logs de recursos

Você pode habilitar e configurar a [exportação de streaming da telemetria de diagnóstico](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) para um dos vários destinos, incluindo o log de recursos Intelligent insights. Use a [análise de SQL](../../azure-monitor/insights/azure-sql.md) e outros recursos para consumir essa telemetria de diagnóstico adicional para identificar e resolver problemas de desempenho.

Você define as configurações de diagnóstico para as categorias de fluxo de métricas e logs de recursos para bancos de dados individuais, bancos de dados em pool, pools elásticos, instâncias gerenciadas e bancos de dados de instância para um dos recursos do Azure a seguir.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics espaço de trabalho no Azure Monitor

Você pode transmitir métricas e logs de recursos para um [espaço de trabalho log Analytics no Azure monitor](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace). Os dados transmitidos aqui podem ser consumidos pela [análise do SQL](../../azure-monitor/insights/azure-sql.md), que é uma solução de monitoramento somente na nuvem que fornece monitoramento inteligente de seus bancos de dados que inclui relatórios de desempenho, alertas e recomendações de mitigação. Os dados transmitidos para um espaço de trabalho do Log Analytics podem ser analisados com outros dados de monitoramento coletados e também permitem que você aproveite outros recursos do Azure Monitor, como alertas e visualizações.

### <a name="azure-event-hubs"></a>Hubs de eventos do Azure

Você pode transmitir métricas e logs de recursos para os [hubs de eventos do Azure](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs). Streaming de telemetria de diagnóstico para hubs de eventos para fornecer a seguinte funcionalidade:

- **Transmitir logs para registros de terceiros e sistemas de telemetria**

  Transmita todas as suas métricas e logs de recursos para um único Hub de eventos para canalizar dados de log para uma ferramenta de SIEM ou log Analytics de terceiros.
- **Criar uma plataforma de registro em log e telemetria personalizada**

  A natureza de publicação-assinatura altamente escalonável dos hubs de eventos permite que você inscreva com flexibilidade as métricas e os logs de recursos em uma plataforma de telemetria personalizada. Consulte [projetando e dimensionando uma plataforma de telemetria de escala global nos hubs de eventos do Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) para obter detalhes.
- **Exibir a integridade do serviço transmitindo dados para o Power BI**

  Use os hubs de eventos, Stream Analytics e Power BI para transformar seus dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Consulte [Stream Analytics e Power bi: um painel de análise em tempo real para dados de streaming](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para obter detalhes sobre essa solução.

### <a name="azure-storage"></a>Armazenamento do Azure

Transmita métricas e logs de recursos para o [armazenamento do Azure](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage). Use o armazenamento do Azure para arquivar grandes quantidades de telemetria de diagnóstico por uma fração do custo das duas opções de streaming anteriores.

## <a name="use-extended-events"></a>Usar eventos estendidos 

Além disso, você pode usar [eventos estendidos](/sql/relational-databases/extended-events/extended-events) em SQL Server para monitoramento e solução de problemas avançados. A arquitetura de eventos estendidos permite que os usuários coletem tantos ou menos dados necessários para solucionar ou identificar um problema de desempenho. Para obter informações sobre como usar eventos estendidos no banco de dados SQL do Azure, consulte [eventos estendidos no banco de dados SQL do Azure](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre recomendações de desempenho inteligente para bancos de dados individuais e em pool, consulte [recomendações de desempenho do Database Advisor](database-advisor-implement-performance-recommendations.md).
- Para mais informações sobre monitoramento automático do desempenho do banco de dados com diagnóstico automatizado e análise de causa raiz de problemas de desempenho, consulte [Intelligent Insights do SQL Azure](intelligent-insights-overview.md).