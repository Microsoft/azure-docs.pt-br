---
title: "Gatilhos e execução de pipeline no Azure Data Factory | Microsoft Docs"
description: "Este artigo fornece informações sobre como executar um pipeline no Azure Data Factory sob demanda ou criando um gatilho."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: c319979cce23da69965d4fbab037919461f67b3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Gatilhos e execução de pipeline no Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-scheduling-and-execution.md)
> * [Versão 2 – Versão prévia](concepts-pipeline-execution-triggers.md)

Uma **execução de pipeline** é um termo no Azure Data Factory Versão 2 que define uma instância de uma execução de pipeline. Por exemplo, digamos que você tem um pipeline que é executado às 8h, 9h e 10h. Haveria três execuções de pipeline separadas nesse caso. Cada execução de pipeline tem uma ID de execução de pipeline exclusiva, que é um GUID que define exclusivamente essa execução do pipeline específica. As execuções de pipeline normalmente são instanciadas por meio da passagem de argumentos para parâmetros definidos nos pipelines. Há duas maneiras de executar um pipeline: **manualmente** ou por meio de um **gatilho**. Este artigo fornece detalhes sobre as duas as maneiras de executar um pipeline. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponível ao público em geral), consulte [agendamento e execução no Data Factory V1](v1/data-factory-scheduling-and-execution.md).

## <a name="run-pipeline-on-demand"></a>Executar o pipeline sob demanda
Nesse método, você executa o pipeline manualmente. Ele também é considerado como uma execução de um pipeline sob demanda. 

Por exemplo, digamos que você tem um pipeline denominado **copyPipeline** que você deseja executar. Trata-se de um pipeline simples com uma única atividade que copia de uma pasta de origem no Armazenamento de Blobs do Azure para uma pasta de destino no mesmo armazenamento. Aqui está a definição do pipeline de exemplo: 

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
O pipeline usa dois parâmetros: sourceBlobContainer e sinkBlobContainer, conforme mostrado na definição JSON. Você passa valores para esses parâmetros em tempo de execução. 

Para executar o pipeline manualmente, você pode usar uma das seguintes maneiras: .NET, REST, PowerShell e Python. 

### <a name="rest-api"></a>API REST
Aqui está uma amostra de comando REST:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
Consulte [Guia de início rápido: criar um data factory usando a API REST](quickstart-create-data-factory-rest-api.md) para obter uma amostra completa.

### <a name="powershell"></a>PowerShell
Aqui está uma amostra de comando do PowerShell: 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Você passa parâmetros no corpo no conteúdo da solicitação. No .NET, Powershell e Python, você passa valores em um dicionário passado como um argumento para a chamada.

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

O conteúdo da resposta é uma ID exclusiva do pipeline de execução:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```


Consulte [Guia de início rápido: criar um data factory usando o PowerShell](quickstart-create-data-factory-powershell.md) para obter uma amostra completa.

### <a name="net"></a>.NET 
Aqui está uma amostra de chamada .NET: 

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Consulte [Guia de início rápido: criar um data factory usando o .NET](quickstart-create-data-factory-dot-net.md) para obter uma amostra completa.

> [!NOTE]
> Você pode usar a API .NET para invocar os pipelines do Data Factory do Azure Functions, seus próprios serviços Web, etc.

## <a name="triggers"></a>Gatilhos
Gatilhos fornecem a segunda maneira de realizar uma execução de pipeline. Os gatilhos representam uma unidade de processamento que determina quando uma execução de pipeline precisa ser inicializada. Atualmente, o Data Factory dá suporte a um gatilho que invoca um pipeline em um agendamento de relógio. Ele é chamado de **Gatilho do Agendador**. Atualmente, o Data Factory não dá suporte a gatilhos baseados em evento como um gatilho de uma execução de pipeline no evento de uma entrada de arquivo.

Pipelines e gatilhos têm uma relação "n-m". Vários gatilhos podem disparar um único pipeline e o mesmo gatilho pode disparar vários pipelines. Na seguinte definição JSON de um gatilho, a propriedade **pipelines** se refere a uma lista dos pipelines que são disparados pelo gatilho específico, bem como valores para parâmetros de pipeline.

### <a name="basic-trigger-definition"></a>Definição básica do gatilho: 
```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
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
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="scheduler-trigger"></a>Gatilho de agendador
O gatilho de agendador executa pipelines em um agendamento de relógio. Esse gatilho dá suporte a opções de calendário periódicas e avançadas (semanalmente, segunda-feira às 17h e quinta-feira às 21h). Ele é flexível, sendo independente de padrão de conjunto de dados, sem distinção entre dados de série temporal e dados de outros tipos.

### <a name="scheduler-trigger-json-definition"></a>Definição JSON de gatilho de agendador
Quando você cria um gatilho de agendador, você pode especificar o agendamento e a recorrência usando JSON, conforme mostrado no exemplo nesta seção. 

Para que o gatilho de agendador dispare uma execução de pipeline, inclua uma referência de pipeline do pipeline específico na definição do gatilho. Pipelines e gatilhos têm uma relação "n-m". Vários gatilhos podem disparar um único pipeline. O mesmo gatilho pode disparar vários pipelines.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": <<default UTC>>
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
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
                    "<parameter 2 Name> : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

### <a name="overview-scheduler-trigger-schema"></a>Visão geral: esquema de gatilho de agendador
A tabela a seguir fornece uma visão geral de alto nível dos principais elementos relacionados a recorrência e planejamento em um gatilho:

Propriedade JSON |     Descrição
------------- | -------------
startTime | startTime é uma Data/Hora. Para agendamentos simples, startTime é a primeira ocorrência. Para agendamentos complexos, o gatilho não inicia antes de startTime.
recurrence | O objeto recurrence especifica regras de recorrência para o gatilho. O objeto recurrence dá suporte aos elementos frequency, interval, endTime, count e schedule. Se recurrence for definido, frequency será necessário; os outros elementos de recurrence serão opcionais.
frequência | Representa a unidade de frequência com que o gatilho se repete. Os valores com suporte são: `minute`, `hour`, `day`, `week` ou `month`.
intervalo | O valor interval é um inteiro positivo. Ele denota o intervalo para a frequência que determina a periodicidade com que o gatilho é executado. Por exemplo, se interval for 3 e frequency for "week", o gatilho se repetirá a cada três semanas.
endTime | Especifica a data/hora de término para o gatilho. O gatilho não é executado após esse horário. Não é válido ter um endTime no passado.
schedule | Um gatilho com uma frequência especificada altera sua recorrência com base em um agendamento de recorrência. Um elemento schedule contém as modificações com base em minutos, em horas, em dias da semana, em dias do mês e em número da semana.

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>Visão geral: padrões de esquema de gatilho de agendador, limites e exemplos

Nome JSON | Tipo de valor | Obrigatório? | Valor padrão | Valores válidos | Exemplo
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Cadeia de caracteres | Sim | Nenhum | Data e hora ISO 8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Objeto | Sim | Nenhum | Objeto de recorrência | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
intervalo | Número | Não | 1 | 1 a 1000. | ```"interval":10```
endTime | Cadeia de caracteres | Sim | Nenhum | Valor de data e hora que representa um momento no futuro | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Objeto | Não | Nenhum | Objeto Agendamento | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>Análise aprofundada: startTime
A tabela a seguir mostra como startTime controla a execução de um gatilho:

Valor startTime | Recorrência sem agendamento | Recorrência com agendamento
--------------- | --------------------------- | ------------------------
Hora de início no passado | Calcula a primeira hora de execução futura após a hora de início e executa nessa hora.<p>Realiza as execuções subsequentes com base no cálculo do tempo da última execução.</p><p>Consulte o exemplo após esta tabela.</p> | O gatilho é iniciado _não antes da_ hora de início especificada. A primeira ocorrência baseia-se no elemento schedule calculado com base na hora de início. <p>Fazer as execuções subsequentes com base no agendamento de recorrência</p>
Hora de início no futuro ou no momento | Executa uma vez na hora de início especificada. <p>Realize as execuções subsequentes com base no cálculo do tempo da última execução.</p> | O gatilho é iniciado _não antes da_ hora de início especificada. A primeira ocorrência baseia-se no elemento schedule calculado com base na hora de início.<p>Realize as execuções subsequentes com base no agendamento de recorrência.</p>

Vejamos um exemplo do que acontece quando startTime está no passado, com recurrence, mas sem elemento schedule. Suponha que a hora atual seja `2017-04-08 13:00`, que startTime seja `2017-04-07 14:00` e que recurrence seja a cada dois dias (definida com frequency: day e interval: 2.) Observe que startTime está no passado e ocorre antes da hora atual.

Sob essas condições, a primeira execução será em `2017-04-09 at 14:00`. O mecanismo do Agendador calcula as ocorrências de execução desde a hora de início. As instâncias no passado serão descartadas. O mecanismo usa a próxima instância que ocorrer no futuro. Nesse caso, startTime é `2017-04-07 at 2:00pm`, portanto, a próxima instância ocorrerá dois dias depois desse momento, o que será em `2017-04-09 at 2:00pm`.

A primeira hora de execução é igual, mesmo que startTime seja `2017-04-05 14:00` ou `2017-04-01 14:00`. Após a primeira execução, as execuções subsequentes são calculadas usando o schedule. Portanto, elas são em `2017-04-11 at 2:00pm`, depois em `2017-04-13 at 2:00pm`, depois em `2017-04-15 at 2:00pm`, etc.

Finalmente, quando um gatilho tiver um agendamento, se as horas e/ou minutos não estiverem definidos no agendamento, serão adotadas as horas e/ou minutos da primeira execução, respectivamente.

### <a name="deep-dive-schedule"></a>Análise aprofundada: schedule
Por um lado, um elemento schedule pode limitar o número de execuções do gatilho. Por exemplo, se um gatilho com o elemento frequency definido como "month" tiver um schedule para ser executado somente no dia 31, o gatilho será executado apenas nos meses que têm o dia 31.

Por outro lado, um elemento schedule também pode expandir o número de execuções do gatilho. Por exemplo, se um gatilho com o elemento frequency definido como "month" tiver um agendamento para ser executado nos dias 1 e 2 do mês, o gatilho será executado no 1º e 2º dias do mês, em vez de apenas uma vez por mês.

Se forem especificados vários elementos schedule, a ordem de avaliação será do maior para o menor – número da semana, dia do mês, dia da semana, hora e minuto.

A tabela a seguir descreve elementos schedule em detalhes:


Nome JSON | Descrição | Valores Válidos
--------- | ----------- | ------------
minutes | Minutos da hora em que o gatilho será executado. | <ul><li>Número inteiro</li><li>Matriz de inteiros</li></ul>
hours | As horas do dia em que o gatilho será executado. | <ul><li>Número inteiro</li><li>Matriz de inteiros</li></ul>
weekDays | Dias da semana em que o gatilho será executado. Só pode ser especificado com uma frequência semanal. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday ou Sunday</li><li>Matriz de qualquer um dos valores acima (tamanho máximo da matriz: 7)</li></p>Não diferencia maiúsculas de minúsculas</p>
monthlyOccurrences | Determina em quais dias do mês o gatilho será executado. Só pode ser especificado com uma frequência mensal. | Matriz de objetos monthlyOccurence: `{ "day": day,  "occurrence": occurence }`. <p> O valor do elemento day é o dia da semana no qual o gatilho será executado, por exemplo, `{Sunday}` representa todos os domingos do mês. Obrigatório.<p>O valor do elemento ocurrence é a ocorrência do dia em questão no mês, por exemplo, `{Sunday, -1}` é o último domingo do mês. Opcional.
monthDays | Dia do mês em que o gatilho será executado. Só pode ser especificado com uma frequência mensal. | <ul><li>Qualquer valor <= -1 e >= -31</li><li>Qualquer valor >= 1 e <= 31</li><li>Uma matriz dos valores acima</li>


## <a name="examples-recurrence-schedules"></a>Exemplos: agendamentos de recorrência
Esta seção fornece exemplos de agendamentos de recorrência focados no objeto schedule e seus subelementos.

Os agendamentos de exemplo pressupõem que o elemento interval esteja definido como 1. Além disso, pressuponha a frequência correta de acordo com o valor do elemento schedule (por exemplo, não é possível usar um valor "day" para o elemento frequency e realizar uma modificação a "monthDays" no elemento schedule). Essas restrições são mencionadas na tabela na seção anterior. 

Exemplo | Descrição
------- | -----------
`{"hours":[5]}` | Executa às 5h todos os dias
`{"minutes":[15], "hours":[5]}` | Executa às 5:15 todos os dias
`{"minutes":[15], "hours":[5,17]}` | Executa às 5:15 e 17:15 todos os dias
`{"minutes":[15,45], "hours":[5,17]}` | Executa às 5h15, 5h45, 17h15 e 17h45 todos os dias
`{"minutes":[0,15,30,45]}` | Executa a cada 15 minutos
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Executa a cada hora. Este gatilho é executado a cada hora. O minuto será controlado por startTime se ele for especificado ou, se nenhum for especificado, será controlado pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 12h25, o gatilho será executado às 0:25, 1:25, 2:25, …, 23:25. O schedule em questão é equivalente a ter um gatilho com frequency de "hour", interval de 1 e sem schedule. A diferença é que esse schedule também pode ser usado com valores diferentes de frequency e de interval para criar outros gatilhos. Por exemplo, se frequency fosse "month", o schedule seria executado somente uma vez por mês, em vez de todos os dias como ocorreria se frequency fosse "day".
`{"minutes":[0]}` | Executa a cada hora em hora exata. Esse gatilho também é executado a cada hora, mas na hora exata (por exemplo, 0h, 1h, 2h, etc.). Essa configuração equivale a um gatilho com frequency igual a "hour", startTime com zero minutos e sem schedule se frequency é "day", mas, sendo frequency igual a "week" ou "month", o schedule deve executar apenas uma vez por semana ou por mês, respectivamente.
`{"minutes":[15]}` | Executa 15 minutos após cada hora exata. É executado a cada hora, começando em 00:15, 1:15, 2:15, etc. e terminando às 22:15 e 23:15.
`{"hours":[17], "weekDays":["saturday"]}` | Executa às 17h aos sábados toda semana
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executa às 17h às segundas-feiras, quartas-feiras e sextas-feiras toda semana
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executa às 17h15 e 17h45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar a cada 15 minutos em dias da semana
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executa a cada 15 minutos nos dias úteis entre 9h e 16h45
`{"weekDays":["tuesday", "thursday"]}` | Executa às terças e quintas-feiras na hora de início especificada.
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | Executa às 6h no 28º dia de cada mês (supondo que o valor de frequency seja month)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Executa às 6h no último dia do mês. Se você quiser executar um gatilho no último dia de um mês, use -1 em vez de dia 28, 29, 30 ou 31.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Executa às 6h no primeiro e no último dias de cada mês
`{monthDays":[1,14]}` | Executa no primeiro e no décimo quarto dia de cada mês na hora de início especificada.
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executa na primeira sexta-feira de cada mês às 5h
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executa na primeira sexta-feira de cada mês na hora de início especificada.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Executa na terceira sexta-feira desde o final do mês, todo mês, na hora de início
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executa na primeira e na última sextas-feiras de cada mês às 5h15
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executa na primeira e na última sextas-feiras de cada mês na hora de início especificada
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Executa na quinta sexta-feira de cada mês na hora de início. Se não houver nenhuma quinta sexta-feira em um determinado mês, o pipeline não será executado, uma vez que está agendado para executar apenas nas quintas sextas-feiras.  Se você quiser executar o gatilho na última sexta-feira do mês, considere o uso de -1 em vez de 5 para a ocorrência.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Executa a cada 15 minutos na última sexta-feira do mês.
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Executa às 5h15, 5h45, 17h15 e 17h45 na terceira quarta-feira de cada mês.




## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes tutoriais: 

- [Início rápido: criar um data factory usando o .NET](quickstart-create-data-factory-dot-net.md)
