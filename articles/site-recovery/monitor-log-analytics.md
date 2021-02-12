---
title: Monitorar Azure Site Recovery com logs de Azure Monitor
description: Saiba como monitorar Azure Site Recovery com Azure Monitor logs (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: e3d3ce8218030bc8ba6c59b26b7360bf2299e02a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499808"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorar Site Recovery com os logs do Azure Monitor

Este artigo descreve como monitorar máquinas replicadas pelo Azure [site Recovery](site-recovery-overview.md), usando [Azure Monitor logs](../azure-monitor/platform/data-platform-logs.md)e [log Analytics](../azure-monitor/log-query/log-query-overview.md).

Os logs de Azure Monitor fornecem uma plataforma de dados de log que coleta logs de atividade e de recursos, juntamente com outros dados de monitoramento. Nos logs de Azure Monitor, você usa Log Analytics para gravar e testar consultas de log e para analisar interativamente os dados de log. Você pode visualizar e consultar os resultados do log e configurar alertas para executar ações com base em dados monitorados.

Por Site Recovery, você pode usar os logs de Azure Monitor para ajudá-lo a fazer o seguinte:

- **Monitorar site Recovery integridade e status**. Por exemplo, você pode monitorar a integridade da replicação, o status do failover de teste, eventos de Site Recovery, RPOs (objetivos de ponto de recuperação) para computadores protegidos e taxas de alteração de disco/dados.
- **Configure alertas para site Recovery**. Por exemplo, você pode configurar alertas para a integridade do computador, status do failover de teste ou Site Recovery status do trabalho.

O uso de logs de Azure Monitor com Site Recovery tem suporte para a replicação do Azure **para o Azure** e para a replicação do **VMware/servidor físico para Azure** .

> [!NOTE]
> Para obter os logs de dados de rotatividade e os logs de taxa de upload para VMware e máquinas físicas, você precisa instalar um Microsoft Monitoring Agent no servidor de processo. Esse agente envia os logs das máquinas de replicação para o espaço de trabalho. Essa funcionalidade está disponível apenas para a versão do agente de mobilidade 9,30 em diante.

## <a name="before-you-start"></a>Antes de começar

Você precisa do seguinte:

- Pelo menos um computador protegido em um cofre dos serviços de recuperação.
- Um espaço de trabalho Log Analytics para armazenar Site Recovery logs. [Saiba mais sobre como](../azure-monitor/learn/quick-create-workspace.md) configurar um espaço de trabalho.
- Um entendimento básico de como escrever, executar e analisar consultas de log no Log Analytics. [Saiba mais](../azure-monitor/log-query/log-analytics-tutorial.md).

Recomendamos que você revise as [perguntas comuns de monitoramento](monitoring-common-questions.md) antes de começar.

## <a name="configure-site-recovery-to-send-logs"></a>Configurar Site Recovery para enviar logs

1. No cofre, clique em **configurações de diagnóstico**  >  **Adicionar configuração de diagnóstico**.

    ![Captura de tela mostrando a opção Adicionar configuração de diagnóstico.](./media/monitoring-log-analytics/add-diagnostic.png)

2. Em **configurações de diagnóstico**, especifique um nome e marque a caixa **Enviar para log Analytics**.
3. Selecione a assinatura de logs de Azure Monitor e o espaço de trabalho Log Analytics.
4. Selecione **diagnóstico do Azure** na alternância.
5. Na lista log, selecione todos os logs com o prefixo **AzureSiteRecovery**. Em seguida, clique em **OK**.

    ![Instantâneo da tela de configuração de diagnóstico.](./media/monitoring-log-analytics/select-workspace.png)

Os logs de Site Recovery começam a ser alimentados em uma tabela (**AzureDiagnostics**) no espaço de trabalho selecionado.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Configurar o agente de monitoramento da Microsoft no servidor de processo para enviar a rotatividade e carregar logs de taxa

Você pode capturar as informações de taxa de variação de dados e informações de taxa de upload de dados de origem para seus computadores VMware/físicos no local. Para habilitar isso, é necessário que um Microsoft Monitoring Agent esteja instalado no servidor de processo.

1. Vá para o espaço de trabalho Log Analytics e clique em **Configurações avançadas**.
2. Clique na página **fontes conectadas** e selecione outros **servidores Windows**.
3. Baixe o agente do Windows (64 bits) no servidor de processo. 
4. [Obter a ID e a chave do espaço de trabalho](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)
5. [Configurar o agente para usar o TLS 1,2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Conclua a instalação do agente](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard) fornecendo a ID e a chave do espaço de trabalho obtidas.
7. Quando a instalação for concluída, vá para Log Analytics espaço de trabalho e clique em **Configurações avançadas**. Vá para a página de **dados** e clique mais em **contadores de desempenho do Windows**. 
8. Clique em **' + '** para adicionar os dois contadores a seguir com o intervalo de amostragem de 300 segundos:

    - ASRAnalytics(*)\SourceVmChurnRate
    - ASRAnalytics(*)\SourceVmThrpRate

Os dados da taxa de rotatividade e upload começarão a alimentar no espaço de trabalho.


## <a name="query-the-logs---examples"></a>Consultar os logs-exemplos

Você recupera dados de logs usando consultas de log escritas com a [linguagem de consulta Kusto](../azure-monitor/log-query/get-started-queries.md). Esta seção fornece alguns exemplos de consultas comuns que você pode usar para Site Recovery monitoramento.

> [!NOTE]
> Alguns dos exemplos usam **replicationProviderName_s** definido como **A2A**. Isso recupera as VMs do Azure que são replicadas para uma região do Azure secundária usando Site Recovery. Nesses exemplos, você pode substituir **A2A** por **InMageAzureV2**, se desejar recuperar VMs VMware locais ou servidores físicos que são replicados para o Azure usando site Recovery.


### <a name="query-replication-health"></a>Integridade da replicação de consulta

Essa consulta Plota um gráfico de pizza para a integridade de replicação atual de todas as VMs do Azure protegidas, dividida em três Estados: normal, aviso ou crítico.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Versão do serviço de mobilidade de consultas

Essa consulta Plota um gráfico de pizza para VMs do Azure replicadas com Site Recovery, divididos pela versão do agente de mobilidade que estão em execução.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Tempo de RPO da consulta

Essa consulta Plota um gráfico de barras de VMs do Azure replicadas com Site Recovery, divididas por RPO (objetivo de ponto de recuperação): menos de 15 minutos, entre 15-30 minutos, mais de 30 minutos.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Captura de tela mostrando um gráfico de barras de VMs do Azure replicadas com Site Recovery.](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Trabalhos de Site Recovery de consulta

Essa consulta recupera todos os trabalhos de Site Recovery (para todos os cenários de recuperação de desastre), disparados nas últimas 72 horas e seu estado de conclusão.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Eventos de Site Recovery de consulta

Essa consulta recupera todos os eventos de Site Recovery (para todos os cenários de recuperação de desastre) gerados nas últimas 72 horas, juntamente com sua gravidade. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Estado do failover de teste de consulta (gráfico de pizza)

Essa consulta Plota um gráfico de pizza para o status do failover de teste das VMs do Azure replicadas com Site Recovery.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Estado do failover de teste de consulta (tabela)

Essa consulta plota uma tabela para o status de failover de teste de VMs do Azure replicadas com Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>RPO de máquina de consulta

Essa consulta Plota um grafo de tendência que controla o RPO de uma VM do Azure específica (ContosoVM123) nas últimas 72 horas.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Captura de tela de um grafo de tendência rastreando o RPO de uma VM do Azure específica.](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Taxa de alteração de dados de consulta (rotatividade) e taxa de upload para uma VM do Azure

Essa consulta Plota um grafo de tendência para uma VM do Azure específica (ContosoVM123), que representa a taxa de alteração de dados (bytes de gravação por segundo) e a taxa de carregamento de dados. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![captura de tela de um grafo de tendência para uma VM do Azure específica.](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Taxa de alteração de dados de consulta (rotatividade) e taxa de upload para um VMware ou computador físico

> [!Note]
> Verifique se você configurou o agente de monitoramento no servidor de processo para buscar esses logs. Consulte [as etapas para configurar o agente de monitoramento](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Essa consulta Plota um grafo de tendência para um **disk0** de disco específico de um item replicado **Win-9r7sfh9qlru**, que representa a taxa de alteração de dados (bytes de gravação por segundo) e a taxa de carregamento de dados. Você pode encontrar o nome do disco na folha **discos** do item replicado no cofre dos serviços de recuperação. O nome da instância a ser usada na consulta é o nome DNS da máquina seguido por _ e o nome do disco como neste exemplo.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
O servidor de processo envia esses dados a cada 5 minutos para o espaço de trabalho Log Analytics. Esses pontos de dados representam a média calculada por 5 minutos.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Resumo da recuperação de desastre de consulta (Azure para Azure)

Essa consulta plota uma tabela de resumo para VMs do Azure replicadas para uma região secundária do Azure.  Ele mostra o nome da VM, a replicação e o status de proteção, o RPO, o status do failover de teste, a versão do agente de mobilidade, os erros de replicação ativa e o local de origem.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Resumo da recuperação de desastre de consulta (servidores VMware/físicos)

Essa consulta plota uma tabela de resumo para VMs VMware e servidores físicos replicados para o Azure.  Ele mostra o nome do computador, a replicação e o status de proteção, o RPO, o status do failover de teste, a versão do agente de mobilidade, os erros de replicação ativa e o servidor de processo relevante.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Configurar alertas-exemplos

Você pode configurar alertas de Site Recovery com base em dados de Azure Monitor. [Saiba mais](../azure-monitor/platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) sobre como configurar alertas de log. 

> [!NOTE]
> Alguns dos exemplos usam **replicationProviderName_s** definido como **A2A**. Isso define alertas para VMs do Azure que são replicadas para uma região secundária do Azure. Nesses exemplos, você pode substituir **A2A** por **InMageAzureV2** se quiser definir alertas para VMs VMware locais ou servidores físicos replicados para o Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Vários computadores em um estado crítico

Configure um alerta se mais de 20 VMs do Azure replicadas entrarem em um estado crítico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Para o alerta, defina o **valor do limite** como 20.

### <a name="single-machine-in-a-critical-state"></a>Computador único em estado crítico

Configure um alerta se uma VM do Azure replicada específica entrar em um estado crítico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, defina o **valor do limite** como 1.

### <a name="multiple-machines-exceed-rpo"></a>Vários computadores excedem o RPO

Configure um alerta se o RPO para mais de 20 VMs do Azure exceder 30 minutos.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Para o alerta, defina o **valor do limite** como 20.

### <a name="single-machine-exceeds-rpo"></a>O computador único excede o RPO

Configure um alerta se o RPO de uma única VM do Azure exceder 30 minutos.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Para o alerta, defina o **valor do limite** como 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>O failover de teste para vários computadores excede 90 dias

Configure um alerta se o último failover de teste bem-sucedido tiver mais de 90 dias, para mais de 20 VMs. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, defina o **valor do limite** como 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>O failover de teste para um único computador excede 90 dias

Configure um alerta se o último failover de teste bem-sucedido de uma VM específica tiver mais de 90 dias atrás.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, defina o **valor do limite** como 1.

### <a name="site-recovery-job-fails"></a>Falha no trabalho de Site Recovery

Configure um alerta se um trabalho de Site Recovery (nesse caso, o trabalho de nova proteção) falhar para qualquer cenário de Site Recovery, durante o último dia. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Para o alerta, defina o **valor do limite** como 1 e **período** como 1440 minutos para verificar falhas no último dia.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre](site-recovery-monitor-and-troubleshoot.md) o monitoramento de site Recovery interno.