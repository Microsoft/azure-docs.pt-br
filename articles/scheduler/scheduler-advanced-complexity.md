---
title: Crie agendas e recorrências de trabalhos avançadas
description: Saiba como criar agendamentos avançados e recorrências para trabalhos no Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 5a74240e3f116121c0aaddd11c186e6e674ea26a
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368172"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Criar agendamentos avançados e recorrências para trabalhos no Agendador do Azure

> [!IMPORTANT]
> Os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) estão substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível. 
>
> O Agendador não está mais disponível no portal do Azure, mas a [API REST](/rest/api/scheduler) e os [cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md) permanecem disponíveis no momento para que você possa gerenciar seus trabalhos e suas coleções de trabalhos.

Dentro de um trabalho do [Agendador do Azure](../scheduler/scheduler-intro.md), a agenda é o núcleo que determina quando e como o serviço do Agendador executa o trabalho. É possível definir agendamentos recorrentes e únicos para um trabalho com o Agendador. Os agendamentos únicos são executados apenas uma vez no horário especificado e são basicamente agendamentos recorrentes que são executados apenas uma vez. Os agendamentos recorrentes são executados em uma frequência especificada. Com esta flexibilidade, é possível usar o Agendador para vários cenários empresariais, por exemplo:

* **Limpar dados regularmente**: crie um trabalho diário que exclui todos os tweets com mais de três meses.

* **Arquivar dados**: crie um trabalho mensal que envia por push o histórico de fatura para um serviço de backup.

* **Solicitar dados externos**: crie um trabalho que é executado a cada 15 minutos e recebe um novo relatório de previsão do tempo do NOAA.

* **Processar imagens**: crie um trabalho de dia da semana que é executado fora dos horários de pico e usa a computação em nuvem para a compactação de imagens carregadas durante o dia.

Este artigo descreve trabalhos de exemplo que você pode criar usando o Agendador e a [API REST do Agendador do Azure](/rest/api/scheduler) e inclui a definição do JSON (JavaScript Object Notation) para cada agendamento. 

## <a name="supported-scenarios"></a>Cenários com suporte

Estes exemplos mostram a variedade de cenários compatíveis com o Agendador do Azure e como criar agendamentos para diversos padrões de comportamento, por exemplo:

* Executar uma vez em uma data e hora específicas.
* Executar e repetir um número específico de vezes.
* Executar imediatamente e repetir.
* Executar e repetir a cada *n* minutos, horas, dias, semanas ou meses, iniciando em um horário específico.
* Executar e repetir semanalmente ou mensalmente, mas apenas em dias específicos da semana ou em dias específicos do mês.
* Executar e repetir mais de uma vez por um período específico. Por exemplo, todos os meses, na última sexta-feira e última segunda-feira, ou diariamente às 5:15 e às 17:15.

Mais tarde este artigo descreve esses cenários mais detalhadamente.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Criar o agendamento com a API REST

Para criar um agendamento básico com a [API REST do Agendador do Azure](/rest/api/scheduler), siga estas etapas:

1. Registre sua assinatura do Azure com um provedor de recursos usando a [Operação de registro – API REST do Resource Manager](/rest/api/resources/providers). O nome do provedor do serviço do Agendador do Azure é **Microsoft.Scheduler**. 

1. Crie uma coleção de trabalhos usando a [Operação de criação ou atualização para coleções de trabalhos](/rest/api/scheduler/jobcollections) na API REST do Agendador. 

1. Crie um trabalho usando a [Operação de criação ou atualização para trabalhos](/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Elementos de esquema do trabalho

Esta tabela fornece uma visão geral de alto nível para os principais elementos JSON que podem ser usados ao configurar recorrências e agendamentos para trabalhos. 

| Elemento | Obrigatório | Descrição | 
|---------|----------|-------------|
| **startTime** | Não | Um valor de cadeia de caracteres de DateTime em [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica quando o trabalho é iniciado em um cronograma básico. <p>Para agendas complexas, o trabalho não inicia antes do **startTime**. | 
| **Recurrence** | Não | As regras de recorrência para quando o trabalho é executado. O objeto de **recorrência** tem suporte para estes elementos: **frequency**, **interval**, **schedule**, **count** e **endTime**. <p>Se você usar o elemento **recurrence**, também deverá usar o **frequency**, enquanto outros elementos **recurrence** são opcionais. |
| **frequência** | Sim, quando você usa **recurrence** | A unidade de tempo entre ocorrências e a compatibilidade desses valores: "Minute", "Hour", "Day", "Week", "Month" e "Year" | 
| **intervalo** | Não | Um inteiro positivo que determina o número de unidades de tempo entre ocorrências com base em **frequency**. <p>Por exemplo, se **interval** for 10 e **frequency** for "Week", o trabalho se repetirá a cada 10 semanas. <p>Aqui estão o maior número de intervalos para cada frequência: <p>– 18 meses <br>– 78 semanas <br>– 548 dias <br>– Para horas e minutos, o intervalo é 1 <= <*interval*> <= 1000. | 
| **agendamento** | Não | Defina as alterações para a recorrência com base nas marcas de minuto, marcas de hora, dias da semana e dias do mês especificados | 
| **contagem** | Não | Um número inteiro positivo que especifica o número de vezes que o trabalho é executado antes de ser concluído. <p>Por exemplo, quando um trabalho diário tem **count** definido como 7 e a data de início é Monday, o trabalho conclui a execução em um domingo. Se a data de início já tiver passado, a primeira execução será calculada do momento da criação. <p>Sem **endTime** ou **count**, o trabalho será executado de maneira infinita. Não é possível usar **count** e **endTime** no mesmo trabalho, mas a regra que termina primeiro é respeitada. | 
| **Final** | Não | Um valor de cadeia de caracteres de Date ou DateTime no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica quando o trabalho para de ser executado. Você pode definir um valor para **endTime** que está no passado. <p>Sem **endTime** ou **count**, o trabalho será executado de maneira infinita. Não é possível usar **count** e **endTime** no mesmo trabalho, mas a regra que termina primeiro é respeitada. |
|||| 

Por exemplo, esse esquema JSON descreve um agendamento básico e a recorrência para um trabalho: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Valores de Dates e DateTime*

* As datas em trabalhos do Agendador incluem apenas a data e seguem a [especificação da ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

* Os valores de hora e data em trabalhos do Agendador incluem a data e a hora, seguem a [especificação da ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e são considerados UTC quando nenhuma diferença de UTC é especificada. 

Para obter mais informações, confira [Conceitos, terminologia e entidades](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Detalhes: startTime

Esta tabela descreve como **startTime** controla como um trabalho é executado:

| startTime | Sem recorrência | Recorrência, sem agendamento | Recorrência com agendamento |
|-----------|---------------|-------------------------|--------------------------|
| **Sem hora de início** | Executar uma vez imediatamente. | Executar uma vez imediatamente. Executar as execuções subsequentes calculadas a partir do último tempo de execução. | Executar uma vez imediatamente. Realizar as execuções subsequentes com base no agendamento de recorrência. | 
| **Hora de início no passado** | Executar uma vez imediatamente. | Calcular a primeira hora de execução futura após a hora de início e executar naquela hora. <p>Executar as execuções subsequentes calculadas a partir do último tempo de execução. <p>Confira o exemplo depois desta tabela. | Inicie o trabalho *somente na* hora de início especificada. A primeira ocorrência baseia-se no elemento schedule calculado com base na hora de início. <p>Realizar as execuções subsequentes com base no agendamento de recorrência. | 
| **Hora de início no futuro ou a hora atual** | Executar uma vez na hora de início especificada. | Executar uma vez na hora de início especificada. <p>Executar as execuções subsequentes calculadas a partir do último tempo de execução. | Inicie o trabalho *somente na* hora de início especificada. A primeira ocorrência baseia-se no elemento schedule calculado com base na hora de início. <p>Realizar as execuções subsequentes com base no agendamento de recorrência. |
||||| 

Suponha que esse exemplo com estas condições: uma hora de início no passado com uma recorrência, mas sem agendamento.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* A data e a hora atuais são 08 de abril de 2015 às 13h.

* A data e a hora de início são 07 de abril de 2015 às 14h, que é antes da data e hora atuais.

* A recorrência é a cada dois dias.

1. Nessas condições, a primeira execução é em 09 de abril de 2015 às 14h. 

   O Agendador calcula as ocorrências de execução com base na hora de início, descarta todas as instâncias no passado e usa a próxima instância no futuro. 
   Nesse caso, **startTime** é em 07 de abril de 2015 às 14h, portanto, a próxima instância será dois dias a partir dessa hora, que será em 09 de abril de 2015 às 14h.

   A primeira execução é igual quer **startTime** seja 2015-04-05 14:00 quer seja 2015-04-01 14:00. Após a primeira execução, as execuções seguintes são calculadas com base no agendamento. 
   
1. As execuções ocorrem nesta ordem: 
   
   1. 11/04/2015 às 14h
   1. 13/04/2015 às 14h 
   1. 15/04/2015 às 14h
   1. E assim por diante...

1. Por fim, quando um trabalho tiver um agendamento, mas não tiver horas e minutos especificados, esses valores assumem como padrão as horas e minutos na primeira execução, respectivamente.

<a name="schedule"></a>

## <a name="details-schedule"></a>Detalhes: agendamento

Você pode usar **schedule** para *limitar* o número de execuções de trabalho. Por exemplo, se um trabalho com uma **frequência** de "month" tiver uma agenda que executa somente no dia 31, o trabalho será executado apenas em meses que tenham um dia 31.

Também é possível usar **schedule** para *expandir* o número de execuções de trabalho. Por exemplo, se um trabalho com uma **frequência** de "month" tiver uma agenda para ser executado nos dias 1 e 2 do mês, o trabalho será executado no primeiro e segundo dias do mês, em vez de apenas uma vez por mês.

Se você especificar mais de um elemento de agenda, a ordem de avaliação será do maior para o menor: número da semana, dia do mês, dia da semana, hora e minuto.

A tabela a seguir descreve elementos schedule em detalhes:

| Nome JSON | Descrição | Valores válidos |
|:--- |:--- |:--- |
| **alguns** |Minutos da hora em que o trabalho executa. |Uma matriz de inteiros. |
| **duração** |Horas do dia em que o trabalho executa. |Uma matriz de inteiros. |
| **Dias da semana** |Dias da semana em que o trabalho executa. Pode ser especificado apenas com uma frequência semanal. |Uma matriz de qualquer um dos valores a seguir (o tamanho máximo da matriz é 7):<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />Não diferencia maiúsculas de minúsculas. |
| **monthlyOccurrences** |Determina em quais dias do mês o trabalho executa. Pode ser especificado apenas com uma frequência mensal. |Uma matriz de objetos **monthlyOccurrences**:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** é o dia da semana em que o trabalho executa. Por exemplo, *{Sunday}* são todos os domingos do mês. Obrigatórios.<br /><br />**occurrence** é a ocorrência do dia durante o mês. Por exemplo,  *{Sunday, -1}* é o último domingo do mês. Opcional. |
| **monthDays** |Dia do mês em que o trabalho executa. Pode ser especificado apenas com uma frequência mensal. |Uma matriz dos valores a seguir:<br />- Qualquer valor <= -1 e >= -31<br />- Qualquer valor >= 1 e <= 31|

## <a name="examples-recurrence-schedules"></a>Exemplos: agendamentos de recorrência

Os exemplos a seguir mostram vários agendamentos de recorrência. Os exemplos concentram-se no objeto de agendamento e seus subelementos.

Esses agendamentos assumem que **intervalo** está definido para 1\. Os exemplos também assumem os valores de **frequência** corretos para os valores na **agenda**. Por exemplo, não é possível usar uma **frequência** de "day" e ter uma modificação **monthDays** na **agenda**. Nós descrevemos essas restrições no início do artigo.

| Exemplo | Descrição |
|:--- |:--- |
| `{"hours":[5]}` |Executar às 5:00 todos os dias.<br /><br />O Agendador corresponde a cada valor em "hours" com cada valor em "minutes", um a um, para criar uma lista de todos os horários em que o trabalho é executado. |
| `{"minutes":[15], "hours":[5]}` |Executar às 5h15 todos os dias. |
| `{"minutes":[15], "hours":[5,17]}` |Executar às 5h15 e 17h15 todos os dias. |
| `{"minutes":[15,45], "hours":[5,17]}` |Executar às 5h15, 5h45, 17h15 e 17h45 todos os dias. |
| `{"minutes":[0,15,30,45]}` |Executar a cada 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Executar a cada hora.<br /><br />Esse trabalho é executado a cada hora. O minuto é controlado pelo valor de **startTime**, se especificado. Se nenhuma valor **startTime** for especificado, o minuto será controlado pelo tempo de criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 00:25, o trabalho é executado às 00:25, 01:25, 02:25, …, 23:25.<br /><br />O agendamento é o mesmo que um trabalho com **frequency** de "hour", **interval** de 1 e sem valor **schedule**. A diferença é que você pode usar essa agenda com diferentes **frequency** e **interval** para criar outros trabalhos. Por exemplo, se **frequency** for "month", a agenda executa somente uma vez por mês em vez de todos os dias (se **frequency** fosse "day"). |
| `{minutes:[0]}` |Executar a cada hora na hora.<br /><br />Esse trabalho também executa a cada hora, mas na hora exata (00:00, 1:00, 2:00, e assim por diante). Essa agenda é a mesma que um trabalho com uma **frequência** de "hour", um valor **startTime** se zero minutos e sem **agenda**, se a frequência for "day". No entanto, se valor de **frequência** for "week" ou "month", a agenda executa somente um dia da semana ou um dia do mês, respectivamente. |
| `{"minutes":[15]}` |Executar 15 minutos após a hora exata a cada hora.<br /><br />Executa a cada hora, iniciando em 00:15, 1:15, 2:15, e assim por diante. Termina às 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Executar às 17:00 aos sábados todas as semanas. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 17:00 nas segundas-feiras, quartas-feiras e sextas-feiras todas as semanas. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 17h15 e às 17h45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 17:00 e às 17:00 às segundas-feiras, quartas-feiras e sextas-feiras todas as semanas. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 5:15, 5:45, 17:15 e 17:45 às segundas-feiras, quartas-feiras e sextas-feiras todas as semanas. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Executar a cada 15 minutos em dias da semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Executar a cada 15 minutos nos dias da semana, entre às 9:00 e às 16:45. |
| `{"weekDays":["sunday"]}` |Executar aos domingos na hora de início. |
| `{"weekDays":["tuesday", "thursday"]}` |Executar às terças-feiras e quintas-feiras na hora de início. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Executar às 6h no 28º dia de cada mês (supondo um valor de **frequency** igual a “month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Executar às 6:00 no último dia do mês.<br /><br />Se você quiser executar um trabalho no último dia de um mês, use -1 em vez de dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Executar às 6:00 no primeiro e no último dia de cada mês. |
| `{monthDays":[1,-1]}` |Executar no primeiro e no último dia de cada mês na hora de início. |
| `{monthDays":[1,14]}` |Executar no primeiro e 14º dia de cada mês na hora de início. |
| `{monthDays":[2]}` |Executar no segundo dia do mês na hora de início. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Executar na primeira sexta-feira de cada mês às 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Executar na primeira sexta-feira de cada mês na hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Executar na terceira sexta-feira do final do mês, todos os meses, na hora de início. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Executar na primeira e última sexta-feira de cada mês às 5h15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Executar na primeira e última sexta-feira de cada mês na hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Executar na quinta sexta-feira de cada mês na hora de início.<br /><br />Se não houver quinta sexta-feira em um mês, o trabalho não será executado. Considere o uso de -1 em vez de 5 na ocorrência se você quiser executar o trabalho na última sexta-feira do mês. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Executar a cada 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Executar às 5h15, 5h45, 17h15 e 17h45 na terceira quarta-feira de cada mês. |

## <a name="next-steps"></a>Próximas etapas

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Referência da API REST do Agendador do Azure](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)
* [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)