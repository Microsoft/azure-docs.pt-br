---
title: "Noções básicas sobre alertas no Azure Log Analytics | Microsoft Docs"
description: "Alertas no Log Analytics identificam informações importantes em seu repositório do OMS e podem notificar proativamente problemas ou invocar ações para tentar corrigi-los.  Este artigo descreve os diferentes tipos de regras de alerta e como elas são definidas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: bwren
ms.openlocfilehash: e03911d589aaab0d0e80da5d58f14d6df417f4be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-alerts-in-log-analytics"></a>Noções básicas sobre alertas no Log Analytics

Alertas no Log Analytics identificam informações importante no repositório de Log Analytics.  Este artigo fornece detalhes de como regras de alerta no Log Analytics funcionam e descreve as diferenças entre diferentes tipos de regras de alerta.

Para o processo de criação de regras de alerta, consulte os artigos a seguir:

- Criar regras de alerta usando o [Portal do Azure](log-analytics-alerts-creating.md)
- Criar regras de alerta usando o [modelo do Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Criar regras de alerta usando a [API REST](log-analytics-api-alerts.md)


## <a name="alert-rules"></a>Regras de alerta

Os alertas são criados por regras de alerta que executam pesquisas de log automaticamente em intervalos regulares.  Se os resultados da pesquisa de log corresponderem a critérios específicos, um registro de alerta será criado.  A regra pode então executar automaticamente uma ou mais ações para notificar você proativamente do alerta ou invocar outro processo.  Diferentes tipos de regras de alerta usam lógicas diferentes para realizar essa análise.

![Alertas do Log Analytics](media/log-analytics-alerts/overview.png)

Regras de Alerta são definidas pelos detalhes a seguir:

- **Pesquisa de log**.  A consulta que é executada cada vez que a regra de alerta é acionada.  Os registros retornados por essa consulta serão usados para determinar se um alerta é criado.
- **Janela de tempo**.  Especifica o intervalo de tempo para a consulta.  A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo atual.  Este pode ser qualquer valor entre 5 minutos e 24 horas. Por exemplo, se a janela de tempo está definida para 60 minutos e a consulta é executada às 13h15, somente os registros criados entre 12h15 e 13h15 são retornados.
- **Frequência**.  Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual a ou menor que a janela de tempo.  Se o valor for maior que a janela de tempo, haverá o risco de que registros sejam perdidos.<br>Por exemplo, considere uma janela de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta for executada à 1:00, retornará registros entre 12:30 e 1:00.  A próxima vez em que a consulta será executada é às 2:00, quando ela retornará registros entre 1:30 e 2:00.  Todos os registros criados entre 1:00 e 1:30 nunca seriam avaliados.
- **Limite**.  Os resultados da pesquisa de logs são avaliados para determinar se um alerta deve ser criado.  O limite é diferente para os diferentes tipos de regras de alerta.

Cada regra de alerta no Log Analytics é de um entre dois tipos.  Cada um desses tipos é descrito detalhadamente nas seções a seguir.

- **[Número de resultados](#number-of-results-alert-rules)**. Alerta único criado quando o número de registros retornados pela pesquisa de logs excedeu um número especificado.
- **[Medida métrica](#metric-measurement-alert-rules)**.  Alerta criado para cada objeto nos resultados da pesquisa de logs com valores que excedem o limite especificado.

As diferenças entre tipos de regra de alerta são conforme descrito a seguir.

- A regra de alerta **Número de resultados** sempre criará um único tempo de alerta, enquanto a regra de alerta **Medição métrica** criará um alerta para cada objeto que exceder o limite.
- Regras de alerta de **Número de resultados** criarão um alerta quando o limite for excedido uma única vez. Regras de alerta de **Medição métrica** podem criar um alerta quando o limite é excedido um determinado número de vezes em um intervalo de tempo específico.

## <a name="number-of-results-alert-rules"></a>Regras de alerta de Número de resultados
Regras de alerta de **Número de resultados** criam um único alerta quando o número de registros retornados pela consulta de pesquisa excede o limite especificado.

### <a name="threshold"></a>Limite
O limite para uma regra de alerta de **Número de resultados** é simplesmente maior ou menor que um valor específico.  Se o número de registros retornados pela pesquisa de logs corresponderem a esses critérios, um alerta será criado.

### <a name="scenarios"></a>Cenários

#### <a name="events"></a>Eventos
Esse tipo de regra de alerta é ideal para trabalhar com eventos como logs de eventos do Windows, Syslog e logs Personalizados.  Pode ser útil criar um alerta quando um evento de erro específico é criado ou quando vários eventos de erros são criados dentro de uma janela de tempo específica.

Para alertar quanto a um único evento, defina o número de resultados para maior que 0 e tanto a frequência quanto a janela de tempo para 5 minutos.  Isso executa a consulta a cada 5 minutos e verifica a ocorrência de um único evento que foi criado desde a última vez em que a consulta foi executada.  Uma frequência maior pode atrasar o tempo entre o evento que está sendo coletado e o alerta criado.

Alguns aplicativos podem registrar um erro ocasional que não necessariamente gerará um alerta.  Por exemplo, o aplicativo pode repetir o processo que criou o evento de erro e depois ter êxito na próxima vez.  Nesse caso, não convém criar o alerta, a menos que vários eventos sejam criados dentro de uma janela de tempo específica.  

Em alguns casos, pode ser útil criar um alerta na ausência de um evento.  Por exemplo, um processo pode registrar eventos regulares para indicar que ele está funcionando corretamente.  Se ele não registrar um desses eventos dentro de uma janela de tempo específica, um alerta deverá ser criado.  Nesse caso você definiria o limite como **menos de 1**.

#### <a name="performance-alerts"></a>Alertas de desempenho
[Os dados de desempenho](log-analytics-data-sources-performance-counters.md) são armazenado como registros no repositório do OMS de forma semelhante aos eventos.  Se você deseja alertar quando um contador de desempenho exceder um limite específico, esse limite deve ser incluído na consulta.

Por exemplo, se você quisesse alertar quando o processador ultrapassasse 90%, usaria uma consulta como a seguinte, com o limite para a regra de alerta **maior do que 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Se você quisesse alertar quando o processador ficasse acima de 90% durante um determinado período, usaria uma consulta com o [comando measure](log-analytics-search-reference.md#commands), como no seguinte, com o limite para a regra de alerta **maior do que 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), as consultas acima seriam alteradas para o demonstrado a seguir: `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90`
> `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90`


## <a name="metric-measurement-alert-rules"></a>Regras de alerta com medição métrica

>[!NOTE]
> Regras de alerta de Medição métrica estão atualmente em visualização pública.

Regras de alerta de **Medição métrica** criam um alerta para cada objeto em uma consulta com um valor que excede um limite especificado.  Eles têm as diferenças distintas a seguir em relação às regras de alerta de **Número de resultados**.

#### <a name="log-search"></a>Pesquisa de log
Embora você possa usar qualquer consulta para uma regra de alerta de **Número de resultados**, há requisitos específicos para a consulta para uma regra de alerta de Medição métrica.  Ela deve incluir um [comando Measure](log-analytics-search-reference.md#commands) para agrupar os resultados em um determinado campo. Os cabeçalhos devem incluir os elementos a seguir.

- **Função de agregação**.  Determina o cálculo que será realizado e, potencialmente, um campo numérico a agregar.  Por exemplo, **count()** retornará o número de registros na consulta e **avg(CounterValue)** retornará a média do campo CounterValue durante o intervalo.
- **Campo Grupo**.  Um registro com um valor agregado será criado para cada instância do campo e um alerta pode ser gerado para cada um deles.  Por exemplo, se você quisesse gerar um alerta para cada computador, você usaria **by Computer**.   
- **Intervalo**.  Define o intervalo de tempo durante o qual os dados são agregados.  Por exemplo, se você especificasse **5 minutos**, um registro seria criado para cada instância do campo de grupo agregada em intervalos de 5 minutos durante a janela de tempo especificada para o alerta.

#### <a name="threshold"></a>Limite
O limite para regras de alerta de Medição métrica é definido por um valor de agregação e um número de violações.  Se qualquer ponto de dados na pesquisa de logs exceder esse valor, ele será considerado uma violação.  Se o número de violações de qualquer objeto nos resultados exceder o valor especificado, um alerta será criado para esse objeto.

#### <a name="example"></a>Exemplo
Considere um cenário em que você deseje um alerta se qualquer computador exceda 90% de utilização do processador por três vezes em 30 minutos.  Você criaria uma regra de alerta com os detalhes a seguir.  

**Consulta:** Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**Janela de tempo:** 30 minutos<br>
**Frequência de alerta:** cinco minutos<br>
**Valor de agregação:** maior que 90<br>
**Disparar alerta com base em:** total de violações maior que cinco<br>

A consulta criaria um valor médio para cada computador em intervalos de cinco minutos.  Essa consulta seria executada cada cinco minutos para os dados coletados durante os 30 minutos anteriores.  Abaixo, são mostrados dados de exemplo para três computadores.

![Resultados da consulta de exemplo](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Neste exemplo, alertas separados seriam criados para srv02 e srv03, já que eles violaram o limite de 90% três vezes durante a janela de tempo.  Se **Disparar alerta com base em:** fosse alterado para **Consecutivas**, o único alerta criado seria para srv03, já que ele violou o limite em três amostras consecutivas.

## <a name="alert-records"></a>Registros de alerta
Registros de alerta criados por regras de alerta no Log Analytics têm um **Type** que indica **Alert** e um **SourceSystem** que indica **OMS**.  Eles têm as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*OMS* |
| *Objeto*  | [Alertas de métrica de medição](#metric-measurement-alert-rules) terão uma propriedade para o campo de grupo.  Por exemplo, se a pesquisa de logs for agrupada em Computador, o registro de alerta terá um campo Computador com o nome do computador como o valor.
| AlertName |Nome do alerta. |
| AlertSeverity |Nível de severidade do alerta. |
| LinkToSearchResults |Vincular a pesquisa de log do Log Analytics que retorna os registros da consulta que criou o alerta. |
| Consultar |Texto da consulta que foi executada. |
| QueryExecutionEndTime |Término do intervalo de tempo para a consulta. |
| QueryExecutionStartTime |Início do intervalo de tempo para a consulta. |
| ThresholdOperator | Operador que foi usado pela regra de alerta. |
| ThresholdValue | Valor que foi usado pela regra de alerta. |
| TimeGenerated |Data e hora em que o alerta foi criado. |

Há outros tipos de registros de alerta criados pela [solução de Gerenciamento de Alertas](log-analytics-solution-alert-management.md) e por [exportações do Power BI](log-analytics-powerbi.md).  Eles têm um **Type** que indica **Alert**, mas são diferenciados por seus **SourceSystem**.


## <a name="next-steps"></a>Próximas etapas
* Instale a [solução de Gerenciamento de Alertas](log-analytics-solution-alert-management.md) para analisar os alertas criados no Log Analytics junto com aqueles coletados do System Center Operations Manager.
* Leia mais sobre [pesquisas de log](log-analytics-log-searches.md) que podem gerar alertas.
* Conclua um passo a passo para [configurar um webhook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
* Aprenda a criar [runbooks na Automação do Azure](https://azure.microsoft.com/documentation/services/automation) para corrigir os problemas identificados por alertas.
