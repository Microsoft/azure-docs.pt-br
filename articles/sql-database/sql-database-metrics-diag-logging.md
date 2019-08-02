---
title: Métricas de banco de dados SQL do Azure e log de diagnósticos | Microsoft Docs
description: Saiba como habilitar o diagnóstico no banco de dados SQL Azure para armazenar informações sobre a utilização de recursos e as estatísticas de execução de consulta.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 05/21/2019
ms.openlocfilehash: 6824a7151a0c007d6fe4ba021f274886a3cf0dcb
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447818"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas de banco de dados SQL do Azure e o log de diagnóstico

Neste tópico, você aprenderá como configurar logs de telemetria de diagnóstico para o banco de dados SQL por meio do portal do Azure, PowerShell, CLI do Azure, API de REST do Azure Monitor e modelo do Resource Manager. Estes diagnósticos podem ser usados para medir a utilização de recursos e as estatísticas de execução de consulta.

Bancos de dados individuais, bancos de dados em pool em pools elásticos e bancos de dados de instância em uma instância gerenciada podem transmitir logs de diagnóstico e métricas para facilitar o monitoramento de desempenho. Você pode configurar um banco de dados para transmitir o uso de recursos, trabalhos e sessões e conectividade para um dos seguintes recursos do Azure:

- **Análise de SQL do Azure**: para obter monitoramento inteligente de seus bancos de dados SQL do Azure que inclui relatórios de desempenho, alertas e recomendações de mitigação.
- **Hubs de Eventos do Azure**: para integrar telemetria de Banco de Dados SQL com suas soluções de monitoramento personalizadas ou pipelines ativos.
- **Armazenamento do Microsoft Azure**: para arquivar grandes quantidades de telemetria por uma fração do preço.

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

Para obter mais informações sobre as categorias de log e métricas com suporte a vários serviços do Azure, veja:

- [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Visão geral do log de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Este artigo apresenta diretrizes para ajudá-lo a habilitar a telemetria de diagnóstico para bancos de dados SQL do Azure, pools elásticos e instâncias gerenciadas. Ele também pode ajudar você entender como configurar a Análise de SQL do Azure como uma ferramenta de monitoramento para exibir a telemetria de diagnóstico do banco de dados.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Habilitar o log de telemetria de diagnóstico

É possível habilitar e gerenciar as métricas e o log de diagnósticos usando um dos seguintes métodos:

- Portal do Azure
- PowerShell
- CLI do Azure
- API REST do Azure Monitor
- Modelo do Azure Resource Manager

Quando você habilita o log de diagnóstico e métricas, você precisa especificar o destino de recursos do Azure para coletar a telemetria de diagnóstico. As opções disponíveis incluem:

- Azure SQL Analytics
- Hubs de eventos do Azure
- Armazenamento do Azure

Você pode provisionar um novo recurso do Azure ou selecionar um recurso existente. Depois de escolher um recurso usando a opção **Configurações de diagnóstico**, especifique quais dados coletar.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Suporte para o log de diagnóstico para bancos de dados SQL do Azure e bancos de dados de instância

Habilitar o registro em log de métricas e diagnóstico em bancos de dados SQL – não estão habilitadas por padrão.

Você pode configurar bancos de dados SQL do Azure e bancos de dados de instância para coletar a telemetria de diagnóstico a seguir:

| Telemetria de monitoramento para bancos de dados | Suporte a banco de dados individual e banco de dados em pool | Suporte de banco de dados de instância |
| :------------------- | ----- | ----- |
| [As métricas básicas](#basic-metrics): contém o percentual de DTU/CPU, o limite de DTU/CPU, o percentual de leitura de dados físicos, o percentual de gravação em log, êxito/falha/bloqueio por conexões de firewall, o percentual de sessões, o percentual de funcionários, o armazenamento, o percentual de armazenamento e o percentual de armazenamento XTP. | Sim | Não |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): contém informações sobre as estatísticas de tempo de execução da consulta, como estatísticas de uso de CPU e duração da consulta. | Sim | Sim |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Contém informações sobre as estatísticas de espera de consulta (o que suas consultas aguardaram) como são CPU, LOG e LOCKING. | Sim | Sim |
| [Erros](#errors-dataset): Contém informações sobre erros do SQL em um banco de dados. | Sim | Sim |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Contém informações sobre quanto tempo um banco de dados gasta aguardando a tipos diferentes de espera. | Sim | Não |
| [Timeouts](#time-outs-dataset): Contém informações sobre tempos limite em um banco de dados. | Sim | Não |
| [Blocks](#blockings-dataset): Contém informações sobre eventos em um banco de dados de bloqueio. | Sim | Não |
| [Deadlocks](#deadlocks-dataset): Contém informações sobre eventos de deadlock em um banco de dados. | Sim | Não |
| [AutomaticTuning](#automatic-tuning-dataset): Contém informações sobre recomendações de ajuste automático para um banco de dados. | Sim | Não |
| [SQLInsights](#intelligent-insights-dataset): Contém o Intelligent Insights sobre desempenho para um banco de dados. Para saber mais, veja [Intelligent Insights](sql-database-intelligent-insights.md). | Sim | Sim |

> [!IMPORTANT]
> Pools Elásticos e instâncias gerenciadas têm sua própria telemetria de diagnóstico separada de bancos de dados que eles contêm. Isso é importante observar que a telemetria de diagnóstico é configurada separadamente para cada um desses recursos, como documentado abaixo.

> [!NOTE]
> Logs de auditoria de segurança e SQLSecurityAuditEvents não podem ser habilitados nas configurações de diagnóstico do banco de dados (embora mostrando na tela). Para habilitar o streaming de log de auditoria, consulte [configurar a auditoria do banco de dados](sql-database-auditing.md#subheading-2), e [logs nos logs de Monitor do Azure e Hubs de eventos de auditoria](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>Portal do Azure

Você pode usar o **as configurações de diagnóstico** menu para cada um único, colocado em pool, ou instância de banco de dados no portal do Azure para configurar a transmissão de telemetria de diagnóstico. Além disso, telemetria de diagnóstico também pode ser configurada separadamente para contêineres de banco de dados: pools Elásticos e instâncias gerenciadas. Você pode definir os destinos a seguir para transmitir a telemetria de diagnóstico: Logs do Azure, armazenamento, Hubs de eventos do Azure e Azure Monitor.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurar a transmissão de telemetria de diagnóstico para pools elásticos

   ![Ícone de pool Elástico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Você pode configurar um recurso de pool elástico para coletar a telemetria de diagnóstico a seguir:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Pool elástico** | [As métricas básicas](sql-database-metrics-diag-logging.md#basic-metrics) contém o percentual de eDTU/CPU, o limite de eDTU/CPU, a física dados percentual de leitura, gravação de log, percentual, porcentagem de sessões, porcentagem de funcionários, armazenamento, porcentagem de armazenamento, limite de armazenamento e percentual de armazenamento XTP. |

Para configurar a transmissão de telemetria de diagnóstico para pools Elásticos e bancos de dados em pools Elásticos, você precisará configurar separadamente **ambos** das seguintes opções:

- Habilitar o streaming de telemetria de diagnóstico para um pool Elástico, **e**
- Habilitar o streaming de telemetria de diagnóstico para cada banco de dados no pool Elástico

Isso ocorre porque o pool Elástico é um contêiner de banco de dados com sua própria telemetria que está sendo separada da telemetria de um banco de dados individuais.

Para habilitar o streaming de telemetria de diagnóstico para recursos de um pool elástico, siga estas etapas:

1. Vá para o **pool Elástico** recurso no portal do Azure.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para pools elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar na conta de armazenamento**, **Transmitir para um hub de eventos** e **Enviar para Log Analytics**.
1. Para o log analytics, selecione **configurar** e crie um novo espaço de trabalho selecionando **+ criar novo espaço de trabalho**, ou selecione um espaço de trabalho.
1. Marque a caixa de seleção para telemetria de diagnóstico de pool elástico: **Básico** métricas.
   ![Configurar o diagnóstico para pools Elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Clique em **Salvar**.
1. Além disso, configure a transmissão de telemetria de diagnóstico para cada banco de dados dentro do pool Elástico que você deseja monitorar seguindo as etapas descritas na próxima seção.

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico para um pool Elástico, você também precisa configurar a telemetria de diagnóstico para cada banco de dados no pool Elástico, como documentado abaixo.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Configurar a transmissão de telemetria de diagnóstico para um único banco de dados ou banco de dados no pool Elástico

   ![Ícone do Banco de Dados SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Para habilitar o streaming de telemetria de diagnóstico para bancos de dados individual ou em pool, siga estas etapas:

1. Vá para o Azure **banco de dados SQL** recursos.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.
   - Você pode criar até três conexões paralelas para transmitir telemetria de diagnóstico.
   - Selecione **+Adicionar configuração de diagnóstico** para configurar o streaming de dados de diagnóstico para vários recursos.

   ![Habilitar o diagnóstico de bancos de dados de instância, individuais ou em pool](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar na conta de armazenamento**, **Transmitir para um hub de eventos** e **Enviar para Log Analytics**.
1. Para obter a experiência de monitoramento padrão baseada em eventos, marque as seguintes caixas de seleção para telemetria de log de diagnóstico do banco de dados: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks** e **Deadlocks**.
1. Para obter uma experiência de monitoramento avançada, com base em minutos um, selecione a caixa de seleção **básica** métricas.
   ![Configurar o diagnóstico de um único, em pool ou bancos de dados da instância](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Clique em **Salvar**.
1. Repita essas etapas para cada banco de dados que você deseja monitorar.

> [!NOTE]
> Logs de auditoria de segurança e SQLSecurityAuditEvents não podem ser habilitados nas configurações de diagnóstico do banco de dados (embora mostrados na tela). Para habilitar o streaming de log de auditoria, consulte [configurar a auditoria do banco de dados](sql-database-auditing.md#subheading-2), e [logs nos logs de Monitor do Azure e Hubs de eventos de auditoria](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).
> [!TIP]
> Repita essas etapas para cada Banco de Dados SQL do Azure que você deseja monitorar.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Configurar a transmissão de telemetria de diagnóstico para as instâncias gerenciadas

   ![Ícone da Instância Gerenciada](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Você pode configurar um recurso de instância gerenciada para coletar a telemetria de diagnóstico a seguir:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Instância gerenciada** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) contém a contagem de vCores, o percentual médio de CPU, as solicitações de E/S, os bytes lidos/gravados, o espaço de armazenamento reservado e o espaço de armazenamento usado. |

Para configurar a transmissão de telemetria de diagnóstico para a instância gerenciada e bancos de dados de instância, você precisará configurar separadamente **ambos** das seguintes opções:

- Habilitar o streaming de telemetria de diagnóstico para a instância gerenciada, **e**
- Habilitar o streaming de telemetria de diagnóstico para cada instância de banco de dados

Isso ocorre porque a instância gerenciada é um contêiner de banco de dados com sua própria telemetria, separado da telemetria de banco de dados instância individual.

Para habilitar o streaming de telemetria de diagnóstico para um recurso de instância gerenciada, siga estas etapas:

1. Vá para o **instância gerenciada** recurso no portal do Azure.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para a instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar na conta de armazenamento**, **Transmitir para um hub de eventos** e **Enviar para Log Analytics**.
1. Para o log analytics, selecione **configurar** e crie um novo espaço de trabalho selecionando **+ criar novo espaço de trabalho**, ou usar um espaço de trabalho.
1. Marque a caixa de seleção para telemetria de diagnóstico de instância: **ResourceUsageStats**.
   ![Configurar o diagnóstico para a instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Clique em **Salvar**.
1. Além disso, configure a transmissão de telemetria de diagnóstico para cada instância de banco de dados dentro da instância gerenciada que você deseja monitorar, seguindo as etapas descritas na próxima seção.

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico para uma instância gerenciada, você também precisa configurar a telemetria de diagnóstico para cada banco de dados de instância, como documentado abaixo.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Configurar a transmissão de telemetria de diagnóstico por exemplo, bancos de dados

   ![Ícone de banco de dados de instância na instância gerenciada](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Para habilitar o streaming de telemetria de diagnóstico por exemplo, bancos de dados, siga estas etapas:

1. Vá para **banco de dados de instância** recurso na instância gerenciada.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.
   - Você pode criar até três (3) conexões paralelas para transmitir telemetria de diagnóstico.
   - Selecione **+Adicionar configuração de diagnóstico** para configurar o streaming de dados de diagnóstico para vários recursos.

   ![Habilitar o diagnóstico de bancos de dados de instância](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar na conta de armazenamento**, **Transmitir para um hub de eventos** e **Enviar para Log Analytics**.
1. Marque as caixas de seleção para a telemetria de diagnóstico do banco de dados: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** e **Errors**.
   ![Configurar o diagnóstico, por exemplo bancos de dados](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Clique em **Salvar**.
1. Repita essas etapas para cada instância de banco de dados que você deseja monitorar.

> [!TIP]
> Repita essas etapas para cada instância de banco de dados que você deseja monitorar.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Você pode habilitar o log de diagnóstico e métricas usando o PowerShell.

- Para habilitar o armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da conta de armazenamento é a ID do recurso para a conta de armazenamento de destino.

- Para habilitar o streaming dos logs de diagnóstico para um hub de eventos, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   A ID da regra do Barramento de Serviço do Azure é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar o envio de logs de diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- É possível obter a ID de recurso do espaço de trabalho do Log Analytics usando o seguinte comando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="to-configure-multiple-azure-resources"></a>Configurar vários recursos do Azure

Para dar suporte a várias assinaturas, use o script do PowerShell de [Habilitar log de métricas de recursos do Azure usando o PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Insira a ID de recurso do workspace \<$WSID\> como um parâmetro ao executar o script `Enable-AzureRMDiagnostics.ps1` para enviar dados de diagnóstico de diversos recursos para o workspace.

- Para obter a ID do workspace \<$WSID\> de destino para seus dados de diagnóstico, use o seguinte script:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   Substitua \<subID\> pela ID de assinatura \<RG_NAME\> com o nome do grupo de recursos e \<WS_NAME\> com o nome do workspace.

### <a name="azure-cli"></a>CLI do Azure

Você pode habilitar o log de diagnóstico e métricas usando a CLI do Azure.

> [!NOTE]
> Scripts para habilitar o log de diagnóstico têm suporte para a versão 1.0 da CLI do Azure. Observe que versão da CLI 2.0 não tem suporte no momento.

- Para habilitar armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A ID da conta de armazenamento é a ID do recurso para a conta de armazenamento de destino.

- Para habilitar streaming dos logs de diagnóstico para um hub de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A ID da regra do Barramento de Serviço é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar envio dos logs de diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba como [alterar as configurações de diagnóstico usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [habilitar as configurações de diagnóstico na criação de recursos usando um modelo do Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Stream em Análise de SQL do Azure

A Análise de SQL do Azure é uma solução de nuvem que monitora o desempenho de bancos de dados SQL do Azure, de pools elásticos e das instâncias gerenciadas em escala e em várias assinaturas. Pode ajudá-lo a coletar e visualizar as métricas de desempenho do Banco de Dados SQL do Azure e tem uma inteligência interna para solução de problemas de desempenho.

![Visão geral da Análise de SQL do Azure](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Logs de diagnóstico e métricas de banco de dados SQL podem ser transmitidos para Análise de SQL do Azure usando o interno **enviar para Log Analytics** opção na guia de configurações de diagnóstico no portal. Você também pode habilitar o log analytics por meio de uma configuração de diagnóstico por meio de cmdlets do PowerShell, a CLI do Azure ou a API REST do Azure Monitor.

### <a name="installation-overview"></a>Visão geral da instalação

Você pode monitorar uma frota de Bancos de Dados SQL com a Análise de SQL do Azure. Execute as seguintes etapas:

1. Criar uma solução de Análise de SQL do Azure usando o Azure Marketplace.
2. Criar workspace de monitoramento na solução.
3. Configurar bancos de dados para transmitir telemetria de diagnóstico para o workspace.

Se você estiver usando pools elásticos ou instâncias gerenciadas, também precisará configurar a telemetria de diagnóstico de streaming desses recursos.

### <a name="create-azure-sql-analytics-resource"></a>Criar recurso de Análise de SQL do Azure

1. Pesquise a Análise de SQL do Azure no Azure Marketplace e selecione-o.

   ![Pesquisar para Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selecione **Criar** na tela de visão geral da solução.

3. Preencha o formulário de Análise de SQL do Azure com as informações adicionais necessárias: nome do workspace, assinatura, grupo de recursos, local e tipo de preço.

   ![Configurar Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selecione **OK** para confirmar e, em seguida, selecione **Criar**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurar bancos de dados para gravar logs de diagnóstico e métricas

É a maneira mais fácil de configurar onde os bancos de dados registram as métricas por meio do portal do Azure. Conforme descrito anteriormente, vá até o recurso de Banco de Dados SQL no portal do Azure e selecione **Configurações de diagnóstico**.

Se você estiver usando pools elásticos ou instância gerenciada, também precisará definir as configurações de diagnóstico nestes recursos para habilitar a telemetria de diagnóstico para transmitir para o workspace.

### <a name="use-the-sql-analytics-solution"></a>Use a solução Análise de SQL

Você pode usar a Análise do SQL como um painel hierárquico para exibir os recursos de Banco de Dados SQL. Para saber como usar a solução de Análise de SQL, consulte [Monitorar o Banco de Dados SQL usando a solução de Análise de SQL](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Transmitir para os Hubs de Eventos

Você pode transmitir métricas e logs de diagnóstico do Banco de Dados SQL para Hubs de Eventos usando a opção interna **Transmitir para um hub de eventos** no portal do Azure. Também é possível habilitar a ID de regra do Barramento de Serviço usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>O que fazer com as métricas e os logs de diagnóstico nos Hubs de Eventos

Depois que os dados selecionados são transmitidos para os Hubs de Eventos, os habilitar cenários de monitoramento avançado estarão mais próximos. Os Hubs de Eventos atuam como uma porta da frente para um pipeline de eventos. Depois que os dados são coletados para um hub de eventos, eles podem ser transformados e armazenados usando um provedor de análise em tempo real ou um adaptador de armazenamento. Os Hubs de Eventos separam a produção de um fluxo de eventos do consumo desses eventos. Dessa forma, os consumidores de evento podem acessá-los em suas próprias agendas. Para obter mais informações sobre os Hubs de Eventos, consulte:

- [O que são Hubs de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Você pode usar métricas de streaming em Hubs de Eventos para:

- **Exibir a integridade do serviço transmitindo dados de caminhos recorrentes ao Power BI**. Ao utilizar os Hubs de Eventos, o Stream Analytics e o Power BI, é fácil transformar suas métricas e dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Para obter uma visão geral de como configurar um hub de eventos, processar dados com o Stream Analytics e usar o Power BI como uma saída, consulte [Stream Analytics e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Transmitir logs para registros de terceiros e fluxos de telemetria**. Ao utilizar a transmissão dos Hubs de Eventos, é possível obter métricas e logs de diagnóstico em diversas soluções de monitoramento de terceiros e análise de logs.

- **Crie uma plataforma personalizada de registro em log e telemetria**. Fazer você já tem uma plataforma de telemetria personalizada ou está pensando em criar uma? A natureza altamente escalonável de publicação-assinatura de Hubs de Eventos permite flexibilidade para ingerir logs de diagnóstico. Consulte [o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmitir para o Armazenamento

Você pode armazenar métricas de Banco de Dados SQL e logs de diagnóstico no Armazenamento do Azure usando a opção interna **Arquivar em uma conta de armazenamento** no portal do Azure. Também é possível habilitar o Armazenamento usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Esquema de métricas e logs de diagnóstico na conta de armazenamento

Depois que você configurar a coleta de métricas e logs de diagnóstico, um contêiner de armazenamento é criado na conta de armazenamento selecionada quando as primeiras linhas de dados estão disponíveis. A estrutura dos blobs é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ou, simplesmente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, pode ser um nome de blob para métricas básicas:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Um nome de blob para armazenar dados de um pool elástico se parece com:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Política de retenção de dados e preços

Ao selecionar os Hubs de Eventos ou uma conta de Armazenamento, é possível especificar uma política de retenção. Essa política exclui dados mais antigos que um período de tempo selecionado. Se você especificar a análise de Log, a política de retenção depende do tipo de preço selecionado. Nesse caso, as unidades gratuitas de ingestão de dados fornecidas podem habilitar o monitoramento gratuito de vários bancos de dados por mês. Qualquer consumo de telemetria de diagnóstico que exceda as unidades gratuitas poderá gerar custos. Lembre-se de que os bancos de dados ativos com cargas de trabalho mais pesadas ingerem mais dados do que bancos de dados ociosos. Para obter mais informações, consulte [preços do Log analytics](https://azure.microsoft.com/pricing/details/monitor/).

Se você estiver usando a Análise de SQL do Azure, poderá monitorar o consumo de ingestão de dados na solução selecionando o **Workspace OMS** no menu de navegação da Análise de SQL do Azure e, em seguida, selecionando **Uso** e **Custos Estimados**.

## <a name="metrics-and-logs-available"></a>Métricas e logs disponíveis

Monitorando telemetria disponível para o banco de dados SQL, pools Elásticos e a instância gerenciada está documentada abaixo. Telemetria coletada de monitoramento dentro de análise de SQL pode ser usada para suas próprias análises personalizadas e desenvolvimento de aplicativo usando [consultas de log do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) idioma.

## <a name="basic-metrics"></a>Métricas básicas

Consulte as tabelas a seguir para obter detalhes sobre as métricas básicas por recurso.

> [!NOTE]
> Opção de métricas básicas era conhecida anteriormente como todas as métricas. A alteração feita à nomeação apenas e não há nenhuma alteração para as métricas monitoradas. Essa alteração foi iniciada para permitir a introdução das categorias de métrica adicionais no futuro.

### <a name="basic-metrics-for-elastic-pools"></a>Métricas básicas para pools Elásticos

|**Recurso**|**Métricas**|
|---|---|
|Pool elástico|O percentual de eDTU, eDTU usado, limite de eDTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, limite de armazenamento, percentual de armazenamento XTP |

### <a name="basic-metrics-for-azure-sql-databases"></a>Métricas básicas para bancos de dados SQL do Azure

|**Recurso**|**Métricas**|
|---|---|
|Banco de Dados SQL do Azure|O percentual de DTU, DTU usado, o limite de DTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, êxito/falha/bloqueio por conexões de firewall, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, percentual de armazenamento XTP e deadlocks |

## <a name="basic-logs"></a>Logs básicos

Detalhes da telemetria disponível para todos os logs são documentadas nas tabelas a seguir. Consulte [suporte para o log de diagnóstico](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) para compreender os logs que têm suporte para um tipo específico de banco de dados - SQL do Azure único, colocado em pool, ou na instância do banco de dados.

### <a name="resource-usage-stats-for-managed-instance"></a>Estatísticas de uso de recursos para a instância gerenciada

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: ResourceUsageStats |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: MANAGEDINSTANCES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome da instância gerenciada |
|ResourceId|URI de recurso |
|SKU_s|SKU do produto da instância gerenciada |
|virtual_core_count_s|Número de vCores disponíveis |
|avg_cpu_percent_s|Percentual médio de CPU |
|reserved_storage_mb_s|Capacidade de armazenamento reservada na instância gerenciada |
|storage_space_used_mb_s|Armazenamento usado na instância gerenciada |
|io_requests_s|Contagem de IOPS |
|io_bytes_read_s|Bytes de IOPS lidos |
|io_bytes_written_s|Bytes de IOPS gravados |

### <a name="query-store-runtime-statistics"></a>Estatísticas de tempo de execução do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|query_hash_s|Hash de consulta |
|query_plan_hash_s|Hash do plano de consulta |
|statement_sql_handle_s|Identificador de sql da instrução |
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|logical_io_writes_d|Número total de gravações lógicas de E/S |
|max_logical_io_writes_d|Número máximo de gravações lógicas de E/S por execução |
|physical_io_reads_d|Número total de leituras físicas de E/S |
|max_physical_io_reads_d|Número máximo de leituras lógicas de E/S por execução |
|logical_io_reads_d|Número total de leituras lógicas de E/S |
|max_logical_io_reads_d|Número máximo de leituras lógicas de E/S por execução |
|execution_type_d|Tipo de execução |
|count_executions_d|Número de execuções da consulta |
|cpu_time_d|Tempo total de CPU consumido pela consulta em microssegundos |
|max_cpu_time_d|Tempo máximo de CPU do consumidor por uma execução única em microssegundos |
|dop_d|Soma de graus de paralelismo |
|max_dop_d|Grau máximo de paralelismo usado para execução única |
|rowcount_d|Número total de linhas retornadas |
|max_rowcount_d|Número máximo de linhas retornadas em uma única execução |
|query_max_used_memory_d|Quantidade total de memória usada em KB |
|max_query_max_used_memory_d|Quantidade máxima de memória usada por uma única execução em KB |
|duration_d|Tempo total de execução em microssegundos |
|max_duration_d|Tempo máximo de execução de uma única execução |
|num_physical_io_reads_d|Número total de leituras físicas |
|max_num_physical_io_reads_d|Número máximo de leituras físicas por execução |
|log_bytes_used_d|Quantidade total de bytes de log usados |
|max_log_bytes_used_d|Quantidade máxima de bytes de log usados por execução |
|query_id_d|ID da consulta no Repositório de Consultas |
|plan_id_d|ID do plano no Repositório de Consultas |

Saiba mais sobre os [Dados de estatísticas de tempo de execução do Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Estatísticas de espera do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreWaitStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreWaitStatisticsEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|wait_category_s|Categoria da espera |
|is_parameterizable_s|É a consulta parametrizável |
|statement_type_s|Tipo da instrução |
|statement_key_hash_s|Hash de chave de instrução |
|exec_type_d|Tipo de execução |
|total_query_wait_time_ms_d|Tempo total de espera da consulta na categoria de espera específica |
|max_query_wait_time_ms_d|Tempo máximo de espera da consulta em execução individual na categoria de espera específica |
|query_param_type_d|0 |
|query_hash_s|Hash de consulta no Repositório de Consultas |
|query_plan_hash_s|Hash de plano de consulta no Repositório de consultas |
|statement_sql_handle_s|Identificador de instrução no Repositório de consultas |
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|count_executions_d|Contagem de execuções da consulta |
|query_id_d|ID da consulta no Repositório de Consultas |
|plan_id_d|ID do plano no Repositório de Consultas |

Saiba mais sobre os [Dados de estatísticas de espera no Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Conjunto de dados de erros

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQ |
|Categoria|Nome da categoria. Sempre: Errors |
|OperationName|Nome da operação. Sempre: ErrorEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|Mensagem|Mensagem de erro em texto sem formatação |
|user_defined_b|É o bit de erro definido pelo usuário |
|error_number_d|Código do erro |
|Severity|Gravidade do erro |
|state_d|Estado do erro |
|query_hash_s|Hash de consulta da consulta com falha, se disponível |
|query_plan_hash_s|Hash do plano de consulta da consulta com falha, se disponível |

Saiba mais sobre as [Mensagens de erro do SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Conjunto de dados das estatísticas de espera do banco de dados

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: DatabaseWaitStatistics |
|OperationName|Nome da operação. Sempre: DatabaseWaitStatisticsEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|wait_type_s|Nome do tipo de espera |
|start_utc_date_t [UTC]|Hora de início do período de medida |
|end_utc_date_t [UTC]|Hora de término do período de medida |
|delta_max_wait_time_ms_d|Tempo máximo de espera por execução |
|delta_signal_wait_time_ms_d|Tempo de espera total de sinais |
|delta_wait_time_ms_d|Tempo total de espera no período |
|delta_waiting_tasks_count_d|Número de tarefas em espera |

Saiba mais sobre as [estatísticas de espera no banco de dados](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Conjunto de dados de tempos limite

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Tempos limite |
|OperationName|Nome da operação. Sempre: TimeoutEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|error_state_d|Código do estado de erro |
|query_hash_s|Hash de consulta, se disponível |
|query_plan_hash_s|Hash do plano de consulta, se disponível |

### <a name="blockings-dataset"></a>Conjunto de dados de boqueios

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Bloqueios |
|OperationName|Nome da operação. Sempre: BlockEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|lock_mode_s|Modo de bloqueio usado pela consulta |
|resource_owner_type_s|Proprietário do bloqueio |
|blocked_process_filtered_s|XML de relatório de processo bloqueado |
|duration_d|Duração do bloqueio em microssegundos |

### <a name="deadlocks-dataset"></a>Conjunto de dados de deadlocks

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC] |Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Deadlocks |
|OperationName|Nome da operação. Sempre: DeadlockEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|deadlock_xml_s|Relatório de deadlock XML |

### <a name="automatic-tuning-dataset"></a>Conjunto de dados de ajuste automático

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: AutomaticTuning |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|LogicalDatabaseName_s|Nome do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|RecommendationHash_s|Hash exclusivo de recomendação de ajuste automático |
|OptionName_s|Opções de ajuste automático |
|Schema_s|Esquema de banco de dados |
|Table_s|Tabela afetada |
|IndexName_s|Nome do índice |
|IndexColumns_s|Nome da coluna |
|IncludedColumns_s|Colunas incluídas |
|EstimatedImpact_s|Estimado impacto recomendação de ajuste automático JSON |
|Event_s|Tipo de evento de ajuste automático |
|Timestamp_t|Última atualização de carimbo de data/hora |

### <a name="intelligent-insights-dataset"></a>Conjunto de dados do Insights inteligentes

Saiba mais sobre o [formato de log do Insights Inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar o registro em log e entender as categorias de métrica e log com suporte dos vários serviços do Azure, veja:

- [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Visão geral do log de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Para saber mais sobre os Hubs de Evento, leia:

- [O que é Hub de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
