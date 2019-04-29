---
title: Tempo de ingestão de dados de log no Azure Monitor | Microsoft Docs
description: Explica os diferentes fatores que afetam a latência na coleta de dados de log no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: ba9a0ab775e062f21a058b537e289fe3ea2b40bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093957"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Tempo de ingestão de dados de log no Azure Monitor
O Azure Monitor é um serviço de dados de grande escala que atende milhares de clientes que enviam terabytes de dados por mês em um ritmo cada vez maior. Frequentemente, há dúvidas sobre o tempo necessário para que os dados de log fiquem disponíveis após a coleta. Este artigo explica os diferentes fatores que afetam essa latência.

## <a name="typical-latency"></a>Latência típica
Latência se refere ao momento em que os dados são criados no sistema monitorado e o momento em que são disponibilizados para análise no Azure Monitor. A latência típica de ingestão de dados de log é entre 2 e 5 minutos. A latência específica para dados específicos variará conforme uma variedade de fatores explicados abaixo.


## <a name="factors-affecting-latency"></a>Fatores que afetam a latência
O tempo total de ingestão para determinado conjunto de dados pode ser dividido nas áreas de alto nível a seguir. 

- Tempo do agente – o tempo necessário para descobrir um evento, coletá-lo e, em seguida, enviá-lo para o ponto de ingestão do Azure Monitor como um registro de log. Na maioria dos casos, esse processo é manipulado por um agente.
- Tempo do pipeline – o tempo necessário para o pipeline de ingestão processar o registro de log. Isso inclui a análise das propriedades do evento e a adição potencial de informações calculadas.
- Tempo de indexação – o tempo gasto para ingestão de um registro de log no armazenamento de Big Data do Azure Monitor.

Os detalhes sobre a latência diferente introduzida nesse processo são descritos abaixo.

### <a name="agent-collection-latency"></a>Latência da coleta de agente
Os agentes e as soluções de gerenciamento usam estratégias diferentes para coletar dados de uma máquina virtual, o que pode afetar a latência. Alguns exemplos específicos incluem os seguintes:

- Os eventos do Windows, os eventos de syslog e as métricas de desempenho são coletados imediatamente. Os contadores de desempenho do Linux são sondados em intervalos de 30 segundos.
- Os logs do IIS e os logs personalizados são coletados quando seu carimbo de data/hora é alterado. Para os logs do IIS, isso é influenciado pelo [agendamento de sobreposição configurado no IIS](data-sources-iis-logs.md). 
- A solução Replicação do Active Directory realiza sua avaliação a cada cinco dias, enquanto a solução Avaliação do Active Directory realiza uma avaliação semanal da infraestrutura do Active Directory. O agente coletará esses logs somente quando a avaliação for concluída.

### <a name="agent-upload-frequency"></a>Frequência de upload de agente
Para garantir que o agente do Log Analytics seja leve, o agente armazena em buffer os logs e carrega-os periodicamente no Azure Monitor. A frequência de upload varia entre 30 segundos e 2 minutos, dependendo do tipo de dados. A maioria dos dados é carregada em menos de 1 minuto. As condições de rede podem afetar negativamente a latência com a qual esses dados são recebidos no ponto de ingestão do Azure Monitor.

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Logs de atividades, logs de diagnóstico e métricas do Azure
Os dados do Azure adicionam tempo extra para se tornarem disponíveis no ponto de ingestão do Log Analytics para processamento:

- Os dados dos logs de diagnóstico levam de 2 a 15 minutos, dependendo do serviço do Azure. Confira a [consulta abaixo](#checking-ingestion-time) para examinar essa latência em seu ambiente
- As métricas de plataforma do Azure levam 3 minutos para serem enviadas ao ponto de ingestão do Log Analytics.
- Os dados do log de atividades levarão cerca de 10 a 15 minutos para serem enviados ao ponto de ingestão do Log Analytics.

Quando estiverem disponíveis no ponto de ingestão, somam-se mais 2 a 5 minutos para que os dados fiquem disponíveis para consulta.

### <a name="management-solutions-collection"></a>Coleção de soluções de gerenciamento
Algumas soluções não coletam seus dados de um agente e podem usar um método de coleta que introduz latência adicional. Algumas soluções coletam dados em intervalos regulares sem tentar a coleta quase em tempo real. Exemplos específicos incluem os seguintes:

- A solução do Office 365 sonda os logs de atividades usando a API de Gerenciamento de Atividade do Office 365, que atualmente não fornece nenhuma garantia de latência quase em tempo real.
- Os dados das soluções de Análise do Windows (Conformidade de Atualizações, por exemplo) são coletados pela solução com uma frequência diária.

Veja a documentação de cada solução para determinar sua frequência de coleta.

### <a name="pipeline-process-time"></a>Tempo de processo de pipeline
Depois que os registros de log são ingeridos no pipeline do Azure Monitor, eles são gravados em um armazenamento temporário para garantir o isolamento do locatário e garantir que os dados não sejam perdidos. Normalmente, esse processo adiciona 5 a 15 segundos. Algumas soluções de gerenciamento implementam algoritmos mais pesados para agregar dados e obter insights conforme os dados são recebidos. Por exemplo, o Monitoramento de Desempenho de Rede agrega os dados recebidos em intervalos de 3 minutos, efetivamente, adicionando uma latência de 3 minutos. Outro processo que adiciona latência é o processo que lida com logs personalizados. Em alguns casos, esse processo pode adicionar alguns minutos de latência aos logs que são coletados de arquivos pelo agente.

### <a name="new-custom-data-types-provisioning"></a>Provisionamento de novos tipos de dados personalizados
Quando um novo tipo de dados personalizados é criado com base em um [log personalizado](data-sources-custom-logs.md) ou na [API do Coletor de Dados](data-collector-api.md), o sistema cria um contêiner de armazenamento dedicado. Essa é uma sobrecarga única que ocorre apenas na primeira aparência desse tipo de dados.

### <a name="surge-protection"></a>Proteção contra aumento
A principal prioridade do Azure Monitor é garantir que nenhum dado do cliente seja perdido e, portanto, o sistema tem uma proteção interna para aumentos de dados. Isso inclui buffers para garantir que, mesmo sob carga imensa, o sistema continue funcionando. Sob carga normal, esses controles adicionam menos de um minuto, mas em condições extremas e falhas, eles podem adicionar tempo significativo, ao mesmo tempo que garantem que os dados estão seguros.

### <a name="indexing-time"></a>Tempo de indexação
Há um equilíbrio interno para cada plataforma de Big Data entre o fornecimento de funcionalidades de análise e pesquisa avançada, em detrimento do fornecimento de acesso imediato aos dados. O Azure Monitor permite que você execute consultas avançadas em bilhões de registros e obtenha resultados em alguns segundos. Isso se torna possível porque a infraestrutura transforma os dados drasticamente durante sua ingestão e armazena-os em estruturas compactas exclusivas. O sistema armazena os dados em buffer até que dados suficientes estejam disponíveis para criar essas estruturas. Isso precisa ser concluído antes que o registro de log seja exibido nos resultados da pesquisa.

No momento, esse processo leva cerca de 5 minutos quando há um baixo volume de dados, mas menos tempo em taxas mais altas de dados. Isso parece confuso, mas esse processo permite a otimização de latência para cargas de trabalho de produção de alto volume.



## <a name="checking-ingestion-time"></a>Verificando o tempo de ingestão
O tempo de ingestão pode variar para recursos diferentes em circunstâncias diferentes. Você pode usar consultas de log para identificar um comportamento específico do seu ambiente.

### <a name="ingestion-latency-delays"></a>Atrasos de latência de ingestão
Você pode medir a latência de um registro específico ao comparar o resultado da função [ingestion_time()](/azure/kusto/query/ingestiontimefunction) com o campo _TimeGenerated_. Esses dados podem ser usados com várias agregações para descobrir como a latência de ingestão se comporta. Examine alguns percentil do tempo de ingestão para obter insights para uma grande quantidade de dados. 

Por exemplo, a consulta a seguir mostrará quais computadores tiveram o maior tempo de ingestão ao longo do dia atual: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc  
```
 
Se você quiser fazer busca detalhada da hora de ingestão para um computador específico em um período, use a seguinte consulta, que também visualiza os dados em um gráfico: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart  
```
 
Use a seguinte consulta para mostrar o tempo de ingestão de computador pelo país em que ele está localizado, o que se baseia no seu endereço IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by RemoteIPCountry 
```
 
Diferentes tipos de dados de origem do agente podem ter tempos de latência de ingestão diferentes, assim, as consultas anteriores podem ser usadas com outros tipos. Use a consulta a seguir para examinar o tempo de ingestão de vários serviços do Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Recursos que param de responder 
Em alguns casos, um recurso pode parar de enviar dados. Para entender se um recurso está enviando dados ou não, examine seu registro mais recente, que pode ser identificado pelo campo _TimeGenerated_ padrão.  

Use a tabela _Pulsação_ para verificar a disponibilidade de uma VM, já que uma pulsação é enviada uma vez por minuto pelo agente. Use a consulta a seguir para listar os computadores ativos que não relataram recentemente a pulsação: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Próximas etapas
* Leia o [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) do Azure Monitor.

