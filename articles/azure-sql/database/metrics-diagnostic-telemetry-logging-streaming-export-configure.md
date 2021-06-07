---
title: Configurar a exportação de streaming de métricas e logs de recursos
description: Saiba como configurar a exportação de streaming de métricas e logs de recursos, incluindo a análise de diagnóstico inteligente do banco de dados SQL do Azure e o Azure SQL Instância Gerenciada para o destino de sua escolha para armazenar informações sobre a utilização de recursos e estatísticas de execução de consulta.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 04/06/2020
ms.openlocfilehash: 1de2c1ff02c799d04f2ab2c81e83dda5001a531f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100592726"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Configurar a exportação de streaming do banco de dados SQL do Azure e telemetria de diagnóstico do SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Neste artigo, você aprenderá sobre as métricas de desempenho e os logs de recursos do banco de dados SQL do Azure que você pode exportar para um dos vários destinos para análise. Você aprenderá a configurar a exportação de streaming dessa telemetria de diagnóstico por meio do portal do Azure, do PowerShell, do CLI do Azure, da API REST e dos modelos de Azure Resource Manager.

Você também aprenderá sobre os destinos para os quais pode transmitir essa telemetria de diagnóstico e como escolher entre essas opções. As opções de destino incluem:

- [Análise de Log Analytics e SQL](#stream-into-sql-analytics)
- [Hubs de Evento](#stream-into-event-hubs)
- [Armazenamento do Azure](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>Telemetria de diagnóstico para exportação

O mais importante entre a telemetria de diagnóstico que você pode exportar é o log Intelligent Insights (sqlsights). O [Intelligent insights](intelligent-insights-overview.md) usa inteligência interna para monitorar continuamente o uso do banco de dados por meio de inteligência artificial e detectar eventos de interrupção que causam baixo desempenho. Depois de detectada, é executada uma análise detalhada que gera um log de Intelligent Insights com uma avaliação inteligente do problema. Essa avaliação consiste em uma análise da causa raiz do problema de desempenho do banco de dados e possíveis recomendações para melhorias de desempenho. Você precisa configurar a exportação de streaming desse log para exibir seu conteúdo.

Além de transmitir a exportação do log de Intelligent Insights, você também pode exportar uma variedade de métricas de desempenho e logs de banco de dados adicionais. A tabela a seguir descreve os logs de recursos e métricas de desempenho que você pode configurar para a exportação de streaming para um dos vários destinos. Essa telemetria de diagnóstico pode ser configurada para bancos de dados individuais, pools elásticos e bancos de dados em pool e instâncias gerenciadas e bancos de dados de instância.

| Telemetria de diagnóstico para bancos de dados | Suporte para Banco de Dados SQL do Azure | Suporte às instâncias gerenciadas do Azure SQL |
| :------------------- | ----- | ----- |
| [Métricas básicas](#basic-metrics): contém percentual de DTU/CPU, limite de DTU/CPU, porcentagem de leitura de dados físicos, percentual de gravação de log, êxito/falha/bloqueado por conexões de firewall, porcentagem de sessões, porcentagem de trabalhos, armazenamento, porcentagem de armazenamento e porcentagem de armazenamento XTP. | Sim | Não |
| [Instância e aplicativo avançado](#advanced-metrics): contém dados do sistema de arquivos tempdb e tamanho do arquivo de log e o arquivo de log de porcentagem do tempdb usado. | Sim | Não |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): contém informações sobre as estatísticas de tempo de execução de consulta, como uso da CPU e estatísticas de duração da consulta. | Sim | Sim |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): contém informações sobre as estatísticas de espera de consulta (as que suas consultas aguardaram) são CPU, log e bloqueio. | Sim | Sim |
| [Erros](#errors-dataset): contém informações sobre erros do SQL em um banco de dados. | Sim | Sim |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): contém informações sobre a quantidade de tempo de espera de um banco de dados em diferentes tipos de espera. | Sim | Não |
| [Tempos limite](#time-outs-dataset): contém informações sobre tempos limite em um banco de dados. | Sim | Não |
| [Blocos](#blockings-dataset): contém informações sobre o bloqueio de eventos em um banco de dados. | Sim | Não |
| [Deadlocks](#deadlocks-dataset): contém informações sobre eventos de deadlock em um banco de dados. | Sim | Não |
| [AutomaticTuning](#automatic-tuning-dataset): contém informações sobre as recomendações de ajuste automático para um banco de dados. | Sim | Não |
| [Sqlsights](#intelligent-insights-dataset): contém Intelligent insights no desempenho de um banco de dados. Para saber mais, veja [Intelligent Insights](intelligent-insights-overview.md). | Sim | Sim |

> [!NOTE]
> As configurações de diagnóstico não podem ser configuradas para os **bancos de dados do sistema**, como bancos de dados mestre, msdb, modelo, recurso e tempdb.

## <a name="streaming-export-destinations"></a>Destinos de exportação de streaming

Essa telemetria de diagnóstico pode ser transmitida para um dos seguintes recursos do Azure para análise.

- **[Espaço de trabalho log Analytics](#stream-into-sql-analytics)**:

  Os dados transmitidos para um [espaço de trabalho do log Analytics](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) podem ser consumidos pela análise do [SQL](../../azure-monitor/insights/azure-sql.md). A análise de SQL é uma solução de monitoramento somente em nuvem que fornece monitoramento inteligente de seus bancos de dados que inclui relatórios de desempenho, alertas e recomendações de mitigação. Os dados transmitidos para um espaço de trabalho de Log Analytics podem ser analisados com outros dados de monitoramento coletados e também permitem que você aproveite outros recursos de Azure Monitor, como alertas e visualizações
- **[Hubs de eventos do Azure](#stream-into-event-hubs)**:

  Os dados transmitidos para um [Hub de eventos do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)fornecem a seguinte funcionalidade:

  - **Transmita logs para sistemas de registro em log e telemetria de terceiros**: transmita todas as suas métricas e logs de recursos para um único Hub de eventos para canalizar dados de log para uma ferramenta Siem ou log Analytics de terceiros.
  - **Crie uma plataforma de registro em log e telemetria personalizada**: a natureza de publicação-assinatura altamente escalonável dos hubs de eventos permite ingerir métricas e logs de recursos de forma flexível em uma plataforma de telemetria personalizada. Consulte [projetando e dimensionando uma plataforma de telemetria de escala global nos hubs de eventos do Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) para obter detalhes.
  - **Exibir a integridade do serviço transmitindo dados para Power bi**: Use os hubs de eventos, Stream Analytics e Power bi para transformar os dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Consulte [Stream Analytics e Power bi: um painel de análise em tempo real para dados de streaming](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para obter detalhes sobre essa solução.
- **[Armazenamento do Azure](#stream-into-azure-storage)**:

  Os dados transmitidos para o [armazenamento do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) permitem que você arquive grandes quantidades de telemetria de diagnóstico por uma fração do custo das duas opções de streaming anteriores.

Essa telemetria de diagnóstico transmitida para um desses destinos pode ser usada para medir a utilização de recursos e as estatísticas de execução de consulta para facilitar o monitoramento do desempenho.

![O diagrama mostra muitos bancos de dados SQL e bancos de dados em instâncias gerenciadas enviando telemetria para Diagnóstico do Azure, que encaminha informações para Análise de SQL do Azure, Hub de eventos e armazenamento.](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Habilitar e configurar a exportação de streaming da telemetria de diagnóstico

É possível habilitar e gerenciar as métricas e o log de diagnóstico usando um dos seguintes métodos:

- Portal do Azure
- PowerShell
- CLI do Azure
- API REST do Azure Monitor
- Modelo do Azure Resource Manager

> [!NOTE]
> Para habilitar o streaming de log de auditoria da telemetria de segurança, consulte [Configurar a auditoria para o banco de dados](./auditing-overview.md#setup-auditing) e [auditar logs em logs de Azure monitor e hubs de eventos do Azure](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Configurar a exportação de streaming da telemetria de diagnóstico

Você pode usar o menu **configurações de diagnóstico** no portal do Azure para habilitar e configurar o streaming de telemetria de diagnóstico. Além disso, você pode usar o PowerShell, o CLI do Azure, a [API REST](/rest/api/monitor/diagnosticsettings)e os [modelos do Resource Manager](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md) para configurar o streaming de telemetria de diagnóstico. Você pode definir os seguintes destinos para transmitir a telemetria de diagnóstico: armazenamento do Azure, hubs de eventos do Azure e logs de Azure Monitor.

> [!IMPORTANT]
> A exportação de streaming da telemetria de diagnóstico não está habilitada por padrão.

Selecione uma das guias a seguir para obter diretrizes passo a passo para configurar a exportação de streaming da telemetria de diagnóstico no portal do Azure e para scripts para realizar o mesmo com o PowerShell e o CLI do Azure.

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Pools elásticos no banco de dados SQL do Azure

Você pode configurar um recurso de pool elástico para coletar a seguinte telemetria de diagnóstico:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Pool elástico** | As [métricas básicas](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) contêm percentual de EDTU/CPU, limite de EDTU/CPU, porcentagem de leitura de dados físicos, percentual de gravação de log, porcentagem de sessões, percentual de operadores, armazenamento, porcentagem de armazenamento, limite de armazenamento e porcentagem de armazenamento XTP. |

Para configurar o streaming de telemetria de diagnóstico para pools elásticos e bancos de dados em pool, você precisa configurar cada separadamente:

- Habilitar streaming de telemetria de diagnóstico para um pool elástico
- Habilitar streaming de telemetria de diagnóstico para cada banco de dados no pool elástico

O contêiner do pool elástico tem sua própria telemetria separada de cada telemetria do banco de dados em pool individual.

Para habilitar o streaming de telemetria de diagnóstico para um recurso de pool elástico, siga estas etapas:

1. Vá para o recurso de **pool elástico** em portal do Azure.
2. Selecionar **configurações de Diagnóstico**.
3. Selecione **Ativar diagnóstico** se não existir nenhuma configuração anterior ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para pools elásticos](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. Insira um nome de configuração para sua própria referência.
5. Selecione um recurso de destino para os dados de diagnóstico de streaming: **arquivar na conta de armazenamento**, **transmitir para um hub de eventos** ou **Enviar para log Analytics**.
6. Para o log Analytics, selecione **Configurar** e crie um novo espaço de trabalho selecionando **+ criar novo espaço de trabalho** ou selecione um espaço de trabalho existente.
7. Marque a caixa de seleção para telemetria de diagnóstico de pool elástico: métricas **básicas** .
   ![Configura o diagnóstico para pools elásticos](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. Clique em **Salvar**.
9. Além disso, configure o streaming de telemetria de diagnóstico para cada banco de dados dentro do pool elástico que você deseja monitorar seguindo as etapas descritas na próxima seção.

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico para um pool elástico, você também precisa configurar a telemetria de diagnóstico para cada banco de dados no pool elástico.

### <a name="databases-in-azure-sql-database"></a>Bancos de dados no Azure SQL Database

Você pode configurar um recurso de banco de dados para coletar a seguinte telemetria de diagnóstico:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Banco de dados único ou em pool** | As [métricas básicas](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) contêm percentual de DTU, DTU usado, limite de DTU, porcentagem de CPU, porcentagem de leitura de dados físicos, porcentagem de gravação de log, êxito/falha/bloqueada por conexões de firewall, porcentagem de sessões, porcentagem de armazenamento, porcentagem de armazenamento, percentual de armazenamento XTP e deadlocks. |

Para habilitar o streaming de telemetria de diagnóstico para um banco de dados único ou em pool, siga estas etapas:

1. Vá para o recurso de **banco de dados SQL** do Azure.
2. Selecionar **configurações de Diagnóstico**.
3. Selecione **Ativar diagnóstico** se não existir nenhuma configuração anterior ou selecione **Editar configuração** para editar uma configuração anterior. Você pode criar até três conexões paralelas para a telemetria de diagnóstico de fluxo.
4. Selecione **Adicionar configuração de diagnóstico** para configurar o streaming paralelo de dados de diagnóstico para vários recursos.

   ![Habilitar o diagnóstico para bancos de dados individuais e em pool](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. Insira um nome de configuração para sua própria referência.
6. Selecione um recurso de destino para os dados de diagnóstico de streaming: **arquivar na conta de armazenamento**, **transmitir para um hub de eventos** ou **Enviar para log Analytics**.
7. Para obter a experiência de monitoramento padrão baseada em eventos, marque as seguintes caixas de seleção para a telemetria do log de diagnóstico de banco de dados: **Sqlsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **erros**, **DatabaseWaitStatistics**, **tempos limite**, **blocos** e **deadlocks**.
8. Para uma experiência de monitoramento avançada, com base em um minuto, marque a caixa de seleção para métricas **básicas** .

   ![Configurar o diagnóstico para o banco de dados SQL do Azure](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. Clique em **Salvar**.
10. Repita essas etapas para cada banco de dados que você deseja monitorar.

> [!TIP]
> Repita essas etapas para cada banco de dados individual e em pool que você deseja monitorar.

### <a name="instances-in-azure-sql-managed-instance"></a>Instâncias no Azure SQL Instância Gerenciada

Você pode configurar um recurso de instância gerenciada para coletar a seguinte telemetria de diagnóstico:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Instância gerenciada** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) contém a contagem de vCores, o percentual médio de CPU, as solicitações de E/S, os bytes lidos/gravados, o espaço de armazenamento reservado e o espaço de armazenamento usado. |

Para configurar o streaming de telemetria de diagnóstico para instâncias gerenciadas e bancos de dados de instância, você precisará configurar cada um deles separadamente:

- Habilitar streaming de telemetria de diagnóstico para instância gerenciada
- Habilitar streaming de telemetria de diagnóstico para cada banco de dados de instância

O contêiner de instância gerenciada tem sua própria telemetria separada da telemetria de cada banco de dados de instância.

Para habilitar o streaming de telemetria de diagnóstico para um recurso de instância gerenciada, siga estas etapas:

1. Vá para o recurso de **instância gerenciada** em portal do Azure.
2. Selecionar **configurações de Diagnóstico**.
3. Selecione **Ativar diagnóstico** se não existir nenhuma configuração anterior ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para a instância gerenciada](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. Insira um nome de configuração para sua própria referência.
5. Selecione um recurso de destino para os dados de diagnóstico de streaming: **arquivar na conta de armazenamento**, **transmitir para um hub de eventos** ou **Enviar para log Analytics**.
6. Para o log Analytics, selecione **Configurar** e crie um novo espaço de trabalho selecionando **+ criar novo espaço de trabalho** ou use um espaço de trabalho existente.
7. Marque a caixa de seleção para telemetria de diagnóstico de instância: **ResourceUsageStats**.

   ![Configurar o diagnóstico para a instância gerenciada](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. Clique em **Salvar**.
9. Além disso, configure o streaming de telemetria de diagnóstico para cada banco de dados de instância dentro da instância gerenciada que você deseja monitorar seguindo as etapas descritas na próxima seção.

> [!IMPORTANT]
> Além de configurar a telemetria de diagnóstico para uma instância gerenciada, você também precisa configurar a telemetria de diagnóstico para cada banco de dados de instância.

### <a name="databases-in-azure-sql-managed-instance"></a>Bancos de dados no SQL Instância Gerenciada do Azure

Você pode configurar um recurso de banco de dados de instância para coletar a seguinte telemetria de diagnóstico:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Banco de dados de instância** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) contém a contagem de vCores, o percentual médio de CPU, as solicitações de E/S, os bytes lidos/gravados, o espaço de armazenamento reservado e o espaço de armazenamento usado. |

Para habilitar o streaming de telemetria de diagnóstico para um banco de dados de instância, siga estas etapas:

1. Vá para o recurso de **banco de dados de instância** na instância gerenciada.
2. Selecionar **configurações de Diagnóstico**.
3. Selecione **Ativar diagnóstico** se não existir nenhuma configuração anterior ou selecione **Editar configuração** para editar uma configuração anterior.
   - Você pode criar até três (3) conexões paralelas para a telemetria de diagnóstico de fluxo.
   - Selecione **+Adicionar configuração de diagnóstico** para configurar o streaming de dados de diagnóstico para vários recursos.

   ![Habilitar o diagnóstico de bancos de dados de instância](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. Insira um nome de configuração para sua própria referência.
5. Selecione um recurso de destino para os dados de diagnóstico de streaming: **arquivar na conta de armazenamento**, **transmitir para um hub de eventos** ou **Enviar para log Analytics**.
6. Marque as caixas de seleção para telemetria de diagnóstico de banco de dados: **Sqlsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** e **erros**.
   ![Configurar o diagnóstico para bancos de dados de instância](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. Clique em **Salvar**.
8. Repita essas etapas para cada banco de dados de instância que você deseja monitorar.

> [!TIP]
> Repita essas etapas para cada banco de dados de instância que você deseja monitorar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Você pode habilitar o log de diagnóstico e métricas usando o PowerShell.

- Para habilitar o armazenamento de métricas e logs de recursos em uma conta de armazenamento, use este comando:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  A ID da conta de armazenamento é a ID do recurso para a conta de armazenamento de destino.

- Para habilitar o streaming de métricas e logs de recursos para um hub de eventos, use este comando:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  A ID de regra do Barramento de Serviço do Azure é uma cadeia de caracteres com o seguinte formato:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Para habilitar o envio de métricas e logs de recursos para um espaço de trabalho Log Analytics, use este comando:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- Para obter a ID do recurso do workspace do Log Analytics, use o seguinte comando:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

É possível combinar esses parâmetros para habilitar várias opções de saída.

**Configurar vários recursos do Azure**

Para dar suporte a várias assinaturas, use o script do PowerShell de [Habilitar log de métricas de recursos do Azure usando o PowerShell](/archive/blogs/msoms/enable-azure-resource-metrics-logging-using-powershell).

Forneça a ID de recurso do espaço de trabalho \<$WSID\> como um parâmetro ao executar o script `Enable-AzureRMDiagnostics.ps1` para enviar dados de diagnóstico de vários recursos para o espaço de trabalho.

- Para obter a ID \<$WSID\> do espaço de trabalho do destino dos dados de diagnóstico, use o seguinte script:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Substitua \<subID\> pela ID da assinatura, \<RG_NAME\> pelo nome do grupo de recursos e \<WS_NAME\> pelo nome do espaço de trabalho.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode habilitar o log de diagnóstico e métricas usando a CLI do Azure.

> [!IMPORTANT]
> Há suporte para scripts para habilitar o log de diagnóstico para o CLI do Azure v 1.0. Não há suporte para o CLI do Azure v 2.0 no momento.

- Para habilitar o armazenamento de métricas e logs de recursos em uma conta de armazenamento, use este comando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  A ID da conta de armazenamento é a ID do recurso para a conta de armazenamento de destino.

- Para habilitar o streaming de métricas e logs de recursos para um hub de eventos, use este comando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  A ID da regra do Barramento de Serviço é uma cadeia de caracteres com este formato:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Para habilitar o envio de métricas e logs de recursos para um espaço de trabalho Log Analytics, use este comando:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

É possível combinar esses parâmetros para habilitar várias opções de saída.

---

## <a name="stream-into-sql-analytics"></a>Transmitir para análise de SQL

O banco de dados SQL do Azure e as métricas do Azure SQL Instância Gerenciada e os logs de recursos que são transmitidos em um espaço de trabalho Log Analytics podem ser consumidos pelo Análise de SQL do Azure. Análise de SQL do Azure é uma solução de nuvem que monitora o desempenho de bancos de dados individuais, pools elásticos e bancos de dados em pool, além de instâncias gerenciadas e bancos de dados de instância em escala e em várias assinaturas. Ele pode ajudá-lo a coletar e Visualizar métricas de desempenho e tem inteligência interna para solução de problemas de desempenho.

![Visão geral da Análise de SQL do Azure](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Visão geral da instalação

Você pode monitorar uma coleção de bancos de dados e de coleções de bancos com Análise de SQL do Azure executando as seguintes etapas:

1. Criar uma solução de Análise de SQL do Azure usando o Azure Marketplace.
2. Crie um espaço de trabalho Log Analytics na solução.
3. Configure bancos de dados para transmitir a telemetria de diagnóstico para o espaço de trabalho.

Você pode configurar a exportação de streaming dessa telemetria de diagnóstico usando a opção interna **Enviar para log Analytics** na guia Configurações de diagnóstico na portal do Azure. Você também pode habilitar o streaming em um espaço de trabalho Log Analytics usando configurações de diagnóstico por meio de [cmdlets do PowerShell](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), o [CLI do Azure](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), a [API REST Azure monitor](/rest/api/monitor/diagnosticsettings)ou [modelos do Resource Manager](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md).

### <a name="create-an-azure-sql-analytics-resource"></a>Criar um recurso de Análise de SQL do Azure

1. Pesquise a Análise de SQL do Azure no Azure Marketplace e selecione-o.

   ![Pesquisar para Análise de SQL do Azure no portal](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. Selecione **criar** na tela de visão geral da solução.

3. Preencha o formulário de Análise de SQL do Azure com as informações adicionais necessárias: nome do workspace, assinatura, grupo de recursos, local e tipo de preço.

   ![Configurar Análise de SQL do Azure no portal](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. Selecione **OK** para confirmar e, em seguida, selecione **Criar**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Configurar o recurso para registrar as métricas e os logs de recursos

Você precisa configurar separadamente o streaming de telemetria de diagnóstico para bancos de dados únicos e em pool, pools elásticos, instâncias gerenciadas e bancos de dados de instância. A maneira mais fácil de configurar onde um recurso registra métricas é usando o portal do Azure. Para obter etapas detalhadas, consulte [Configurar a exportação de streaming de telemetria de diagnóstico](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Usar Análise de SQL do Azure para monitoramento e alertas

Você pode usar a análise do SQL como um painel hierárquico para exibir os recursos do banco de dados.

- Para saber como usar Análise de SQL do Azure, consulte [monitorar usando a análise de SQL](../../azure-monitor/insights/azure-sql.md).
- Para saber como configurar alertas para o na análise de SQL, consulte [criando alertas para banco de dados, pools elásticos e instâncias gerenciadas](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Transmitir para os Hubs de Eventos

Você pode transmitir o banco de dados SQL do Azure e as métricas do Azure SQL Instância Gerenciada e os logs de recursos para os hubs de eventos usando o **fluxo interno para uma** opção de Hub de eventos no portal do Azure. Você também pode habilitar a ID da regra do barramento de serviço usando configurações de diagnóstico por meio de cmdlets do PowerShell, o CLI do Azure ou a API REST do Azure Monitor. Verifique se o hub de eventos está na mesma região que o banco de dados e o servidor.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>O que fazer com métricas e logs de recursos em hubs de eventos

Depois que os dados selecionados são transmitidos para os Hubs de Eventos, os habilitar cenários de monitoramento avançado estarão mais próximos. Os Hubs de Eventos atuam como uma porta da frente para um pipeline de eventos. Depois que os dados são coletados para um hub de eventos, eles podem ser transformados e armazenados usando um provedor de análise em tempo real ou um adaptador de armazenamento. Os Hubs de Eventos separam a produção de um fluxo de eventos do consumo desses eventos. Dessa forma, os consumidores de evento podem acessá-los em suas próprias agendas. Para obter mais informações sobre os Hubs de Eventos, consulte:

- [O que são Hubs de Eventos do Azure?](../../event-hubs/event-hubs-about.md)
- [Introdução aos Hubs de Eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Você pode usar métricas de streaming em Hubs de Eventos para:

- **Exibir a integridade do serviço transmitindo dados de Hot-Path para Power BI**

  Ao utilizar os Hubs de Eventos, o Stream Analytics e o Power BI, é fácil transformar suas métricas e dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Para obter uma visão geral de como configurar um hub de eventos, processar dados com o Stream Analytics e usar o Power BI como uma saída, consulte [Stream Analytics e Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Transmitir logs para fluxos de telemetria e log de terceiros**

  Usando o streaming de hubs de eventos, você pode obter suas métricas e logs de recursos em várias soluções de monitoramento e log Analytics de terceiros.

- **Criar uma plataforma de registro em log e telemetria personalizada**

  Fazer você já tem uma plataforma de telemetria personalizada ou está pensando em criar uma? A natureza de publicação-assinatura altamente escalonável dos hubs de eventos permite ingerir métricas e logs de recursos de maneira flexível. Consulte [o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Fluxo no Armazenamento do Azure

Você pode armazenar métricas e logs de recursos no armazenamento do Azure usando a opção interno **arquivar em uma conta de armazenamento** no portal do Azure. Você também pode habilitar o armazenamento usando configurações de diagnóstico por meio de cmdlets do PowerShell, o CLI do Azure ou a API REST do Azure Monitor.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Esquema de métricas e logs de recursos na conta de armazenamento

Depois de configurar a coleta de métricas e logs de recursos, um contêiner de armazenamento é criado na conta de armazenamento que você selecionou quando as primeiras linhas de dados estão disponíveis. A estrutura dos blobs é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ou, simplesmente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, um nome de BLOB para métricas básicas pode ser:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Um nome de blob para armazenar dados de um pool elástico se parece com:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Política de retenção de dados e preços

Ao selecionar os Hubs de Eventos ou uma conta de Armazenamento, é possível especificar uma política de retenção. Essa política exclui dados mais antigos que um período de tempo selecionado. Se você especificar a análise de Log, a política de retenção depende do tipo de preço selecionado. Nesse caso, as unidades gratuitas de ingestão de dados fornecidas podem habilitar o monitoramento gratuito de vários bancos de dados por mês. Qualquer consumo de telemetria de diagnóstico que ultrapasse as unidades livres pode incorrer em custos.

> [!IMPORTANT]
> Bancos de dados ativos com cargas de trabalho mais pesadas ingerim mais informações do que bancos de dados ociosos. Para obter mais informações, consulte [preços do log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Se você estiver usando Análise de SQL do Azure, poderá monitorar seu consumo de ingestão de dados selecionando **espaço de trabalho do OMS** no menu de navegação de análise de SQL do Azure e, em seguida, selecionando **uso** e **custos estimados**.

## <a name="metrics-and-logs-available"></a>Métricas e logs disponíveis

O monitoramento de telemetria disponível para bancos de dados individuais, bancos de dados em pool, pools elásticos, instância gerenciada e bancos de dados de instância está documentado nesta seção do artigo. A telemetria de monitoramento coletada dentro do SQL Analytics pode ser usada para sua própria análise personalizada e desenvolvimento de aplicativos usando Azure Monitor linguagem de [consultas de log](../../azure-monitor/logs/get-started-queries.md) .

### <a name="basic-metrics"></a>Métricas básicas

Consulte as tabelas a seguir para obter detalhes sobre as métricas básicas por recurso.

> [!NOTE]
> A opção de métricas básica era conhecida anteriormente como todas as métricas. A alteração feita era apenas para a nomenclatura e não havia nenhuma alteração nas métricas monitoradas. Essa alteração foi iniciada para permitir a introdução de categorias métricas adicionais no futuro.

#### <a name="basic-metrics-for-elastic-pools"></a>Métricas básicas para pools elásticos

|**Recurso**|**Métricas**|
|---|---|
|Pool elástico|O percentual de eDTU, eDTU usado, limite de eDTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, limite de armazenamento, percentual de armazenamento XTP |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Métricas básicas para bancos de dados individuais e em pool

|**Recurso**|**Métricas**|
|---|---|
|Banco de dados único e em pool|O percentual de DTU, DTU usado, o limite de DTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, êxito/falha/bloqueio por conexões de firewall, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, percentual de armazenamento XTP e deadlocks |

### <a name="advanced-metrics"></a>Métricas avançadas

Consulte a tabela a seguir para obter detalhes sobre as métricas avançadas.

|**Métrica**|**Nome de exibição da métrica**|**Descrição**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|Porcentagem de núcleo do processo SQL|Percentual de uso da CPU para o processo SQL, conforme medido pelo sistema operacional.|
|sqlserver_process_memory_percent<sup>1</sup> |Porcentagem de memória do processo SQL|Porcentagem de uso de memória para o processo SQL, conforme medido pelo sistema operacional.|
|tempdb_data_size<sup>2</sup>| Quilobytes do Tamanho do Arquivo de Dados Tempdb |Tamanho do arquivo de dados tempdb em kilobytes.|
|tempdb_log_size<sup>2</sup>| Quilobytes do Tamanho do Arquivo de Log Tempdb |Tamanho do arquivo de log de tempdb em kilobytes.|
|tempdb_log_used_percent<sup>2</sup>| Porcentagem de Uso do Log Tempdb |Log de porcentagem de tempdb usado.|

<sup>1</sup> essa métrica está disponível para bancos de dados usando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para modelos de compra baseados em DTU.

<sup>2</sup> essa métrica está disponível para bancos de dados usando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para modelos de compra baseados em DTU. Essa métrica não está disponível no momento para bancos de dados ou data warehouses de hiperescala.

### <a name="basic-logs"></a>Logs básicos

Os detalhes da telemetria disponível para todos os logs estão documentados nas tabelas a seguir. Para obter mais informações, consulte [telemetria de diagnóstico com suporte](#diagnostic-telemetry-for-export).

#### <a name="resource-usage-stats-for-managed-instances"></a>Estatísticas de uso de recursos para instâncias gerenciadas

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
|SKU_s|SKU do produto SQL Instância Gerenciada |
|virtual_core_count_s|Número de vCores disponíveis |
|avg_cpu_percent_s|Percentual médio de CPU |
|reserved_storage_mb_s|Capacidade de armazenamento reservada na instância gerenciada |
|storage_space_used_mb_s|Armazenamento usado na instância gerenciada |
|io_requests_s|Contagem de IOPS |
|io_bytes_read_s|Bytes de IOPS lidos |
|io_bytes_written_s|Bytes de IOPS gravados |

#### <a name="query-store-runtime-statistics"></a>Estatísticas de runtime do Repositório de consultas

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
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
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

Saiba mais sobre [repositório de consultas dados de estatísticas de tempo de execução](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Estatísticas de espera do Repositório de consultas

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
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
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

Saiba mais sobre os [Dados de estatísticas de espera no Repositório de Consultas](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Conjunto de dados de erros

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Erros |
|OperationName|Nome da operação. Sempre: ErrorEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|Mensagem|Mensagem de erro em texto sem formatação |
|user_defined_b|É o bit de erro definido pelo usuário |
|error_number_d|Código do erro |
|Severidade|Gravidade do erro |
|state_d|Estado do erro |
|query_hash_s|Hash de consulta da consulta com falha, se disponível |
|query_plan_hash_s|Hash do plano de consulta da consulta com falha, se disponível |

Saiba mais sobre [as mensagens de erro do SQL](/sql/relational-databases/errors-events/database-engine-events-and-errors).

#### <a name="database-wait-statistics-dataset"></a>Conjunto de dados das estatísticas de espera do banco de dados

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
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
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

Saiba mais sobre as [estatísticas de espera no banco de dados](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Conjunto de dados de tempos limite

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: tempos limite |
|OperationName|Nome da operação. Sempre: TimeoutEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|error_state_d|Código do estado de erro |
|query_hash_s|Hash de consulta, se disponível |
|query_plan_hash_s|Hash do plano de consulta, se disponível |

#### <a name="blockings-dataset"></a>Conjunto de dados de boqueios

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: blocos |
|OperationName|Nome da operação. Sempre: BlockEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
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

#### <a name="deadlocks-dataset"></a>Conjunto de dados de deadlocks

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC] |Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Deadlocks |
|OperationName|Nome da operação. Sempre: Deadlocks |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|deadlock_xml_s|Relatório de deadlock XML |

#### <a name="automatic-tuning-dataset"></a>Conjunto de dados de ajuste automático

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: AutomaticTuning |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
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

#### <a name="intelligent-insights-dataset"></a>Conjunto de dados do Insights inteligentes

Saiba mais sobre o [formato de log do Insights Inteligentes](intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar o registro em log e entender as categorias de métrica e log com suporte dos vários serviços do Azure, veja:

- [Visão geral das métricas no Microsoft Azure](../../azure-monitor/data-platform.md)
- [Visão geral dos logs de plataforma do Azure](../../azure-monitor/essentials/platform-logs-overview.md)

Para saber mais sobre os Hubs de Evento, leia:

- [O que são os Hubs de Eventos do Azure?](../../event-hubs/event-hubs-about.md)
- [Introdução aos Hubs de Eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Para saber como configurar alertas com base na telemetria do log Analytics, consulte:

- [Criando alertas para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)