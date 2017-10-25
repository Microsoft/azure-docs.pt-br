---
title: Roteiro da linguagem de consulta do Azure Log Analytics | Microsoft Docs
description: "Este artigo fornece assistência na transição para a nova linguagem de consulta do Log Analytics, caso você já esteja familiarizado com a linguagem herdada."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2017
ms.author: bwren
ms.openlocfilehash: 50713d69f6dce6b7b154b6b4a6df3f679eb7b7c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Transição para a nova linguagem de consulta do Azure Log Analytics

> [!NOTE]
> Você pode saber mais sobre a nova linguagem de consulta do Log Analytics e obter o procedimento para atualizar seu espaço de trabalho em [Atualizar seu espaço de trabalho do Azure Log Analytics para uma nova pesquisa de logs](log-analytics-log-search-upgrade.md).

Este artigo fornece assistência na transição para a nova linguagem de consulta do Log Analytics, caso você já esteja familiarizado com a linguagem herdada.

## <a name="language-converter"></a>Conversor de linguagem

Se você estiver familiarizado com a linguagem de consulta do Log Analytics herdada, a maneira mais fácil de criar a mesma consulta na nova linguagem é usar o conversor de linguagem que é instalado no portal Pesquisa de Logs quando o espaço de trabalho é convertido.  Usar o conversor é extremamente simples, basta digitar uma consulta herdada na caixa de texto superior e, em seguida, clicar em **converter**.  Você pode clicar no botão de pesquisa para executar a consulta ou copiá-la colá-la para usá-la em outro lugar.

![Conversor de linguagem](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="cheat-sheet"></a>Roteiro

A tabela a seguir fornece uma comparação entre uma variedade de consultas comuns para comandos equivalentes entre a linguagem de consulta herdada e a nova no Azure Log Analytics.

| Descrição | Herdada | novo |
|:--|:--|:--|
| Pesquisar todas as tabelas      | error | Pesquisar “erro” (não diferencia maiúsculas de minúsculas) |
| Selecione os dados da tabela | Type=Event |  Evento |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| Comparação de cadeias de caracteres      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Evento &#124; em que o Computador contém “contoso” (não diferencia maiúsculas de minúsculas)<br>Evento &#124; em que o Computador contains_cs “Contoso” (diferencia maiúsculas de minúsculas) |
|                        | Type=Event Computer=RegEx("@contoso@")  | Event &#124; where Computer matches regex ".*contoso*" |
| Comparação de datas        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &#124; where TimeGenerated between (datetime(2017-05-01) .. datetime(2017-05-31)) |
| Comparação de boolianos     | Type=Heartbeat IsGatewayInstalled=false  | Pulsação \| where IsGatewayInstalled == false |
| Classificar                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| Distinct               | Type=Event &#124; dedup Computer \| select Computer | Event &#124; summarize by Computer, EventLog |
| Estender colunas         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| Agregação            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| Agregação com limite | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| União                  | Type=Event or Type=Syslog | union Event, Syslog |
| Ingressar                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Próximas etapas
- Confira um [tutorial sobre como escrever consultas](https://go.microsoft.com/fwlink/?linkid=856078) usando a nova linguagem de consulta.
- Consulte a [Referência da linguagem de consulta](https://go.microsoft.com/fwlink/?linkid=856079) para obter detalhes sobre todas as funções, operadores e comandos para a nova linguagem de consulta.  
