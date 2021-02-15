---
title: Monitorar o desempenho do banco de dados com o Intelligent Insights
description: Intelligent Insights no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada usa inteligência interna para monitorar continuamente o uso do banco de dados por meio de inteligência artificial e detectar eventos de interrupção que causam baixo desempenho.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 0f3dce3ca79b12b05325a1d8284dd3304653d5fa
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488860"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligent Insights usando o ia para monitorar e solucionar problemas de desempenho do banco de dados (visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Intelligent Insights no banco de dados SQL do Azure e no SQL Instância Gerenciada do Azure permite que você saiba o que está acontecendo com o desempenho do banco de dados.

O Intelligent Insights usa uma inteligência interna para monitorar continuamente o uso de banco de dados por meio de inteligência artificial e detecta os eventos de interrupção que causam baixo desempenho. Depois de detectada, é executada uma análise detalhada que gera um log de recursos Intelligent Insights (chamado sqlsights) com uma avaliação inteligente do problema. Essa avaliação consiste em uma análise da causa raiz do problema de desempenho do banco de dados e possíveis recomendações para melhorias de desempenho.

## <a name="what-can-intelligent-insights-do-for-you"></a>O que o recurso Intelligent Insights pode fazer por você?

O Intelligent Insights é uma capacidade exclusiva de inteligência interna do Azure e fornece o seguinte valor:

- Monitoramento proativo
- Insights de desempenho personalizado
- Detecção prévia de degradação de desempenho do banco de dados
- Análise da causa raiz dos problemas detectados
- Recomendações de melhoria de desempenho
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="how-does-intelligent-insights-work"></a>Como o Intelligent Insights funciona?

O Intelligent Insights analisa o desempenho do banco de dados comparando a carga de trabalho dele na última hora com a carga de trabalho de linha de base dos últimos sete dias. A carga de trabalho do banco de dados é composta por consultas determinadas a serem mais significativas ao desempenho do banco de dados, como as consultas mais repetidas e maiores. Uma vez que cada banco de dados é exclusivo com base em sua estrutura, data, uso e aplicativo, cada linha de base de carga de trabalho gerada é específica e exclusiva para essa carga de trabalho. O recurso Insights inteligentes, independentemente da linha de base da carga de trabalho, também monitora limites operacionais absolutos e detecta problemas com tempos de espera excessivos, exceções críticas e problemas com parametrizações de consulta que podem afetar o desempenho.

Após a detecção de um problema de degradação do desempenho de várias métricas observadas usando inteligência artificial, a análise é executada. O log de diagnóstico é gerado com um insight inteligente sobre o que está acontecendo com seu banco de dados. O Insights inteligentes facilita o acompanhamento do problema de desempenho do banco de dados, da primeira ocorrência à solução. Cada problema detectado é rastreado em todo o ciclo de vida, da detecção inicial do problema e verificação da melhora de desempenho até sua conclusão.

![Fluxo de trabalho de análise de desempenho do banco de dados](./media/intelligent-insights-overview/intelligent-insights-concept.png)

A métrica usada para medir e detectar problemas de desempenho do banco de dados é baseada na duração da consulta, em solicitações que atingem o tempo limite, tempos de espera excessivo e solicitações com erros. Para obter mais informações sobre métricas, consulte [métricas de detecção](#detection-metrics).

As degradações de desempenho do banco de dados identificadas são registradas no log do sqlsights com entradas inteligentes que consistem nas seguintes propriedades:

| Propriedade | Detalhes |
| :------------------- | ------------------- |
| Informações de banco de dados | Metadados sobre um banco de dados no qual foi detectado um insight, como o URI do recurso. |
| Intervalo de tempo observado | Hora de início e término do período do insight detectado. |
| Métricas afetadas | Métricas que fizeram uma visão mais detalhada ser gerada: <ul><li>Aumento da duração da consulta [segundos].</li><li>Espera excessiva [segundos].</li><li>Solicitações com tempo limite [percentual].</li><li>Solicitações com erro [percentual].</li></ul>|
| Valor do impacto | Valor de uma métrica medida. |
| Consultas afetadas e códigos de erro | Hash de consulta ou código de erro. Podem ser usados para correlacionar facilmente a consultas afetadas. São fornecidas métricas que consistem em aumento de duração da consulta, tempo de espera, contagens de tempo limite ou códigos de erro. |
| Detecções | Detecção identificada no banco de dados durante o tempo de um evento. Há 15 padrões de detecção. Para obter mais informações, consulte [Solucionar problemas de desempenho do banco de dados com o Insights inteligentes](intelligent-insights-troubleshoot-performance.md). |
| Análise da causa raiz | Análise da causa raiz do problema identificado em um formato legível por humanos. Alguns insights podem conter recomendação de melhoria de desempenho sempre que possível. |
|||

Para obter uma visão geral prática sobre como usar Intelligent Insights com Análise de SQL do Azure e para cenários de uso típicos, consulte este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights se destaca na descoberta e solução de problemas de desempenho do banco de dados. Para usar Intelligent Insights para solucionar problemas de desempenho do banco de dados, consulte [solucionar problemas de desempenho com Intelligent insights](intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Opções de Intelligent Insights

Intelligent Insights opções disponíveis são:

| Opção Intelligent Insights | Suporte para Banco de Dados SQL do Azure | Suporte às instâncias gerenciadas do Azure SQL |
| :----------------------------- | ----- | ----- |
| **Configurar Intelligent insights** -configurar a análise de Intelligent insights para seus bancos de dados. | Sim | Sim |
| **Transmita informações para análise de SQL do Azure** --Stream insights para análise de SQL do Azure. | Sim | Sim |
| **Transmita informações para os hubs de eventos do Azure** – Transmita informações para os hubs de eventos para obter mais integrações personalizadas. | Sim | Sim |
| **Transmita informações para o armazenamento do Azure** -Stream insights para o armazenamento do Azure para análise adicional e arquivamento de longo prazo. | Sim | Sim |

> [!NOTE]
> O insights inteligentes é um recurso de visualização, não disponível nas seguintes regiões: Europa Ocidental, Europa Setentrional, oeste dos EUA 1 e leste dos EUA 1.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Configurar a exportação do log de Intelligent Insights

A saída do Intelligent Insights pode ser transmitida para um dos vários destinos para análise:

- A saída transmitida para um espaço de trabalho Log Analytics pode ser usada com [análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md) para exibir informações por meio da interface do usuário do portal do Azure. Esta é a solução do Azure integrada e a maneira mais comum de exibir os insights.
- A saída transmitida para os hubs de eventos do Azure pode ser usada para o desenvolvimento de cenários de monitoramento e alerta personalizados
- A saída transmitida para o armazenamento do Azure pode ser usada para desenvolvimento de aplicativos personalizados, como, por exemplo, relatórios personalizados, arquivamento de dados de longo prazo e assim por diante.

A integração do Análise de SQL do Azure, dos hubs de eventos do Azure, do armazenamento do Azure ou de produtos de terceiros para consumo é realizada pela primeira vez que habilita o registro em log de Intelligent Insights (o log "sqlsights") na folha configurações de diagnóstico de um banco de dados e, em seguida, a configuração de Intelligent Insights log para serem transmitidos em um desses destinos.

Para obter mais informações sobre como habilitar o log de Intelligent Insights e configurar a métrica e os dados de log de recursos a serem transmitidos para um produto de consumo, consulte [log de métricas e de diagnóstico](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

### <a name="set-up-with-azure-sql-analytics"></a>Configuração com a Análise de SQL do Azure

A solução Análise de SQL do Azure fornece recursos de interface gráfica do usuário, relatórios e alertas sobre o desempenho do banco de dados, usando a Intelligent Insights de log de recursos.

Adicione Análise de SQL do Azure ao seu painel de portal do Azure do Marketplace e para criar um espaço de trabalho, consulte [configurar análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md#configuration)

Para usar Intelligent Insights com Análise de SQL do Azure, configure Intelligent Insights dados de log a serem transmitidos para Análise de SQL do Azure espaço de trabalho que você criou na etapa anterior, consulte [log de diagnóstico e métricas](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

O exemplo a seguir mostra um Intelligent Insights exibido por meio da Análise de SQL do Azure:

![Relatório de Insights inteligentes](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Configuração com os Hubs de Eventos

Para usar Intelligent Insights com os hubs de eventos, configure Intelligent Insights dados de log a serem transmitidos para os hubs de eventos, consulte [log de métricas e diagnósticos](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) e [transmita logs de diagnóstico do Azure para os hubs de eventos](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

Para usar os hubs de eventos para configurar o monitoramento e alertas personalizados, consulte [o que fazer com métricas e logs de diagnóstico em hubs de eventos](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Configuração com o Armazenamento do Microsoft Azure

Para usar Intelligent Insights com armazenamento, configure Intelligent Insights dados de log a serem transmitidos para armazenamento, consulte [log de diagnóstico e métricas](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) e [fluxo no armazenamento do Azure](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Integrações personalizadas de log do Insights inteligentes

Para usar Intelligent Insights com ferramentas de terceiros ou para o desenvolvimento de alertas personalizados e monitoramento, consulte [usar o log de diagnóstico de desempenho do banco de dados Intelligent insights](intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Métricas de detecção

Métricas usadas para modelos de detecção que geram insights inteligentes são baseadas em monitoramento:

- Duração da consulta
- Solicitações de tempo limite
- Tempo de espera excessivo
- Solicitações com erro

Solicitações de duração e tempo limite de consulta são usadas como modelos principais na detecção de problemas de desempenho de carga de trabalho de banco de dados. Elas são usadas porque medem diretamente o que está acontecendo com a carga de trabalho. Para detectar todos os casos possíveis de degradação do desempenho de carga de trabalho, tempo de espera excessivo e solicitações com erros são usados como modelos adicionais para indicar problemas que afetam o desempenho da carga de trabalho.

O sistema automaticamente considera alterações na carga de trabalho e no número de solicitações de consulta feitas ao banco de dados para determinar dinamicamente os limites normais e fora do comum de desempenho do banco de dados.

Todas as métricas são consideradas em conjunto em vários relacionamentos por meio de um modelo de dados derivado cientificamente que categoriza cada problema de desempenho detectado. As informações fornecidas por meio de um insight inteligente incluem:

- Detalhes do problema de desempenho detectado.
- Uma análise da causa raiz do problema detectado.
- Recomendações sobre como melhorar o desempenho do banco de dados monitorado, sempre que possível.

## <a name="query-duration"></a>Duração da consulta

O modelo de degradação da duração da consulta analisa consultas individuais e detecta o aumento no tempo necessário para compilar e executar uma consulta em comparação à linha de base de desempenho.

Se a inteligência interna detectar um aumento significativo na compilação da consulta ou no tempo de execução da consulta que afeta o desempenho da carga de trabalho, essas consultas serão sinalizadas como problemas de degradação do desempenho da duração da consulta.

O log de diagnóstico do Intelligent Insights gera o hash de consulta da consulta com degradação de desempenho. O hash de consulta indica se a degradação do desempenho foi relacionada a um aumento no tempo de execução ou compilação de consulta, que aumentou o tempo de duração da consulta.

## <a name="timeout-requests"></a>Solicitações de tempo limite

O modelo de degradação das solicitações de tempo limite analisa consultas individuais e detecta qualquer aumento nos tempos limite no nível de execução da consulta e nos limites de solicitação geral no nível do banco de dados em comparação ao período de linha de base de desempenho.

Algumas das consultas podem atingir o tempo limite até mesmo antes de atingirem o estágio de execução. Por meio de trabalhadores anulados versus solicitações feitas, a inteligência interna mede e analisa todas as consultas que atingiram o banco de dados, independentemente de terem sido realizadas no estágio de execução ou não.

Depois que o número de tempos limite das consultas executadas ou o número de trabalhadores com solicitação anuladas cruza o limite gerenciado do sistema, um log de diagnóstico é preenchido com insights inteligentes.

Os insights gerados contêm o número de solicitações que atingiram o tempo limite e o número de consultas que atingiram o tempo limite. A indicação da degradação de desempenho está relacionada ao aumento de tempo limite no estágio de execução ou ao nível de banco de dados geral fornecido. Quando o aumento nos tempos limite é considerado significativo para o desempenho do banco de dados, essas consultas são sinalizadas como problemas de degradação de desempenho de tempo limite.

## <a name="excessive-wait-times"></a>Tempos excessivos de espera

O modelo de tempo de espera excessivo monitora as consultas de banco de dados individuais. Ele detecta estatísticas de espera de consulta incomumente altas que cruzaram os limites absolutos gerenciado pelo sistema. As métricas de tempo de espera excessivas de consulta a seguir são observadas usando o, [repositório de consultas estatísticas de espera (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql):

- Atingindo os limites do recurso
- Atingindo os limites de recurso do pool elástico
- Número excessivo de threads de trabalho ou sessão
- Bloqueio excessivo do banco de dados
- Demanda de memória
- Outras estatísticas de espera

Atingir os limites de recurso ou os limites de recursos do pool elástico denota que o consumo de recursos disponíveis em uma assinatura ou no pool elástico cruzou limites absolutos. Essas estatísticas indicam degradação do desempenho de carga de trabalho. Um número excessivo de threads de trabalho ou sessão denota uma condição em que o número de threads de trabalho ou sessões iniciadas cruzou os limites absolutos. Essas estatísticas indicam degradação do desempenho de carga de trabalho.

Bloqueio de banco de dados excessivo indica uma condição em que a contagem de bloqueios em um banco de dados cruzou os limites absolutos. Esta estatística indica uma degradação de desempenho da carga de trabalho. A pressão de memória é uma condição em que o número de threads solicitando concessões de memória cruzou um limite absoluto. Esta estatística indica uma degradação de desempenho da carga de trabalho.

A detecção de outras estatísticas de espera indica uma condição em que métricas diversas medidas por meio das Estatísticas de Espera de Repositório de Consultas cruzaram um limite absoluto. Essas estatísticas indicam degradação do desempenho de carga de trabalho.

Depois que forem detectados tempos de espera excessivo, dependendo dos dados disponíveis, o log de diagnóstico de Insights inteligentes produz hashes das consultas afetadas e que afetam o sistema degradadas no desempenho, dos detalhes das métricas que fazem as consultas esperarem em execução e do tempo de espera medido.

## <a name="errored-requests"></a>Solicitações com erros

O modelo de degradação de solicitações com erros monitora consultas individuais e detecta um aumento no número de consultas que apresentaram erros em comparação com o período da linha de base. Esse modelo também monitora as exceções críticas que cruzam os limites absolutos gerenciados pela inteligência interna. O sistema automaticamente considera o número de solicitações de consulta feitas ao banco de dados e considera quaisquer alterações de carga de trabalho no período monitorado.

Quando o aumento medido em solicitações com erros em relação o número total de solicitações feitas é considerado significativo para o desempenho da carga de trabalho, as consultas afetadas são sinalizadas como problemas de degradação do desempenho de solicitações com erros.

O log do Intelligent Insights gera a contagem de solicitações com erros. Indica se a degradação do desempenho estava relacionada a um aumento nas solicitações com erros ou a cruzar um limite de exceção crítico monitorado e tempo medido da degradação do desempenho.

Se qualquer uma das exceções críticas monitoradas cruzar os limites absolutos gerenciados pelo sistema, um insight inteligente será gerado com detalhes da exceção crítica.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [monitorar bancos de dados usando a análise de SQL](../../azure-monitor/insights/azure-sql.md).
- Saiba como [solucionar problemas de desempenho com o Intelligent insights](intelligent-insights-troubleshoot-performance.md).