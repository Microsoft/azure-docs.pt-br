---
title: Tempo de ingestão de dados de log no Azure Monitor | Microsoft Docs
description: Explica os diferentes fatores que afetam a latência na coleta de dados de log no Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 56ef6563982c315d34cfeb87070b9ebfa3d27a30
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500420"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Tempo de ingestão de dados de log no Azure Monitor
O Azure Monitor é um serviço de dados de grande escala que atende milhares de clientes que enviam terabytes de dados por mês em um ritmo cada vez maior. Frequentemente, há dúvidas sobre o tempo necessário para que os dados de log fiquem disponíveis após a coleta. Este artigo explica os diferentes fatores que afetam essa latência.

## <a name="typical-latency"></a>Latência típica
Latência se refere ao momento em que os dados são criados no sistema monitorado e o momento em que são disponibilizados para análise no Azure Monitor. A latência típica para ingerir dados de log está entre 20 s e 3 minutos. No entanto, a latência específica para qualquer dado específico variará dependendo de uma variedade de fatores explicados abaixo.


## <a name="factors-affecting-latency"></a>Fatores que afetam a latência
O tempo total de ingestão para determinado conjunto de dados pode ser dividido nas áreas de alto nível a seguir. 

- Tempo do agente-o tempo para descobrir um evento, coletá-lo e enviá-lo para Azure Monitor ponto de ingestão de logs como um registro de log. Na maioria dos casos, esse processo é manipulado por um agente. A latência adicional pode ser introduzida pela rede.
- Tempo do pipeline – o tempo necessário para o pipeline de ingestão processar o registro de log. Isso inclui a análise das propriedades do evento e a adição potencial de informações calculadas.
- Tempo de indexação – o tempo gasto para ingestão de um registro de log no armazenamento de Big Data do Azure Monitor.

Os detalhes sobre a latência diferente introduzida nesse processo são descritos abaixo.

### <a name="agent-collection-latency"></a>Latência da coleta de agente
Os agentes e as soluções de gerenciamento usam estratégias diferentes para coletar dados de uma máquina virtual, o que pode afetar a latência. Alguns exemplos específicos incluem os seguintes:

- Os eventos do Windows, os eventos de syslog e as métricas de desempenho são coletados imediatamente. Os contadores de desempenho do Linux são sondados em intervalos de 30 segundos.
- Os logs do IIS e os logs personalizados são coletados quando seu carimbo de data/hora é alterado. Para os logs do IIS, isso é influenciado pelo [agendamento de sobreposição configurado no IIS](../agents/data-sources-iis-logs.md). 
- A solução Replicação do Active Directory realiza sua avaliação a cada cinco dias, enquanto a solução Avaliação do Active Directory realiza uma avaliação semanal da infraestrutura do Active Directory. O agente coletará esses logs somente quando a avaliação for concluída.

### <a name="agent-upload-frequency"></a>Frequência de upload de agente
Para garantir que o agente do Log Analytics seja leve, o agente armazena em buffer os logs e carrega-os periodicamente no Azure Monitor. A frequência de upload varia entre 30 segundos e 2 minutos, dependendo do tipo de dados. A maioria dos dados é carregada em menos de 1 minuto. 

### <a name="network"></a>Rede
As condições de rede podem afetar negativamente a latência desses dados para alcançar Azure Monitor ponto de ingestão de logs.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Logs de atividades do Azure, logs de recursos e métricas
Os dados do Azure adicionam mais tempo para serem disponibilizados em Azure Monitor ponto de ingestão de logs para processamento:

- Os logs de recursos normalmente adicionam 30-90 segundos, dependendo do serviço do Azure. Alguns serviços do Azure (especificamente, o banco de dados SQL do Azure e a rede virtual do Azure) atualmente relatam seus logs em intervalos de 5 minutos. O trabalho está em andamento para melhorar ainda mais esse processo. Confira a [consulta abaixo](#checking-ingestion-time) para examinar essa latência em seu ambiente
- As métricas da plataforma Azure levam 3 minutos adicionais para serem exportadas para Azure Monitor ponto de ingestão de logs.
- Os dados do log de atividades podem levar mais de 10-15 minutos, se a integração herdada for usada. É recomendável usar as configurações de diagnóstico no nível de assinatura para ingerir logs de atividades em logs de Azure Monitor, o que incorre em uma latência adicional de aproximadamente 30 segundos.

### <a name="management-solutions-collection"></a>Coleção de soluções de gerenciamento
Algumas soluções não coletam seus dados de um agente e podem usar um método de coleta que introduz latência adicional. Algumas soluções coletam dados em intervalos regulares sem tentar a coleta quase em tempo real. Exemplos específicos incluem os seguintes:

- Microsoft 365 solução sonda logs de atividade usando a API de atividade de gerenciamento, que atualmente não fornece nenhuma garantia de latência quase em tempo real.
- Os dados das soluções de Análise do Windows (Conformidade de Atualizações, por exemplo) são coletados pela solução com uma frequência diária.

Veja a documentação de cada solução para determinar sua frequência de coleta.

### <a name="pipeline-process-time"></a>Tempo de processo de pipeline

Uma vez disponível no ponto de ingestão, os dados levam 30-60 segundos adicionais para serem disponibilizados para consulta.

Depois que os registros de log são ingeridos no pipeline de Azure Monitor (conforme identificado na propriedade [_TimeReceived](./log-standard-columns.md#_timereceived) ), eles são gravados no armazenamento temporário para garantir o isolamento do locatário e para garantir que os dados não sejam perdidos. Normalmente, esse processo adiciona 5 a 15 segundos. Algumas soluções de gerenciamento implementam algoritmos mais pesados para agregar dados e obter insights conforme os dados são recebidos. Por exemplo, o Monitoramento de Desempenho de Rede agrega os dados recebidos em intervalos de 3 minutos, efetivamente, adicionando uma latência de 3 minutos. Outro processo que adiciona latência é o processo que lida com logs personalizados. Em alguns casos, esse processo pode adicionar alguns minutos de latência aos logs que são coletados de arquivos pelo agente.

### <a name="new-custom-data-types-provisioning"></a>Provisionamento de novos tipos de dados personalizados
Quando um novo tipo de dados personalizados é criado a partir de um [log personalizado](../agents/data-sources-custom-logs.md) ou da [API do coletor de dados](../logs/data-collector-api.md), o sistema cria um contêiner de armazenamento dedicado. Essa é uma sobrecarga única que ocorre apenas na primeira aparência desse tipo de dados.

### <a name="surge-protection"></a>Proteção contra aumento
A principal prioridade do Azure Monitor é garantir que nenhum dado do cliente seja perdido e, portanto, o sistema tem uma proteção interna para aumentos de dados. Isso inclui buffers para garantir que, mesmo sob carga imensa, o sistema continue funcionando. Sob carga normal, esses controles adicionam menos de um minuto, mas em condições extremas e falhas, eles podem adicionar tempo significativo, ao mesmo tempo que garantem que os dados estão seguros.

### <a name="indexing-time"></a>Tempo de indexação
Há um equilíbrio interno para cada plataforma de Big Data entre o fornecimento de funcionalidades de análise e pesquisa avançada, em detrimento do fornecimento de acesso imediato aos dados. O Azure Monitor permite que você execute consultas avançadas em bilhões de registros e obtenha resultados em alguns segundos. Isso se torna possível porque a infraestrutura transforma os dados drasticamente durante sua ingestão e armazena-os em estruturas compactas exclusivas. O sistema armazena os dados em buffer até que dados suficientes estejam disponíveis para criar essas estruturas. Isso precisa ser concluído antes que o registro de log seja exibido nos resultados da pesquisa.

No momento, esse processo leva cerca de 5 minutos quando há um baixo volume de dados, mas menos tempo em taxas mais altas de dados. Isso parece confuso, mas esse processo permite a otimização de latência para cargas de trabalho de produção de alto volume.



## <a name="checking-ingestion-time"></a>Verificando o tempo de ingestão
O tempo de ingestão pode variar para recursos diferentes em circunstâncias diferentes. Você pode usar consultas de log para identificar um comportamento específico do seu ambiente. A tabela a seguir especifica como você pode determinar as diferentes horas para um registro conforme ele é criado e enviado para Azure Monitor.

| Etapa | Propriedade ou função | Comentários |
|:---|:---|:---|
| Registro criado na fonte de dados | [TimeGenerated](./log-standard-columns.md#timegenerated-and-timestamp) <br>Se a fonte de dados não definir esse valor, ela será definida para o mesmo horário que _TimeReceived. |
| Registro recebido por Azure Monitor ponto de extremidade de ingestão | [_TimeReceived](./log-standard-columns.md#_timereceived) | |
| Registro armazenado no espaço de trabalho e disponível para consultas | [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Atrasos de latência de ingestão
Você pode medir a latência de um registro específico comparando o resultado da função [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) com a propriedade _TimeGenerated_ . Esses dados podem ser usados com várias agregações para descobrir como a latência de ingestão se comporta. Examine alguns percentil do tempo de ingestão para obter insights para uma grande quantidade de dados. 

Por exemplo, a consulta a seguir mostrará quais computadores tiveram o maior tempo de ingestão durante as 8 horas anteriores: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

As verificações de percentil anteriores são boas para localizar tendências gerais em latência. Para identificar um pico de curto prazo em latência, usar o máximo ( `max()` ) pode ser mais eficaz.

Se você quiser fazer uma busca detalhada no tempo de ingestão de um computador específico durante um período de tempo, use a consulta a seguir, que também visualiza os dados do dia anterior em um grafo: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Use a consulta a seguir para mostrar o tempo de ingestão do computador pelo país/região em que eles estão localizados, com base em seu endereço IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Diferentes tipos de dados de origem do agente podem ter tempos de latência de ingestão diferentes, assim, as consultas anteriores podem ser usadas com outros tipos. Use a consulta a seguir para examinar o tempo de ingestão de vários serviços do Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
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
* Leia o [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/en-us/support/legal/sla/monitor/v1_3/) do Azure Monitor.
