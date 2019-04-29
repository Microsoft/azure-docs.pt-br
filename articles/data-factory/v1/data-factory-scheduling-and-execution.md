---
title: Agendamento e execução com o Data Factory | Microsoft Docs
description: Aprenda sobre os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 2d7fc45faf1fb77c7d9181e5a2419096dd1ad0f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258875"
---
# <a name="data-factory-scheduling-and-execution"></a>Agendamento e execução com o Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o artigo [execução de pipeline e gatilhos](../concepts-pipeline-execution-triggers.md).

Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. Este artigo presume que você compreenda as noções básicas sobre conceitos de modelo de aplicativo do data factory, incluindo atividade, pipelines, serviços vinculados e conjuntos de dados. Para obter conceitos básicos do Azure Data Factory, consulte os seguintes artigos:

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Conjunto de dados](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Horas de início e término do pipeline
Um pipeline está ativo somente entre suas horas de **início** e **término**. Ele não é executado antes da hora de início ou após a hora de término. Se o pipeline estiver em pausa, ele não será executado, independentemente de suas horas de início e término. Para um pipeline ser executado, ele não deve estar pausado. É possível encontrar essas configurações (início, término, em pausa) na definição do pipeline: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Para obter mais informações sobre essas propriedades, consulte o artigo [Criar pipelines](data-factory-create-pipelines.md). 


## <a name="specify-schedule-for-an-activity"></a>Especificar o agendamento de uma atividade
Não é o pipeline que é executado. São as atividades no pipeline que são executadas no contexto geral do pipeline. É possível especificar um agendamento recorrente para uma atividade usando a seção **agendador** do JSON da atividade. Por exemplo, você pode agendar uma atividade para ser executada a cada hora, da seguinte maneira:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Conforme mostrado no diagrama a seguir, especificar um agendamento para uma atividade cria uma série de janelas em cascata nas horas de início e término do pipeline. Janelas em cascata são uma série de intervalos de tempo de tamanho fixo, não sobrepostos e contínuos. Essas janelas lógicas em cascata de uma atividade são chamadas de **janelas de atividades**.

![Exemplo de agendador de atividades](media/data-factory-scheduling-and-execution/scheduler-example.png)

A propriedade **agendador** de uma atividade é opcional. Se você especificar essa propriedade, ela deverá corresponder à cadência especificada na definição do conjunto de dados de saída da atividade. Atualmente, o conjunto de dados de saída é o que conduz o agendamento. Portanto, é necessário criar um conjunto de dados de saída mesmo que a atividade não produza nenhuma saída. 

## <a name="specify-schedule-for-a-dataset"></a>Especificar o agendamento de um conjunto de dados
Uma atividade em um pipeline do Data Factory pode usar zero ou mais **conjuntos de dados** de entrada e gerar um ou mais conjuntos de dados de saída. Para uma atividade, você pode especificar a cadência na qual os dados de entrada estão disponíveis ou os dados de saída são gerados usando a seção **disponibilidade** nas definições do conjunto de dados. 

**Frequência** na seção **disponibilidade** especifica a unidade de tempo. Os valores permitidos para a frequência são: Minuto, Hora, Dia, Semana e Mês. A propriedade **intervalo** na seção de disponibilidade especifica um multiplicador para a frequência. Por exemplo: se a frequência for definida como Dia e o intervalo for definido como 1 para um conjunto de dados de saída, os dados de saída serão gerados diariamente. Caso você especifique a frequência como minuto, recomendamos que defina o intervalo como não inferior a 15. 

No exemplo a seguir, os dados de entrada estão disponíveis a cada hora e os dados de saída são gerados a cada hora (`"frequency": "Hour", "interval": 1`). 

**Conjunto de dados de entrada:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Conjunto de dados de saída**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Atualmente, **o conjunto de dados de saída conduz o agendamento**. Em outras palavras, o agendamento especificado para o conjunto de dados de saída é usado para executar uma atividade em tempo de execução. Portanto, é necessário criar um conjunto de dados de saída mesmo que a atividade não produza nenhuma saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada. 

Na definição de pipeline a seguir, a propriedade **agendador** é usada para especificar um agendamento para a atividade. Essa propriedade é opcional. Atualmente, o agendamento da atividade deve corresponder ao agendamento especificado para o conjunto de dados de saída.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

Neste exemplo, a atividade é executada a cada hora entre as horas de início e término do pipeline. Os dados de saída são gerados a cada hora para janelas de três horas (8h às 9h, 9h às 10h e 10h às 11h). 

Cada unidade de dados consumida ou gerada por uma execução de atividade é chamada de uma **fatia de dados**. O seguinte diagrama mostra um exemplo de uma atividade com um conjunto de dados de entrada e um conjunto de dados de saída: 

![Agendador de disponibilidade](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

O diagrama mostra as fatias de dados por hora para o conjunto de dados de entrada e saída. O diagrama mostra três fatias de entrada que estão prontas para processamento. A atividade de 10-11h está em andamento, produzindo a fatia de saída de 10-11h. 

É possível acessar o intervalo de tempo associado à fatia atual no conjunto de dados JSON usando estas variáveis: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Da mesma forma, é possível acessar o intervalo de tempo associado a uma janela de atividades usando WindowStart e WindowEnd. O agendamento de uma atividade deve corresponder ao agendamento do conjunto de dados de saída da atividade. Portanto, os valores de SliceStart e SliceEnd são iguais aos valores de WindowStart e WindowEnd, respectivamente. Para obter mais informações sobre essas variáveis, consulte os artigos [Funções e variáveis de sistema do Data Factory](data-factory-functions-variables.md#data-factory-system-variables).  

Você pode usar essas variáveis para finalidades diferentes em sua atividade JSON. Por exemplo, você pode usá-las para selecionar dados em conjuntos de dados de entrada e de saída que representam dados de série temporal (por exemplo: das 8h às 9h). Este exemplo também usa **WindowStart** e **WindowEnd** para selecionar dados relevantes para uma execução de atividade e copia-os para um blob com o **folderPath** apropriado. O **folderPath** é parametrizado para ter uma pasta separada para cada hora.  

No exemplo anterior, o agendamento especificado para conjuntos de dados de entrada e saída é o mesmo (por hora). Se o conjunto de dados de entrada da atividade estiver disponível em uma frequência diferente, digamos, a cada 15 minutos, a atividade que produz esse conjunto de dados de saída ainda será executada uma vez por hora, pois o conjunto de dados de saída é o que conduz o agendamento da atividade. Para obter mais informações, consulte [Modelar conjuntos de dados com frequências diferentes](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Políticas e disponibilidade do conjunto de dados
Você já viu o uso da frequência e das propriedades de intervalo na seção de disponibilidade da definição do conjunto de dados. Há algumas outras propriedades que afetam o agendamento e a execução de uma atividade. 

### <a name="dataset-availability"></a>Disponibilidade do conjunto de dados 
A tabela a seguir descreve as propriedades que você pode usar na seção de **availability**:

| Propriedade | DESCRIÇÃO | Obrigatório | Padrão |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para a produção da fatia de conjunto de dados.<br/><br/><b>Frequência compatível</b>: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| intervalo |Especifica um multiplicador para frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se você precisa que o conjunto de dados seja dividido por hora, defina <b>Frequência</b> como <b>Hora</b> e <b>intervalo</b> como <b>1</b>.<br/><br/><b>Observação</b>: Caso você especifique a frequência como Minuto, recomendamos que defina o intervalo não sendo inferior a 15 |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início/término do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência for definida como Mês e o estilo como EndOfInterval, a fatia será produzida no último dia do mês. Se o estilo for definido como StartOfInterval, a fatia será produzida no primeiro dia do mês.<br/><br/>Se a frequência for definida como Dia e o estilo como EndOfInterval, a fatia será produzida na última hora do dia.<br/><br/>Se a Frequência for definida como Hora e o estilo como EndOfInterval, a fatia será produzida ao final da hora. Por exemplo, para uma fatia de período 13h – 14h, a fatia é produzida às 14h. |Não  |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo usada pelo agendador para computar limites de fatia do conjunto de dados. <br/><br/><b>Observação</b>: Se AnchorDateTime tiver partes de datas mais granulares do que a frequência, as partes mais granulares serão ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> for <b>por hora</b> (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contiver <b>minutos e segundos</b>, as partes <b>minutos e segundos</b> do AnchorDateTime serão ignoradas. |Não  |01/01/0001 |
| deslocamento |O período de tempo no qual o início e o término de todas as fatias de conjunto de dados são deslocados. <br/><br/><b>Observação</b>: Se anchorDateTime e o deslocamento forem especificados, o resultado será um deslocamento combinado. |Não  |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Por padrão, fatias (`"frequency": "Day", "interval": 1`) diárias começam em hora UTC de 12: 00 (meia-noite). Se desejar que a hora de início para hora UTC de 6 horas em vez disso, define o deslocamento, conforme mostrado no snippet a seguir: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Exemplo de anchorDateTime
No exemplo a seguir, o conjunto de dados é gerado uma vez a cada 23 horas. Primeira fatia começa no momento especificado pelo anchorDateTime, que é definido como `2017-04-19T08:00:00` (hora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Exemplo de deslocamento/estilo
O conjunto de dados a seguir é um conjunto de dados mensal e é produzido no dia 3 de cada mês, às 8:00 h (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Política do conjunto de dados
Um conjunto de dados pode ter uma política de validação definida que especifica como os dados gerados pela execução de uma fatia podem ser validados antes de estarem prontos para consumo. Nesses casos, quando a execução da fatia tiver terminado, o status da fatia de saída será alterado para **Aguardando** com um substatus de **Validação**. Depois que as fatias são validadas, o status de fatia é alterado para **Pronto**. Se uma fatia de dados foi produzida mas não passou na validação, as execuções de atividade para fatias downstream que dependem dessa fatia não são processadas. [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md) .

A seção **política** na definição do conjunto de dados define os critérios ou a condição que as divisões de conjunto de dados devem atender. A seguinte tabela descreve as propriedades que você pode usar na seção **política**:

| Nome da política | DESCRIÇÃO | Aplicado a | Obrigatório | Padrão |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Valida que os dados em um **blob do Azure** atendem aos requisitos de tamanho mínimo (em megabytes). |blob do Azure |Não  |ND |
| minimumRows | Valida que os dados em um **Banco de Dados SQL do Azure** ou uma **tabela do Azure** contêm o número mínimo de linhas. |<ul><li>Banco de Dados SQL do Azure</li><li>tabela do Azure</li></ul> |Não  |ND |

#### <a name="examples"></a>Exemplos
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Para obter mais informações sobre essas propriedades e exemplos, consulte o artigo [Criar conjuntos de dados](data-factory-create-datasets.md). 

## <a name="activity-policies"></a>Políticas de atividades
As políticas afetam o comportamento de tempo de execução de uma atividade, especialmente quando a divisão de uma tabela é processada. A tabela a seguir fornece os detalhes.

| Propriedade | Valores permitidos | Valor Padrão | DESCRIÇÃO |
| --- | --- | --- | --- |
| simultaneidade |Número inteiro <br/><br/>Valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções de atividade paralela que podem ocorrer em divisões diferentes. Por exemplo, se uma atividade precisa passar por um grande conjunto de dados disponíveis, ter um valor de concorrência maior acelera o processamento de dados. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina a ordem das divisões de dados que estão sendo processadas.<br/><br/>Por exemplo, se houver duas fatias (uma ocorre às 16h e a outra às 17h),e ambas estiverem com a execução pendente. Se você definir executionPriorityOrder como NewestFirst, a divisão às 17h será processada primeiro. De modo semelhante, se você definir executionPriorityORder como OldestFIrst, a fatia às 16h será processada. |
| tentar novamente |Número inteiro<br/><br/>O valor máximo pode ser 10 |0 |Número de novas tentativas antes do processamento de dados da divisão ser marcado como Com falha. A execução da atividade para uma divisão de dados é repetida até a contagem de repetição especificada. A nova tentativa é feita logo após a falha. |
| Tempo limite |TimeSpan |00:00:00 |Tempo limite para a atividade. Exemplo: 00:10:00 (pressupõe um tempo de limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite será infinito.<br/><br/>Se o tempo de processamento de dados em uma divisão exceder o valor de tempo limite, ele será cancelado e o sistema tentará repetir o processamento. O número de repetições depende da propriedade de repetição. Quando atingir o tempo limite, o status será TimedOut. |
| atrasar |TimeSpan |00:00:00 |Especifique o atraso antes do processamento de dados da divisão começar.<br/><br/>A execução da atividade de uma fatia de dados será iniciada após o atraso passar do tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (pressupõe um atraso de 10 minutos) |
| longRetry |Número inteiro<br/><br/>Valor máximo: 10 |1 |O número de tentativas repetidas longas antes que a execução da divisão falhe.<br/><br/>Tentativas de longRetry são espaçadas por longRetryInterval. Portanto, se você precisar especificar um tempo entre tentativas de repetição, use longRetry. Se Retry e longRetry forem especificados, cada tentativa de longRetry incluirá tentativas de Retry, e o número máximo de tentativas será Retry * longRetry.<br/><br/>Por exemplo, se tivermos as seguintes configurações na política de atividade:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Presumindo que haja apenas uma fatia para execução (o status é Aguardando) e a execução da atividade sempre falhe. Inicialmente haveria três tentativas consecutivas de execução. Após cada tentativa, o status de divisão seria Retry. Depois das três primeiras tentativas, o status da divisão seria LongRetry.<br/><br/>Depois de uma hora (ou seja, valor de longRetryInteval), deve haver outro conjunto de três tentativas consecutivas de execução. Depois disso, o status da divisão seria Com falha e não haveria nova tentativa. Portanto, em geral, foram feitas seis tentativas.<br/><br/>Se qualquer execução for bem-sucedida, o status da fatia seria Ready e não haverá mais nenhuma tentativa.<br/><br/>longRetry pode ser usado em situações em que dados dependentes chegam em horários não determinísticos ou o ambiente geral está instável onde o processamento de dados ocorre. Nesses casos, fazer novas tentativas uma após a outra pode não ajudar e fazer isso após um intervalo de tempo resulta na saída desejada.<br/><br/>Advertência: não defina valores altos para longRetry ou longRetryInterval. Normalmente, os valores mais altos implicam outros problemas sistêmicos. |
| longRetryInterval |TimeSpan |00:00:00 |O intervalo entre tentativas de repetição longa |

Para obter mais informações, consulte o artigo [Pipelines](data-factory-create-pipelines.md). 

## <a name="parallel-processing-of-data-slices"></a>Processamento paralelo de fatias de dados
Você pode definir a data de início do pipeline no passado. Quando você faz isso, o Data Factory calcula automaticamente (faz preenchimento de fundo) todas as fatias de dados no passado e começa a processá-las. Por exemplo: se você criar um pipeline com a data de início 01/04/2017 e a data atual for 10/04/2017. Se a cadência do conjunto de dados de saída for diária, o Data Factory começará a processar todas as fatias de 01/04/2017 a 09/04/2017 imediatamente porque a data de início está no passado. A fatia de 10/04/2017 ainda não será processada porque o valor da propriedade de estilo na seção de disponibilidade é EndOfInterval por padrão. A fatia mais antiga é processada primeiro, pois o valor padrão de executionPriorityOrder é OldestFirst. Para obter uma descrição da propriedade de estilo, consulte a seção [disponibilidade do conjunto de dados](#dataset-availability). Para obter uma descrição da seção executionPriorityOrder, consulte a seção [políticas de atividades](#activity-policies). 

Você pode configurar as fatias de dados com preenchimento de fundo para serem processadas em paralelo definindo a propriedade **simultaneidade** na seção **política** do JSON da atividade. Essa propriedade determina o número de execuções de atividade paralela que podem ocorrer em fatias diferentes. O valor padrão da propriedade de simultaneidade é 1. Portanto, uma única fatia é processada por vez, por padrão. O valor máximo é 10. Quando um pipeline precisa passar por um grande conjunto de dados disponíveis, ter um valor de simultaneidade maior acelera o processamento dos dados. 

## <a name="rerun-a-failed-data-slice"></a>Executar novamente uma fatia de dados com falha
Quando ocorre um erro durante o processamento de uma fatia de dados, você pode descobrir por que o processamento de uma fatia falhou usando folhas do portal do Azure ou o Aplicativo Monitorar e Gerenciar. Confira [Monitorar e gerenciar pipelines usando folhas do portal do Azure](data-factory-monitor-manage-pipelines.md) ou [aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md) para obter detalhes.

Considere o exemplo a seguir, que mostra duas atividades. Activity1 e Activity2. A Activity1 consome uma fatia do Dataset1 e produz uma fatia do Dataset2, que é consumida como entrada pela Activity2 para gerar uma fatia do Conjunto de Dados Final.

![Fatia com falha](./media/data-factory-scheduling-and-execution/failed-slice.png)

O diagrama mostra que, de três fatias recentes, houve uma falha ao produzir a fatia de 9-10h para Dataset2. O Data Factory controla automaticamente a dependência para o conjunto de dados de série temporal. Como resultado, ele não inicia a execução da atividade para a fatia downstream de 9-10h.

Ferramentas de gerenciamento e monitoramento do data factory permitem analisar os logs de diagnóstico da fatia com falha, para que você localize facilmente a causa raiz do problema e corrija-o. Depois de corrigir o problema, você pode iniciar facilmente a execução da atividade para produzir a fatia com falha. Para obter mais informações sobre como executar novamente e entender as transições de estado de fatias de dados, consulte [Monitorando e gerenciando pipelines usando folhas do portal do Azure](data-factory-monitor-manage-pipelines.md) ou [Aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md).

Após você executar novamente a fatia de 9-10h para o **Dataset2**, o Data Factory iniciará a execução da fatia dependente de 9-10h no conjunto de dados final.

![Executar novamente uma fatia com falha](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Várias atividades em um pipeline
Você pode ter mais de uma atividade em um pipeline. Caso você tenha várias atividades em um pipeline e a saída de uma atividade não seja uma entrada de outra atividade, as atividades poderão ser executadas em paralelo se as fatias de dados de entrada das atividades estiverem prontas.

É possível encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. As atividades podem estar no mesmo pipeline ou em pipelines diferentes. A segunda atividade é executada apenas quando a primeira é concluída com êxito.

Por exemplo, considere o seguinte caso em que um pipeline tem duas atividades:

1. A Atividade A1, que exige o conjunto de dados de entrada externo D1 e produz o conjunto de dados de saída D2.
2. A Atividade A2, que exige a entrada do conjunto de dados D2 e produz o conjunto de dados de saída D3.

Nesse cenário, as atividades A1 e A2 estão no mesmo pipeline. A atividade A1 é executada quando os dados externos estão disponíveis e a frequência de disponibilidade agendada é alcançada. A atividade A2 é executada quando as fatias agendadas de D2 ficam disponíveis e a frequência de disponibilidade agendada é alcançada. Se houver um erro em uma das fatias no conjunto de dados D2, A2 não será executada para essa fatia até que fique disponível.

O modo de exibição de Diagrama com ambas as atividades no mesmo pipeline seria semelhante ao seguinte diagrama:

![Encadeando atividades no mesmo pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Conforme mencionado anteriormente, as atividades poderiam estar em pipelines diferentes. Nesse cenário, a exibição de diagrama seria parecida com o seguinte diagrama:

![Encadeando atividades em dois pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Confira a seção Copiar sequencialmente no apêndice para obter um exemplo.

## <a name="model-datasets-with-different-frequencies"></a>Modelar conjuntos de dados com frequências diferentes
Nos exemplos, as frequências de conjuntos de dados de entrada e saída e a janela de agendamento de atividade foram iguais. Alguns cenários exigem a capacidade de produzir saída em uma frequência diferente de frequências de uma ou mais entradas. O Data Factory dá suporte à modelagem desses cenários.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exemplo 1: gerar um relatório diário de saída para dados de entrada que estão disponíveis de hora em hora
Considere um cenário em que temos dados de medição de entrada de sensores disponíveis a cada hora no armazenamento de Blobs do Azure. Você deseja produzir um relatório diário de agregação com estatísticas, como média, máximo e mínimo para o dia com a [Atividade hive do Data Factory](data-factory-hive-activity.md).

Veja como você pode modelar esse cenário com o Data Factory:

**Conjunto de dados de entrada**

Os arquivos de entrada por hora são instalados na pasta para o dia determinado. A disponibilidade de entrada é definida por **Hora** (frequência: Hora, intervalo: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Conjunto de dados de saída**

Um arquivo de saída é criado diariamente na pasta do dia. A disponibilidade de saída é definida em **Dia** (frequência: Dia e intervalo: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Atividade: atividade de hive em um pipeline**

O script do hive recebe as informações de *DateTime* apropriadas como parâmetros que usam a variável **WindowStart** conforme mostrado no snippet de código a seguir. O script do hive usa essa variável para carregar os dados da pasta correta para o dia e executar a agregação para gerar a saída.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

O diagrama a seguir mostra o cenário de um ponto de vista de dependência de dados.

![Dependência de dados](./media/data-factory-scheduling-and-execution/data-dependency.png)

A fatia de saída para cada dia depende de 24 fatias horárias do conjunto de dados de entrada. O Data Factory calcula essas dependências automaticamente descobrindo as fatias de dados de entrada que equivalem ao mesmo período de tempo utilizado para a produção da fatia de saída. Se qualquer uma das 24 fatias de entrada não estiver disponível, o Data Factory aguardará até que a fatia de entrada esteja pronta antes de iniciar a execução da atividade diária.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exemplo 2: especificar a dependência com expressões e funções do Data Factory
Vamos considerar outro cenário. Suponha que você tenha uma atividade de hive que processa dois conjuntos de dados de entrada. Um deles tem novos dados diariamente, mas o outro obtém novos dados toda semana. Suponha que você queira fazer uma associação entre as duas entradas e produzir uma saída diariamente.

A abordagem simples, na qual o Data Factory detecta automaticamente as fatias de entrada certas a serem processadas alinhando-se ao período de tempo da fatia de dados de saída, não funciona.

Você precisa especificar isso para cada execução de atividade, o Data Factory deve usar a fatia de dados da semana passada para o conjunto de dados de entrada semanal. Use as funções do Azure Data Factory conforme mostrado no snippet a seguir para implementar esse comportamento.

**Entrada1: Blob do Azure**

A primeira entrada é blob do Azure que está sendo atualizado diariamente.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Entrada2: Blob do Azure**

Entrada2 é o blob do Azure que está sendo atualizado semanalmente.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Saída: Blob do Azure**

Um arquivo de saída é criado diariamente na pasta para o dia. A disponibilidade de saída é definida por **Dia** (frequência: Dia, intervalo: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Atividade: atividade de hive em um pipeline**

A atividade de hive usa as duas entradas e produz uma fatia de saída todos os dias. É possível especificar que a fatia de saída de cada dia dependa da fatia de entrada da semana anterior para a entrada semanal, como demonstrado a seguir.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Veja [Funções e variáveis do sistema do Data Factory](data-factory-functions-variables.md) para obter uma lista das funções e variáveis às quais o Azure Data Factory dá suporte.

## <a name="appendix"></a>Apêndice

### <a name="example-copy-sequentially"></a>Exemplo: copiar sequencialmente
É possível executar várias operações de cópia, uma após a outra de maneira sequencial/ordenada. Por exemplo, você pode ter duas atividades de cópia em um pipeline (CopyActivity1 e CopyActivity2) com os seguintes conjuntos de dados de saída dos dados de entrada:   

CopyActivity1

Entrada: Dataset. Saída: Dataset2.

CopyActivity2

Entrada: Dataset2.  Saída: Dataset3.

CopyActivity2 seria executado somente se CopyActivity1 tivesse sido executado com êxito e Dataset2 estivesse disponível.

Veja o exemplo de JSON do pipeline:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observe que no exemplo, o conjunto de dados de saída da primeira atividade de cópia (Dataset2) é especificado como entrada para a segunda atividade. Portanto, a segunda atividade será executada somente quando o conjunto de dados de saída da primeira atividade estiver pronto.  

No exemplo, CopyActivity2 pode ter uma entrada diferente como Dataset3, mas você especifica Dataset2 como uma entrada para CopyActivity2 para que a atividade não seja executada até que CopyActivity1 seja concluída. Por exemplo: 

CopyActivity1

Entrada: Dataset1. Saída: Dataset2.

CopyActivity2

Entradas: Dataset3, Dataset2. Saída: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observe que no exemplo, dois conjuntos de dados de entrada são especificados para a segunda atividade de cópia. Quando várias entradas forem especificadas, somente o primeiro conjunto de dados de entrada será usado para copiar dados, mas outros conjuntos de dados serão usados como dependências. CopyActivity2 começaria apenas quando as seguintes condições fossem atendidas:

* CopyActivity1 foi concluído com êxito e Dataset2 está disponível. Esse conjunto de dados não é usado ao copiar dados para Dataset4. Ele atua apenas como uma dependência de agendamento de CopyActivity2.   
* Dataset3 está disponível. Esse conjunto de dados representa os dados que são copiados para o destino. 
