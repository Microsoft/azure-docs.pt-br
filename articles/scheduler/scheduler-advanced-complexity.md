---
title: "Como criar agendamentos complexos e recorrência avançada com o Agendador do Azure"
description: "Como criar agendamentos complexos e recorrência avançada com o Agendador do Azure"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 20c3e3c1cb85308cad47054c2efa87f61cae0f22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Como criar agendamentos complexos e recorrência avançada com o Agendador do Azure
## <a name="overview"></a>Visão geral
O centro de um trabalho do Agendador do Azure é o *agendamento*. O agendamento determina quando e como o Agendador executa o trabalho.

O Agendador do Azure permite que você especifique diferentes agendamentos únicos e recorrentes para um trabalho. Os agendamentos *únicos* são acionados uma vez em um momento especificado: na verdade, eles são agendamentos *recorrentes* que são executados somente uma vez. Os agendamentos recorrentes são acionados com uma frequência predeterminada.

Com essa flexibilidade, o Agendador do Azure permite que você dê suporte a uma ampla variedade de cenários de negócios:

* Limpeza periódica de dados: por exemplo, todos os dias, excluir todos os tweets com mais de três meses
* Arquivamento: por exemplo, a cada mês, enviar o histórico da fatura para o serviço de backup
* Solicitações de dados externos: por exemplo, a cada 15 minutos, receber um novo relatório de previsão do tempo de Esqui da NOAA
* Processamento: por exemplo, a cada dia da semana, fora do horário de pico, usar computação em nuvem para compactar as imagens carregadas no dia

Neste artigo, percorreremos trabalhos de exemplo que você pode criar com o Agendador do Azure. Nós fornecemos os dados JSON que descrevem cada agendamento. Se você usar a [API REST do Agendador](https://msdn.microsoft.com/library/mt629143.aspx), poderá usar esse mesmo JSON para [criar um trabalho do Agendador do Azure](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Cenários com suporte
Os vários exemplos neste tópico ilustram a variedade de cenários com suporte no Agendador do Azure. No geral, esses exemplos ilustram como criar agendamentos para vários padrões de comportamento, incluindo estes abaixo:

* Executar uma vez em uma determinada data e hora
* Executar e repetir um número de vezes específico
* Executar imediatamente e repetir
* Executar e repetir a cada *n* minutos, horas, dias, semanas ou meses, começando em um momento específico
* Executar e repetir em frequência semanal ou mensal, mas somente em dias específicos, em dias da semana específicos ou em dias do mês específicos
* Executar e repetir várias vezes em um período: por exemplo, na última sexta-feira e segunda-feira de cada mês ou às 5h15 e 17h15 todos os dias

## <a name="dates-and-datetimes"></a>Datas e DateTimes
As datas em trabalhos do Agendador do Azure seguem a [especificação ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) e incluem apenas a data.

As referências de data e hora em trabalhos do Agendador do Azure seguem a [especificação ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) e incluem partes de data e hora. Uma data e hora que não especifique um deslocamento UTC é considerada como UTC.  

## <a name="how-to-use-json-and-rest-api-for-creating-schedules"></a>Como usar JSON e API REST para criar agendamentos
Para criar um agendamento simples usando a [API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143), primeiro [registre a sua assinatura com um provedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx) (o nome do provedor do Agendador é *Microsoft.Scheduler*), [crie uma coleção de trabalhos](https://msdn.microsoft.com/library/mt629159.aspx) e, por fim, [crie um trabalho](https://msdn.microsoft.com/library/mt629145.aspx). Quando você cria um trabalho, pode especificar o agendamento e recorrência usando JSON, como representado abaixo:

    {
        "startTime": "2012-08-04T00:00Z", // optional
         …
        "recurrence":                     // optional
        {
            "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
            "interval": 1,                // optional, how often to fire (default to 1)
            "schedule":                   // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // optional (default to recur infinitely)
            "endTime": "2012-11-04",      // optional (default to recur infinitely)
        },
        …
    }

## <a name="overview-job-schema-basics"></a>Visão geral: Noções básicas sobre esquemas de trabalho
A tabela a seguir fornece uma visão geral de alto nível dos principais elementos relacionados a recorrência e planejamento em um trabalho:

| **Nome JSON** | **Descrição** |
|:--- |:--- |
| ***startTime*** |*startTime* é uma Data/Hora. Para agendamentos simples, *startTime* é a primeira ocorrência e, para agendamentos complexos, o trabalho será iniciado somente na *startTime*. |
| ***recurrence*** |O objeto *recurrence* especifica regras de recorrência do trabalho e a recorrência de execução dele. O objeto recurrence dá suporte aos elementos *frequency, interval, endTime, count* e *schedule*. Se *recurrence* for definido, *frequency* será necessário; os outros elementos de *recurrence* serão opcionais. |
| ***frequency*** |A cadeia de caracteres *frequency* que representa a unidade de frequência com a qual o trabalho se repete. Os valores com suporte são *"minute", "hour", "day", "week"* ou *"month"*. |
| ***interval*** |O *interval* é um inteiro positivo e denota o intervalo para *frequency*, que determina a frequência de execução do trabalho. Por exemplo, se *interval* for 3 e *frequency* for "week", o trabalho se repetirá a cada três semanas. O Agendador do Azure dá suporte a um valor máximo de *interval* de 18 meses para a frequência mensal, 78 semanas para a frequência semanal ou 548 dias para a frequência diária. Para a frequência em hora e em minuto, o intervalo com suporte é 1 <= *interval* <= 1.000. |
| ***endTime*** |A cadeia de caracteres *endTime* especifica a data/hora depois da qual o trabalho não deverá ser executado. Não é válido ter um *endTime* no passado. Se nem *endTime* nem count forem especificados, o trabalho será executado de maneira infinita. Não é possível incluir *endTime* e *count* em um mesmo trabalho. |
| ***count*** |<p>O valor de *count* é um inteiro positivo (maior que zero) que especifica o número de vezes que o trabalho deve ser executado antes de ser concluído.</p><p>O elemento *count* representa o número de vezes que o trabalho é executado antes de ser considerado concluído. Por exemplo, para um trabalho executado diariamente com *count* 5 e a data inicial na segunda-feira, o trabalho será concluído depois ser executado na sexta-feira. Se a data de início estiver no passado, a primeira execução é calculada do momento da criação.</p><p>Se nem *endTime* nem *count* forem especificados, o trabalho será executado de maneira infinita. Não é possível incluir *endTime* e *count* em um mesmo trabalho.</p> |
| ***schedule*** |Um trabalho com uma frequência especificada altera sua recorrência com base em um agendamento de recorrência. Um elemento *schedule* contém as modificações com base em minutos, em horas, em dias da semana, em dias do mês e em número da semana. |

## <a name="overview-job-schema-defaults-limits-and-examples"></a>Visão geral: padrões de esquema, limites e exemplos de trabalho
Após essa visão geral, vamos examinar cada um desses elementos em detalhes.

| **Nome JSON** | **Tipo de valor** | **Obrigatório?** | **Valor padrão** | **Valores válidos** | **Exemplo** |
|:--- |:--- |:--- |:--- |:--- |:--- |
| ***startTime*** |Cadeia de caracteres |Não |Nenhum |Data e hora ISO 8601 |<code>"startTime" : "2013-01-09T09:30:00-08:00"</code> |
| ***recurrence*** |Objeto |Não |Nenhum |Objeto de recorrência |<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code> |
| ***frequency*** |string |Sim |Nenhum |"minuto", "hora", "dia", "semana", "mês" |<code>"frequency" : "hour"</code> |
| ***interval*** |Número |Não |1 |1 a 1000. |<code>"interval":10</code> |
| ***endTime*** |Cadeia de caracteres |Não |Nenhum |Valor de data e hora que representa um momento no futuro |<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
| ***count*** |Número |Não |Nenhum |>= 1 |<code>"count": 5</code> |
| ***schedule*** |Objeto |Não |Nenhum |Objeto Agendamento |<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code> |

## <a name="deep-dive-starttime"></a>Análise aprofundada: *startTime*
A tabela a seguir mostra como *startTime* controla a execução de um trabalho.

| **valor startTime** | **Sem recorrência** | **Recorrência. Sem agendamento** | **Recorrência com agendamento** |
|:--- |:--- |:--- |:--- |
| **Sem hora de início** |Executar uma vez imediatamente |Executar uma vez imediatamente. Fazer as execuções subsequentes com base no cálculo do tempo da última execução |<p>Executar uma vez imediatamente</p><p>Fazer as execuções subsequentes com base no agendamento de recorrência</p> |
| **Hora de início no passado** |Executar uma vez imediatamente |<p>Calcular a primeira hora de execução futura após a hora de início e executar naquela hora</p><p>Fazer as execuções subsequentes com base no cálculo da hora da última execução</p><p>Veja o exemplo depois desta tabela para obter uma explicação mais detalhada</p> |<p>O trabalho é iniciado *somente na* hora de início especificada. A primeira ocorrência baseia-se no agendamento calculado a partir da hora de início</p><p>Fazer as execuções subsequentes com base no agendamento de recorrência</p> |
| **Hora de início no futuro ou no momento** |Executar uma vez na hora de início especificada |<p>Executar uma vez na hora de início especificada</p><p>Fazer as execuções subsequentes com base no cálculo do tempo da última execução</p> |<p>O trabalho é iniciado *somente na* hora de início especificada. A primeira ocorrência baseia-se no agendamento calculado a partir da hora de início</p><p>Fazer as execuções subsequentes com base no agendamento de recorrência</p> |

Vejamos um exemplo do que acontece quando *startTime* está no passado, com *recurrence*, mas sem *schedule*.  Suponha que a hora atual seja 2015-04-08 13:00, que *startTime* seja 2015-04-07 14:00 e que *recurrence* seja a cada dois dias (definida com *frequency*: day e *interval*: 2.) Observe que *startTime* está no passado e ocorre antes da hora atual

Nessas condições, a *primeira execução* será em 09-04-2015 às 14:00\. O mecanismo do Agendador calcula as ocorrências de execução desde a hora de início.  As instâncias no passado serão descartadas. O mecanismo usa a próxima instância que ocorrer no futuro.  Nesse caso, *startTime* é 2015-04-07, às 14:00; portanto, a próxima instância ocorrerá dois dias depois desse momento, o que será 2015-04-09, às 14:00.

Observe que a primeira execução deve ser a mesma que o startTime seja 05-04-2015 14:00 ou 01-04-2015 14:00\. Após a primeira execução, as execuções subsequentes são calculadas usando a agendada; portanto, seriam em 11-04-2015 às 14:00, 13-04-2015 às 14:00, 15-04-2015 às 14:00, etc.

Finalmente, quando um trabalho tiver um agendamento, se as horas e/ou minutos não estiverem definidos no agendamento, serão adotadas as horas e/ou minutos da primeira execução, respectivamente.

## <a name="deep-dive-schedule"></a>Análise aprofundada: *schedule*
Por um lado, um elemento *schedule* pode *limitar* o número de execuções do trabalho.  Por exemplo, se um trabalho com o elemento frequency definido como "month" tiver um *schedule* para ser executado somente no dia 31, o trabalho será executado apenas nos meses que têm o dia 31<sup></sup>.

Por outro lado, um elemento *schedule* também pode *expandir* o número de execuções do trabalho. Por exemplo, se um trabalho com o elemento frequency definido como "month" tiver um *schedule* para ser executado nos dias 1 e 2 do mês, o trabalho será executado no 1º e 2º dias do mês em vez de apenas uma vez por mês<sup></sup><sup></sup>.

Se forem especificados vários elementos de agendamento, a ordem de avaliação é do maior para o menor: número da semana, dia do mês, dia da semana, hora e minuto.

A tabela a seguir descreve elementos de *schedule* em detalhes.

| **Nome JSON** | **Descrição** | **Valores Válidos** |
|:--- |:--- |:--- |
| **minutos** |Minutos da hora em que o trabalho será executado |<ul><li>Inteiro ou</li><li>Matriz de inteiros</li></ul> |
| **horas** |Horas do dia em que o trabalho será executado |<ul><li>Inteiro ou</li><li>Matriz de inteiros</li></ul> |
| **Dias da semana** |Dias da semana em que o trabalho será executado. Só pode ser especificado com uma frequência semanal. |<ul><li>"Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" ou "Sunday"</li><li>Matriz de qualquer um dos valores acima (tamanho máximo da matriz: 7)</li></ul>*Não* diferencia maiúsculas de minúsculas |
| **monthlyOccurences** |Determina em quais dias do mês o trabalho será executado. Só pode ser especificado com uma frequência mensal. |<ul><li>Matriz de objetos monthlyOccurrence:</li></ul> <pre>{ "day": *day*,<br />  "occurrence": *occurrence*<br />}</pre><p> *dia* é o dia da semana no qual o trabalho será executado, por exemplo, {Sunday} representa todos os domingos do mês. Obrigatório.</p><p>*ocorrência* é o valor do elemento ocurrence que se refere ao dia durante o mês, por exemplo, {Sunday, -1} é o último domingo do mês. Opcional.</p> |
| **Dias do mês** |Dia do mês em que o trabalho será executado. Só pode ser especificado com uma frequência mensal. |<ul><li>Qualquer valor <= -1 e >= -31.</li><li>Qualquer valor >= 1 e <= 31.</li><li>Uma matriz dos valores acima</li></ul> |

## <a name="examples-recurrence-schedules"></a>Exemplos: agendamentos de recorrência
Seguem diversos exemplos de agendamentos de recorrência voltados para o objeto de agendamento e seus subelementos.

Todos os agendamentos abaixo pressupõem que o *intervalo* é definido como 1\. Além disso, deve assumir a frequência correta de acordo com o que está no *agendamento*, por exemplo, não é possível usar a frequência "day" e ter uma modificação de "monthDays" no agendamento. As restrições estão descritas acima.

| **Exemplo** | **Descrição** |
|:--- |:--- |
| <code>{"hours":[5]}</code> |Executar às 5h da manhã todos os dias. O Agendador do Azure corresponde a cada valor em "horas" com cada valor em "minutos", um a um, para criar uma lista de todos os horários em que o trabalho deve ser executado. |
| <code>{"minutes":[15], "hours":[5]}</code> |Executar às 5:15 todos os dias |
| <code>{"minutes":[15], "hours":[5,17]}</code> |Executar às 5:15 e 17:15 todos os dias |
| <code>{"minutes":[15,45], "hours":[5,17]}</code> |Executar às 5:15, 5:45, 17:15 e 17:45 todos os dias |
| <code>{"minutes":[0,15,30,45]}</code> |Executar a cada 15 minutos |
| <code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code> |Executar a cada hora. Esse trabalho é executado a cada hora. O minuto será controlado por *startTime*, se especificado, caso contrário, será controlado pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 00:25, o trabalho será executado às 00:25, 01:25, 02:25, …, 23:25. O agendamento é equivalente a ter um trabalho com *frequency* de "hour", *interval* de 1 e sem *schedule*. A diferença é que esse agendamento também pode ser usado com valores diferentes de *frequency* e de *interval* para criar outros trabalhos. Por exemplo, se *frequency* fosse "month", o agendamento seria executado somente uma vez por mês, em vez de todos os dias se *frequency* fosse "day" |
| <code>{minutes:[0]}</code> |Execute a cada hora em hora exata. Esse trabalho também é executado a cada hora, mas na hora exata (por exemplo, 00:00, 1:00, 2:00, etc.) Isso equivale a um trabalho com frequência de "hora", startTime com zero minutos e sem agendamento se a frequência fosse "dia", mas, sendo a frequência "semana" ou "mês", o agendamento deve executar apenas uma vez por semana ou por mês, respectivamente. |
| <code>{"minutes":[15]}</code> |Executar 15 minutos após a hora exata a cada hora. É executado a cada hora, começando em 00:15, 1:15, 2:15, etc. e terminando às 22:15 e 23:15. |
| <code>{"hours":[17], "weekDays":["saturday"]}</code> |Executar às 17h aos sábados toda semana |
| <code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Executar às 17h às segundas-feiras, quartas-feiras e sextas-feiras toda semana |
| <code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Executar às 17:15 e 17:45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana |
| <code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Executar às 5h e às 17h toda semana às segundas-feiras, quartas-feiras e sextas-feiras |
| <code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Executar às 5:15, 5:45, 17:15 e 17:45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana |
| <code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Executar a cada 15 minutos em dias da semana |
| <code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Executar a cada 15 minutos nos dias úteis entre 9:00 e 16:45 |
| <code>{"weekDays":["sunday"]}</code> |Executar aos domingos na hora de início |
| <code>{"weekDays":["tuesday", "thursday"]}</code> |Executar às terças e quintas-feiras na hora de início |
| <code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code> |Executar às 6h no 28º dia de cada mês (supondo a frequência do mês) |
| <code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code> |Executar às 6h no último dia do mês. Se você quiser executar um trabalho no último dia de um mês, use -1 em vez de dia 28, 29, 30 ou 31. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code> |Executar às 6h no primeiro e no último dia de cada mês |
| <code>{monthDays":[1,-1]}</code> |Executar no primeiro e no último dia de cada mês na hora de início |
| <code>{monthDays":[1,14]}</code> |Executar no primeiro e no décimo quarto dia de cada mês na hora de início |
| <code>{monthDays":[2]}</code> |Executar no segundo dia do mês na hora de início |
| <code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |Executar na primeira sexta-feira de cada mês às 5h |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |: Executar na primeira sexta-feira de cada mês na hora de início |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code> |Executar na terceira sexta-feira do final do mês, todo mês, na hora de início |
| <code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Executar na primeira e na última sexta-feira de cada mês às 5:15 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Executar na primeira e na última sexta-feira de cada mês na hora de início |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code> |Executar na quinta sexta-feira de cada mês na hora de início. Se não houver nenhuma quinta sexta-feira em um mês, ele não é executado, uma vez que está agendado para executar apenas nas quintas sextas-feiras. Considere o uso de -1 em vez de 5 na ocorrência se você quiser executar o trabalho na última sexta-feira do mês. |
| <code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code> |Executar a cada 15 minutos na última sexta-feira do mês |
| <code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code> |Executar às 5:15, 5:45, 17:15 e 17:45 na terceira quarta-feira de cada mês |

## <a name="see-also"></a>Consulte também
 [O que é o Agendador?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)

 [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador do Azure](scheduler-high-availability-reliability.md)

 [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador do Azure](scheduler-outbound-authentication.md)

