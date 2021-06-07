---
title: Gatilhos e execução de pipeline no Azure Data Factory
description: Este artigo fornece informações sobre como executar um pipeline no Azure Data Factory sob demanda ou criando um gatilho.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/05/2018
ms.openlocfilehash: 271dbd87950018cebbd23841d32324afa42511e7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785794"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Gatilhos e execução de pipeline no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-scheduling-and-execution.md)
> * [Versão atual](concepts-pipeline-execution-triggers.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma _execução de pipeline_ no Azure Data Factory define uma instância de uma execução de pipeline. Por exemplo, digamos que você tem um pipeline que é executado às 8h, 9h e 10h. Nesse caso, há três execuções separadas do pipeline ou execuções de pipeline. Cada execução de pipeline possui uma ID de execução de pipeline exclusiva. A ID de execução é um GUID que define exclusivamente essa execução de pipeline específica.

As execuções de pipeline normalmente são instanciadas por meio da passagem de argumentos para parâmetros definidos no pipeline. Você pode executar um pipeline manualmente ou usando um _gatilho_. Este artigo fornece detalhes sobre as duas maneiras de executar um pipeline.

## <a name="manual-execution-on-demand"></a>Execução manual (sob demanda)

A execução manual de um pipeline é também conhecida como execução _sob demanda_.

Por exemplo, digamos que você tenha um pipeline básico denominado **copyPipeline** que você deseja executar. O pipeline tem uma única atividade que copia de uma pasta de origem em um armazenamento de Blobs do Azure para uma pasta de destino no mesmo armazenamento. A definição de JSON a seguir mostra esse pipeline de exemplo:

```json
{
    "name": "copyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "name": "CopyBlobtoBlob",
                "inputs": [
                    {
                        "referenceName": "sourceBlobDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "sinkBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "parameters": {
            "sourceBlobContainer": {
                "type": "String"
            },
            "sinkBlobContainer": {
                "type": "String"
            }
        }
    }
}
```

Na definição JSON, o pipeline usa dois parâmetros: **sourceBlobContainer** e **sinkBlobContainer**. Você passa valores para esses parâmetros em runtime.

Você pode executar o pipeline manualmente usando um dos seguintes métodos:
- SDK .NET
- Módulo do Azure PowerShell
- API REST
- SDK do Python

### <a name="rest-api"></a>API REST

O comando de exemplo a seguir mostra como executar o pipeline usando a API REST manualmente:

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Para obter um exemplo completo, confira o [Início Rápido: criar um data factory usando a API REST](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando de exemplo abaixo mostra como executar o pipeline manualmente usando o Azure PowerShell:

```powershell
Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Você passa parâmetros no corpo do conteúdo da solicitação. No SDK do .NET, no Azure PowerShell e no SDK do Python, você passa valores em um dicionário que é passado como um argumento para a chamada:

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

O conteúdo da resposta é uma ID exclusiva do pipeline de execução:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

Para obter um exemplo completo, confira o [Início Rápido: criar um data factory usando o Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="net-sdk"></a>SDK .NET

A chamada de exemplo a seguir mostra como executar o pipeline usando o SDK do .NET manualmente:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Para obter um exemplo completo, confira o [Início Rápido: criar um data factory usando o SDK do .NET](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> Você pode usar o SDK do .NET para invocar Data Factory pipelines do Azure Functions, de seus serviços Web e assim por diante.

## <a name="trigger-execution"></a>Execução do gatilho

Os gatilhos são outra forma de realizar uma execução do pipeline. Os gatilhos representam uma unidade de processamento que determina quando uma execução de pipeline precisa ser inicializada. Atualmente, o Data Factory dá suporte a três tipos de gatilhos:

- Gatilho de agenda: um gatilho que invoca um pipeline em um agendamento de relógio.

- Gatilho de janela em cascata: um gatilho que opera em um intervalo periódico enquanto também mantém o estado.

- Gatilho baseado em eventos: um disparador que responde a um evento.

Pipelines e gatilhos têm uma relação muitos-para-muitos (exceto para o gatilho de janela em cascata). Vários gatilhos podem disparar um único pipeline, ou um único gatilho pode disparar vários pipelines. Na definição de gatilho a seguir, a propriedade **pipelines** se refere a uma lista de canais que são disparados pelo gatilho específico. A definição da propriedade inclui valores para os parâmetros de pipeline.
### <a name="basic-trigger-definition"></a>Definição básica do gatilho

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>Gatilho de agendamento
Um gatilho de agendamento executa pipelines em um agendamento de hora de relógio. Esse gatilho dá suporte às opções de calendário periódicas e avançadas. Por exemplo, os gatilho dá suporte a intervalos como "semanalmente" ou "Segunda-feira às 17h e quinta-feira às 21h”. O gatilho de agendamento é flexível porque o padrão de conjunto de dados é independente e o gatilho não distingue entre os dados de série temporal e não temporal.

Para obter mais informações sobre gatilhos de agendamento e, para obter exemplos, consulte [criar um gatilho que executa um pipeline em um agendamento](how-to-create-schedule-trigger.md).

## <a name="schedule-trigger-definition"></a>Definição do gatilho de agenda
Quando você cria um gatilho de agenda, especifica o agendamento e a recorrência usando uma definição JSON.

Para que o gatilho de agendamento dispare uma execução de pipeline, inclua uma referência de pipeline do pipeline específico na definição do gatilho. Pipelines e gatilhos têm uma relação de muitos para muitos. Vários gatilhos podem disparar um único pipeline. Um único gatilho pode disparar vários pipelines.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
            {
              "day": <<Monday-Sunday>>,
              "occurrence": <<1-5>>
            }
          ]
        }
      }
    },
  "pipelines": [
    {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "<Name of your pipeline>"
      },
      "parameters": {
        "<parameter 1 Name>": {
          "type": "Expression",
          "value": "<parameter 1 Value>"
        },
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> A propriedade **parameters** é uma propriedade obrigatória do elemento **pipelines**. Se o pipeline não usar nenhum parâmetro, será necessário incluir uma definição de JSON vazia na propriedade **parameters**.

### <a name="schema-overview"></a>Visão geral do esquema
A tabela a seguir fornece uma visão geral de alto nível dos principais elementos de esquema relacionados à recorrência e ao agendamento de um gatilho:

| Propriedade JSON | Descrição |
| --- | --- |
| **startTime** | Um valor de data/hora. Para agendamentos básicos, o valor da propriedade **startTime** se aplica à primeira ocorrência. Para agendamentos complexos, o gatilho não é iniciado antes do valor de **startTime** especificado. |
| **Final** | A data e a hora de início do gatilho. O gatilho não é executado após a data e a hora de término especificadas. O valor da propriedade não pode estar no passado. <!-- This property is optional. --> |
| **Fuso horário** | O fuso horário. Para obter uma lista de fusos horários com suporte, consulte [criar um gatilho que executa um pipeline em um agendamento](how-to-create-schedule-trigger.md#time-zone-option). |
| **Recurrence** | Um objeto de recorrência que especifica as regras de recorrência para o gatilho. O objeto de recorrência dá suporte aos elementos **frequência**, **intervalo**, **EndTime**, **contagem** e **agendamento** . Quando um objeto de recorrência é definido, o elemento **frequency** é obrigatório. Os outros elementos do objeto de recorrência são opcionais. |
| **frequência** | A unidade de frequência com a qual o gatilho se repete. Os valores com suporte incluem “minute”, “hour”, “day”, “week” e “month”. |
| **intervalo** | Um número inteiro positivo que indica o intervalo para o valor de **frequency**. O valor de **frequency** determina a frequência na qual o gatilho é executado. Por exemplo, se **interval** for 3 e **frequency** for "week", o gatilho será repetido a cada três semanas. |
| **agendamento** | O agendamento de recorrência do gatilho. Um gatilho com um valor de **frequency** especificado altera sua recorrência com base em um agendamento de recorrência. A propriedade **schedule** contém modificações para a recorrência que se baseiam em minutos, horas, dias da semana, dias do mês e número da semana. |

### <a name="schedule-trigger-example"></a>Exemplo de gatilho de elemento schedule

```json
{
  "properties": {
    "name": "MyTrigger",
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": "Hour",
        "interval": 1,
        "startTime": "2017-11-01T09:00:00-08:00",
        "endTime": "2017-11-02T22:00:00-08:00"
      }
    },
    "pipelines": [{
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToBlobPipeline"
        },
        "parameters": {}
      },
      {
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToAzureSQLPipeline"
        },
        "parameters": {}
      }
    ]
  }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Padrões, limites e exemplos de esquema

| Propriedade JSON | Type | Obrigatório | Valor padrão | Valores válidos | Exemplo |
| --- | --- | --- | --- | --- | --- |
| **startTime** | Cadeia de caracteres | Sim | Nenhum | Data/hora ISO 8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **Recurrence** | object | Sim | Nenhum | Um objeto de recorrência | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **intervalo** | número | Não | 1 | 1 a 1000 | `"interval":10` |
| **Final** | Cadeia de caracteres | Sim | Nenhum | Um valor de Data/Hora que representa uma hora no futuro | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **agendamento** | object | Não | Nenhum | Um objeto do agendamento | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Propriedade startTime
A seguinte tabela mostra como a propriedade **startTime** controla uma execução de gatilho:

| Valor startTime | Recorrência sem agendamento | Recorrência com agendamento |
| --- | --- | --- |
| **A hora de início está no passado** | Calcula a primeira hora de execução futura após a hora de início e executa nessa hora.<br /><br />Executa as execuções subsequentes calculadas a partir do último tempo de execução.<br /><br />Consulte o exemplo após esta tabela. | O gatilho _não inicia antes_ da hora de início especificada. A primeira ocorrência se baseia no agendamento, calculada com base na hora de início.<br /><br />Executa as execuções seguintes com base no agendamento de recorrência. |
| **A hora de início está no futuro ou é a hora atual** | É executado uma vez na hora de início especificada.<br /><br />Executa as execuções subsequentes calculadas a partir do último tempo de execução. | O gatilho é iniciado _não antes da_ hora de início especificada. A primeira ocorrência baseia-se no elemento schedule calculado com base na hora de início.<br /><br />Executa as execuções seguintes com base no agendamento de recorrência. |

Vejamos um exemplo do que acontece quando a hora de início está no passado, com uma recorrência, mas sem agendamento. Suponha que a hora atual seja 8/4/2017 às 13h, a hora de início seja 7/4/2017 às 14h e a recorrência seja a cada dois dias. (O valor de **recorrência** é definido definindo a propriedade **Frequency** como "Day" e a propriedade **Interval** como 2.) Observe que o valor **StartTime** está no passado e ocorre antes da hora atual.

Sob essas condições, a primeira execução será em 9/4/2017 às 14h. O mecanismo do Agendador calcula as ocorrências de execução desde a hora de início. As instâncias no passado serão descartadas. O mecanismo usa a próxima instância que ocorrer no futuro. Neste cenário, a hora de início é 7/4/2017 às 14h. A próxima instância é dois dias a partir dessa hora, que é 9/4/2017 às 14h.

A primeira hora de execução é igual, mesmo que **startTime** seja 5/4/2017 às 14h ou 1/4/2017 às 14h. Após a primeira execução, as execuções seguintes são calculadas com o uso do agendamento. Portanto, as execuções subsequentes serão em 11/4/2017 às 14h, em 13/4/2017 às 14h, em 15/4/2017 às 14h e assim por diante.

Por fim, quando as horas ou os minutos não estiverem definidos no agendamento de um gatilho, as horas ou os minutos da primeira execução serão usados como padrões.

### <a name="schedule-property"></a>Propriedade schedule
Você pode usar **schedule** para *limitar* o número de execuções de gatilho. Por exemplo, se um gatilho com uma frequência mensal é agendado para execução somente no dia 31, ele é executado apenas nos meses que têm o 31º dia.

Também é possível usar **schedule** para *expandir* o número de execuções de gatilho. Por exemplo, um gatilho com uma frequência mensal agendado para execução nos dias 1 e 2 do mês é executado no primeiro e no segundo dias do mês, em vez de uma vez por mês.

Se vários elementos de **agendamento** forem especificados, a ordem de avaliação será da maior para a menor configuração de agenda: número da semana, dia do mês, dia da semana, hora, minuto.

A seguinte tabela descreve elementos **schedule** em detalhes:

| Elemento JSON | Descrição | Valores válidos |
| --- | --- | --- |
| **minutes** | Minutos da hora em que o gatilho será executado. |- Número inteiro<br />- Matriz de números inteiros |
| **duração** | As horas do dia em que o gatilho será executado. |- Número inteiro<br />- Matriz de números inteiros |
| **Dias da semana** | Dias da semana em que o gatilho será executado. O valor pode ser especificado apenas com uma frequência semanal.|<br />- Segunda-feira<br />- Terça-feira<br />- Quarta-feira<br />- Quinta-feira<br />- Sexta-feira<br />- Sábado<br />- Domingo<br />- Matriz de valores de dia (o tamanho máximo da matriz é 7)<br /><br />Os valores de dia não diferenciam maiúsculas de minúsculas |
| **monthlyOccurrences** | Dias do mês em que o gatilho é executado. O valor pode ser especificado apenas com uma frequência mensal. |- Matriz de objetos **monthlyOccurrence**: `{ "day": day, "occurrence": occurrence }`<br />- O atributo **day** é o dia da semana no qual o gatilho é executado. Por exemplo, uma propriedade **monthlyOccurrences** com um valor de **day** igual a `{Sunday}` significa todo domingo do mês. O atributo **day** é obrigatório.<br />- O atributo **occurrence** é a ocorrência de **day** especificado durante o mês. Por exemplo, uma propriedade **monthlyOccurrences** com os valores de **day** e **occurrence** iguais a `{Sunday, -1}` significa o último domingo do mês. O atributo **occurrence** é opcional. |
| **monthDays** | Dia do mês em que o gatilho é executado. O valor pode ser especificado apenas com uma frequência mensal. |- Qualquer valor <= -1 e >= -31<br />- Qualquer valor >= 1 e <= 31<br />- Matriz de valores |

## <a name="tumbling-window-trigger"></a>Gatilho de janela em cascata
Os gatilhos de janela em cascata são um tipo de gatilho acionado em um intervalo de tempo periódico a partir de uma hora de início especificada, enquanto mantém o estado. As janelas em cascata são uma série de intervalos de tempo de tamanho fixo, não sobrepostos e contíguos.

Para obter mais informações sobre gatilhos de janela em cascata e, para obter exemplos, consulte [criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md).

## <a name="examples-of-trigger-recurrence-schedules"></a>Exemplos de agendamentos de recorrência de gatilho

Esta seção fornece exemplos de agendamentos de recorrência. Ela tem como foco o objeto **schedule** e seus elementos.

Os exemplos supõem que o valor do **intervalo** é 1 e que o valor de **Frequency** está correto de acordo com a definição da agenda. Por exemplo, você não pode ter um valor de **Frequency** de "Day" e também ter uma modificação **monthDays** no objeto **Schedule** . Esses tipos de restrições estão descritos na tabela da seção anterior.

| Exemplo | Descrição |
| --- | --- |
| `{"hours":[5]}` | Executar às 5h todos os dias. |
| `{"minutes":[15], "hours":[5]}` | Executar às 5h15 todos os dias. |
| `{"minutes":[15], "hours":[5,17]}` | Executar às 5h15 e 17h15 todos os dias. |
| `{"minutes":[15,45], "hours":[5,17]}` | Executar às 5h15, 5h45, 17h15 e 17h45 todos os dias. |
| `{"minutes":[0,15,30,45]}` | Executar a cada 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Executar a cada hora.<br /><br />Este gatilho é executado a cada hora. Os minutos são controlados pelo valor de **startTime**, quando um valor é especificado. Se um valor não for especificado, os minutos serão controlados pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 12h25, o gatilho será executado às 24h25, 1h25, 2h25, … e 23h25.<br /><br />Esse agendamento é equivalente a ter um gatilho com um valor de **frequency** igual a “hour”, um valor de **interval** igual a 1 e nenhum **schedule**. Esse agendamento pode ser usado com valores diferentes de **frequency** e **interval** para criar outros gatilhos. Por exemplo, quando o valor de **frequency** é “month”, o agendamento é executado somente uma vez por mês, em vez de todos os dias, quando o valor de **frequency** é “day”. |
| `{"minutes":[0]}` | Executar a cada hora na hora.<br /><br />Esse gatilho é executado a cada hora na hora, começando às 24h, 1h, 2h e assim por diante.<br /><br />Esse agendamento é equivalente a um gatilho com um valor de **frequency** igual a “hour” e um valor de **startTime** igual a zero minutos e nenhum **schedule**, mas um valor de **frequency** igual a “day”. Se o valor de **Frequency** for "Week" ou "month", o agendamento será executado um dia uma semana ou um dia apenas um mês, respectivamente. |
| `{"minutes":[15]}` | Executa 15 minutos após cada hora.<br /><br />Esse gatilho é executado a cada hora em 15 minutos após a hora, começando às 24h15, 1h15, 2h15 e assim por diante, terminando em 23h15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Executar às 17h aos sábados toda semana. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17h às segundas-feiras, quartas-feiras e sextas-feiras toda semana. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17h15 e às 17h45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar a cada 15 minutos em dias da semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar a cada 15 minutos em dias da semana entre 9h e 16h45. |
| `{"weekDays":["tuesday", "thursday"]}` | Executar às terças-feiras e quintas-feiras na hora de início especificada. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Executar às 6h no vigésimo oitavo dia de cada mês supondo um valor de **frequency** de "month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Executar às 6h no último dia do mês.<br /><br />Para executar um gatilho no último dia de um mês, use -1, em vez do dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Executar às 6h no primeiro e último dia de cada mês. |
| `{monthDays":[1,14]}` | Executar no primeiro e no décimo quarto dia de cada mês na hora de início especificada. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês às 5h. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês na hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Executar na terceira sexta-feira do final do mês, todo mês, na hora de início especificada. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sexta-feira de cada mês às 5h15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sexta-feira de cada mês na hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Executar na quinta sexta-feira de cada mês na hora de início especificada.<br /><br />Quando não houver uma quinta sexta-feira em um mês, o pipeline não será executado. Para executar o gatilho na última sexta-feira do mês, considere o uso de -1, em vez de 5 para o valor de **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Executar a cada 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Executar às 5h15, 5h45, 17h15 e 17h45 na terceira quarta-feira de cada mês. |

## <a name="trigger-type-comparison"></a>Comparação de tipo de gatilho

Tanto o gatilho de janela em cascata quanto o gatilho de agendamento operam sobre pulsações de tempo. Como eles são diferentes?

> [!NOTE]
> A janela em cascata do gatilho de execução *espera que a execução do pipeline* disparado seja concluída. Seu estado de execução reflete o estado da execução do pipeline disparado. Por exemplo, se uma execução de pipeline disparada for cancelada, a execução do gatilho da janela em cascata correspondente será marcada como cancelada. Isso é diferente do comportamento "acionar e esquecer" do gatilho de agendamento, que é marcado com êxito desde que uma execução de pipeline seja iniciada.

A tabela a seguir fornece uma comparação entre o gatilho de janela em cascata e o gatilho de agenda:

| Item | Gatilho de janela em cascata | Gatilho de agendamento |
| --- | --- | --- |
| **Cenários de aterramento** | Com suporte. As execuções de pipeline podem ser agendadas para janelas no passado. | Sem suporte. As execuções de pipeline podem ser executadas somente em períodos de tempo atuais e no futuro. |
| **Confiabilidade** | 100% de confiabilidade. As execuções de pipeline podem ser agendadas para todas as janelas de uma data de início especificada sem folgas. | Menos confiável. |
| **Repetir o recurso** | Com suporte. As execuções de pipeline com falha têm o padrão de política de repetição como 0, ou uma política especificada pelo usuário como parte da definição do gatilho. Repete automaticamente quando o pipeline é executado com falha devido a limites de simultaneidade/servidor/limitação (ou seja, códigos de status 400: erro do usuário, 429: muitas solicitações e 500: erro interno do servidor). | Sem suporte. |
| **Simultaneidade** | Com suporte. Os usuários podem definir explicitamente os limites de simultaneidade para o gatilho. Permite entre 1 e 50 execuções de pipeline disparadas simultaneamente. | Sem suporte. |
| **Variáveis do sistema** | Juntamente com @trigger (). scheduletime e @trigger (). StartTime, ele também dá suporte ao uso das variáveis de sistema **WindowStart** e **WindowEnd** . Os usuários podem acessar `trigger().outputs.windowStartTime` e `trigger().outputs.windowEndTime` como variáveis de sistema de gatilho na definição do gatilho. Os valores são usados como hora de início e hora de término da janela, respectivamente. Por exemplo, para um gatilho de janela em cascata que é executado a cada hora, para a janela de 1h a 2h, a definição é `trigger().outputs.windowStartTime = 2017-09-01T01:00:00Z` e `trigger().outputs.windowEndTime = 2017-09-01T02:00:00Z`. | Dá suporte apenas a @trigger variáveis padrão (). scheduletime e @trigger (). StartTime. |
| **Relação pipeline para gatilho** | Dá suporte a uma relação um para um. Somente um pipeline pode ser disparado. | Dá suporte a relações muitos para muitos. Vários gatilhos podem disparar um único pipeline. Um único gatilho pode disparar vários pipelines. |

## <a name="event-based-trigger"></a>Gatilho baseado em eventos

Um gatilho baseado em evento executa pipelines em resposta a um evento. Há dois tipos de gatilhos baseados em eventos.

* O _gatilho de evento de armazenamento_ executa um pipeline em relação a eventos que ocorrem em uma conta de armazenamento, como a chegada de um arquivo ou a exclusão de um arquivo na conta de armazenamento de BLOBs do Azure.
* Processos de _gatilho de evento personalizado_ e manipula [Tópicos personalizados](../event-grid/custom-topics.md) na grade de eventos

Para obter mais informações sobre gatilhos baseados em evento, consulte [gatilho de evento de armazenamento](how-to-create-event-trigger.md) e gatilho de [evento personalizado](how-to-create-custom-event-trigger.md).

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes tutoriais:

- [Início Rápido: criar um data factory usando o SDK do .NET](quickstart-create-data-factory-dot-net.md)
- [Criar um gatilho de agenda](how-to-create-schedule-trigger.md)
- [Criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md)
