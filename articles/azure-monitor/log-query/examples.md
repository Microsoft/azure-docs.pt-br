---
title: Exemplos de consulta de log do Azure Monitor | Microsoft Docs
description: Exemplos de consultas de log no Azure Monitor usando a linguagem de consulta do Kusto.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 736daa8a09a8f08721c7b7d9c20f012f274b384a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045510"
---
# <a name="azure-monitor-log-query-examples"></a>Exemplos de consulta de log do Azure Monitor
Este artigo inclui vários exemplos de [consultas](log-query-overview.md) usando a [linguagem de consulta do Kusto](/azure/kusto/query/) para recuperar os diferentes tipos de dados de log do Azure Monitor. Diferentes métodos são usados para consolidar e analisar os dados, de modo que você pode usar esses exemplos para identificar estratégias diferentes que pode usar para seus próprios requisitos.  

Veja a [referência da linguagem Kusto](/azure/kusto/query/) para obter detalhes sobre as diferentes palavras-chave usadas nesses exemplos. Confira uma [lição sobre como criar consultas](get-started-queries.md) se você não tiver experiência com o Azure Monitor.

## <a name="events"></a>Eventos

### <a name="search-application-level-events-described-as-cryptographic"></a>Pesquisar eventos de nível de aplicativo descritos como "Criptográficos"
Este exemplo pesquisa a tabela **Eventos** em busca de registros em que **EventLog** é _Application_ e **RenderedDescription** contém _cryptographic_. Inclui registros das últimas 24 horas.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Pesquisar eventos relacionados a unmarshaling
Pesquise as tabelas **Evento** e **SecurityEvents** para registros que mencionem _unmarshaling_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Pulsação

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Traçar um gráfico semana a semana do número de computadores que enviam dados

O exemplo a seguir mostra em gráfico o número de computadores distintos que enviam pulsações, a cada semana.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Localizar computadores obsoletos

O exemplo a seguir localiza os computadores que estavam ativos no último dia, mas não enviaram pulsações na última hora.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Obter o registro de pulsação mais recente por IP do computador

Este exemplo retorna o último registro de pulsação para cada IP de computador.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Corresponder os registros de status protegidos com os registros de pulsação

Este exemplo localiza os registros de status de proteção e os registros de pulsação relacionados, correspondidos quanto ao computador e também quanto à hora.
Observe que o campo de hora será arredondado para o minuto mais próximo. Usamos o cálculo de compartimentalização do runtime para fazer isso: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Taxa de disponibilidade do servidor

Calcule a taxa de disponibilidade de servidor com base em registros de pulsação. A disponibilidade é definida como "pelo menos 1 pulsação por hora".
Portanto, se um servidor estiver disponível em 98 horas de 100, a taxa de disponibilidade será de 98%.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Vários tipos de dados

### <a name="chart-the-record-count-per-table"></a>Criação de gráfico da contagem de registros por tabela
O exemplo a seguir coleta todos os registros de todas as tabelas das últimas cinco horas e conta quantos registros estavam em cada tabela. Os resultados são mostrados em um timechart.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Contar todos os logs coletados na última hora por tipo
O exemplo a seguir pesquisa tudo que foi relatado na última hora e conta os registros de cada tabela por **Tipo**. Por padrão, os resultados são exibidos em um gráfico de barras.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Contar os registros do Diagnóstico do Azure por categoria
Este exemplo conta todos os registros de Diagnóstico do Azure para cada categoria exclusiva.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Obter um registro aleatório para cada categoria exclusiva
Este exemplo obtém um único registro de Diagnóstico do Azure para cada categoria exclusiva.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Obter o registro mais recente por categoria
Este exemplo obtém o registro de Diagnóstico do Azure mais recente em cada categoria exclusiva.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitoramento de rede

### <a name="computers-with-unhealthy-latency"></a>Computadores com latência não íntegra
Este exemplo cria uma lista de computadores distintos com latência não íntegra.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Desempenho

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Unir registros de desempenho do computador para correlacionar a memória e a CPU
Este exemplo correlaciona os registros de **desempenho** de um computador específico e cria dois gráficos de tempo, a média de CPU e memória máxima.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Gráfico de desempenho de uso de CPU por computador
Este exemplo calcula a utilização da CPU dos computadores que começam com _Contoso_ e cria os gráficos correspondentes.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Status de proteção

### <a name="computers-with-non-reporting-protection-status-duration"></a>Computadores com duração de status de proteção sem geração de relatórios
Este exemplo lista os computadores que tinham um status de proteção _Not Reporting_ e a duração pela qual apresentaram esse status.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Corresponder os registros de status protegidos com os registros de pulsação
Este exemplo localiza os registros de status de proteção e os registros de pulsação relacionados, correspondidos quanto ao computador e também quanto à hora.
O campo de hora será arredondado para o minuto mais próximo usando **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Registros de segurança

### <a name="count-security-events-by-activity-id"></a>Contar eventos de segurança por ID da atividade


Este exemplo se baseia na estrutura fixa da coluna **atividade** : \<ID\> - \<Name\> .
Ele analisa o valor de **Activity** em duas novas colunas e conta a ocorrência de cada **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Contar eventos de segurança relacionados a permissões
Este exemplo mostra o número de registros de **securityEvent**, nos quais a coluna **Activity** contém todo o termo _Permissions_. A consulta aplica-se aos registros criados nos últimos 30 minutos.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Localizar as contas que falharam ao fazer logon de computadores com uma detecção de segurança
Este exemplo localiza e faz a contagem de contas que falharam ao fazer logon desde computadores nos quais identificamos uma detecção de segurança.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Meus dados de segurança estão disponíveis?
O início da exploração dos dados geralmente ocorre verificando-se a disponibilidade desses dados. Este exemplo mostra o número de registros **SecurityEvent** nos últimos 30 minutos.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Analisar a ID e o nome da atividade
Os dois exemplos abaixo dependem da estrutura fixa da coluna **atividade** : \<ID\> - \<Name\> . O primeiro exemplo usa o operador **parse** para atribuir valores a duas novas colunas: **activityID** e **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Este exemplo usa o operador **split** para criar uma matriz de valores separados
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Processos de credenciais explícitas
O exemplo a seguir mostra um gráfico de pizza de processos que usavam credenciais explícitas na última semana

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Principais processos em execução

O exemplo a seguir mostra uma linha do tempo de atividade para os cinco processos mais comuns nos últimos três dias.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Localizar tentativas de logon com falha repetidas da mesma conta, de diferentes IPs

O exemplo a seguir localiza tentativas de logon com falha pela mesma conta originadas de mais de cinco IPs diferentes nas últimas seis horas.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Localizar contas de usuário que falharam em fazer logon 
O exemplo a seguir identifica as contas de usuário que falharam em fazer logon mais de cinco vezes no último dia, bem como quando foi a última tentativa de logon por elas.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Usando instruções **join** e **let**, podemos verificar se as mesmas contas suspeitas foram capazes de fazer logon com êxito posteriormente.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Uso

O `Usage` tipo de dados pode ser usado para acompanhar o volume de dados ingeridos por solução ou tipo de dados. Há outras técnicas para estudar os volumes de dados ingeridos por [computador](../platform/manage-cost-storage.md#data-volume-by-computer) ou [assinatura do Azure, grupo de recursos ou recurso](../platform/manage-cost-storage.md#data-volume-by-azure-resource-resource-group-or-subscription).

#### <a name="data-volume-by-solution"></a>Volume de dados por solução

A consulta usada para exibir o volume de dados faturáveis por solução ao longo do último mês (exceto o último dia parcial) é:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Observe que a cláusula `where IsBillable = true` filtra os tipos de dados de determinadas soluções para o qual não há nenhuma taxa de ingestão.  Além disso, a cláusula WITH `TimeGenerated` é apenas para garantir que a experiência de consulta no portal do Azure pareçará além do padrão de 24 horas. Ao usar o tipo de dados Uso, `StartTime` e `EndTime` representam os buckets de tempo para os quais os resultados são apresentados. 

#### <a name="data-volume-by-type"></a>Volume de dados por tipo

Você pode fazer uma análise mais detalhada para ver as tendências de dados por tipo de dados:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Ou para ver uma tabela por solução e por tipo em relação ao último mês:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> Alguns dos campos do tipo de dados Uso, ainda no esquema, foram reprovados e seus valores não serão mais preenchidos. Estes são **Computador**, bem como campos relacionados à ingestão (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.

## <a name="updates"></a>Atualizações

### <a name="computers-still-missing-updates"></a>Computadores que ainda têm atualizações ausentes
Este exemplo mostra uma lista de computadores que tinham uma ou mais atualizações críticas ausentes há alguns dias e ainda têm atualizações ausentes.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Próximas etapas

- Veja a [Referência da linguagem Kusto](/azure/kusto/query) para obter detalhes sobre a linguagem.
- Confira uma [lição sobre as consultas de log de gravação no Azure Monitor](get-started-queries.md).
