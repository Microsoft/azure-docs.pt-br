---
title: Criar gatilhos de agendamento no Azure Data Factory
description: Saiba como criar um gatilho no Azure Data Factory que execute um pipeline em um agendamento.
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: f10dac4e70a1edb05f2f2c02c48b9ae16c4f6823
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177803"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Criar um gatilho que executa um pipeline com base em um agendamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece informações sobre o gatilho de agendamento e as etapas para criar, iniciar e monitorar um gatilho de agendamento. Para outros tipos de gatilhos, consulte [Execução de pipeline e gatilhos](concepts-pipeline-execution-triggers.md).

Ao criar um gatilho de agendamento, especifique um agendamento (data de início, recorrência, data de término, etc.) para o gatilho e associe-o a um pipeline. Pipelines e gatilhos têm uma relação de muitos para muitos. Vários gatilhos podem disparar um único pipeline. Um único gatilho pode disparar vários pipelines.

As seções a seguir fornecem etapas para criar um gatilho de agendamento de diferentes maneiras. 

## <a name="data-factory-ui"></a>IU do Data Factory

Crie um **gatilho de agendamento** para agendar a execução periódica de um pipeline (por hora, diariamente, etc.). 

> [!NOTE]
> Para obter uma explicação completa de como criar um pipeline e um gatilho de agendamento, que associa o gatilho ao pipeline e executa e monitora o pipeline, consulte [início rápido: criar um data Factory usando a interface do usuário do data Factory](quickstart-create-data-factory-portal.md).

1. Alterne para a guia **Editar** , mostrada com um símbolo de lápis. 

    ![Mude para a guia Editar](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. Selecione **gatilho** no menu e, em seguida, selecione **novo/editar**. 

    ![Novo menu de gatilho](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. Na página **Adicionar gatilhos** , selecione **escolher gatilho...** e, em seguida, selecione **+ novo**. 

    ![Adicionar gatilhos – novo gatilho](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. Na página **novo gatilho** , execute as seguintes etapas: 

    1. Confirme se **Agendamento** está selecionado para **Tipo**.
    1. Especifique o DateTime inicial do gatilho para a **data de início**. Ele é definido como o DateTime atual em UTC (tempo Universal Coordenado) por padrão.
    1. Especifique o fuso horário no qual o gatilho será criado. A configuração de fuso horário será aplicada a **data de início**, data de **término** e tempos de **execução de agendamento** nas opções de recorrência avançadas. Alterar a configuração de fuso horário não alterará automaticamente a data de início. Certifique-se de que a data de início esteja correta no fuso horário especificado. Observe que o tempo de execução agendado do gatilho será considerado após a data de início (verifique se a data de início é pelo menos 1 minuto menor do que o tempo de execução. em seguida, ele disparará o pipeline na próxima recorrência). 

        > [!NOTE]
        > Para os fusos horários que observam o horário de verão, o tempo de disparo será ajustado automaticamente para a alteração de um ano duas vezes. Para recusar a alteração de salvamento de horário de verão, selecione um fuso horário que não observe o horário de verão, por exemplo, UTC

    1. Especifique **Recorrência** para o gatilho. Selecione um dos valores na lista suspensa (A Cada minuto, Por hora, Diariamente, Semanalmente e Mensalmente). Insira o multiplicador na caixa de texto. Por exemplo, se você deseja que o gatilho seja executado uma vez a cada 15 minutos, selecione **A Cada Minuto** e insira **15** na caixa de texto. 
    1. Para especificar uma data e hora de término, selecione **especificar uma data de término** e especifique _termina em_ e selecione **OK**. Há um custo associado a cada execução do pipeline. Se estiver testando, recomendamos garantir que o pipeline é disparado apenas algumas vezes. No entanto, verifique se há tempo suficiente para a execução do pipeline entre a hora da publicação e a hora de término. O gatilho só entra em vigor depois de você publicar a solução no Data Factory, e não ao salvar o gatilho na interface do usuário.

        ![Configurações do gatilho](./media/how-to-create-schedule-trigger/trigger-settings-01.png)

        ![Configurações do gatilho para data de término](./media/how-to-create-schedule-trigger/trigger-settings-02.png)

1. Na janela **novo gatilho** , selecione **Sim** na opção **ativado** e, em seguida, selecione **OK**. Use essa caixa de seleção para desativar o gatilho mais tarde. 

    ![Configurações de gatilho – botão Avançar](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. Na janela **novo gatilho** , examine a mensagem de aviso e selecione **OK**.

    ![Configurações de gatilho – botão Concluir](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. Selecione **publicar tudo** para publicar as alterações para data Factory. Até que você publique as alterações no Data Factory, o gatilho não começará a disparar as execuções de pipeline. 

    ![Botão Publicar](./media/how-to-create-schedule-trigger/publish-2.png)

1. Alterne para a guia **execuções de pipeline** à esquerda e, em seguida, selecione **Atualizar** para atualizar a lista. Você verá as execuções de pipeline disparadas pelo gatilho agendado. Observe os valores na coluna **disparados por** . Se você usar a opção **disparar agora** , verá o gatilho manual executado na lista. 

    ![Monitorar execuções disparadas](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. Alterne para o modo de exibição de agendamento de **execuções de gatilho**  \   . 

    ![Monitorar execuções de gatilho](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Esta seção mostra como usar o Azure PowerShell para criar, iniciar e monitorar um gatilho de agendamento. Para ver essa amostra funcionando, primeiro acompanhe o [Início Rápido: Criar um data factory usando o Azure PowerShell](quickstart-create-data-factory-powershell.md). Em seguida, adicione o seguinte código ao método principal, que cria e inicia um gatilho de agenda que é executado a cada 15 minutos. O gatilho é associado a um pipeline chamado **Adfv2QuickStartPipeline** que você cria como parte do Início Rápido.

1. Crie um arquivo JSON chamado **MyTrigger.js** na pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

    > [!IMPORTANT]
    > Antes de salvar o arquivo JSON, defina o valor do elemento **startTime** com a hora UTC atual. Defina o valor do elemento **endTime** como uma hora após a hora UTC atual.

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00Z",
                    "endTime": "2017-12-08T01:00:00Z",
                    "timeZone": "UTC"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    No snippet JSON:
    - O elemento **Type** do gatilho é definido como "ScheduleTrigger".
    - O elemento **frequency** é definido como “Minutes” e o elemento **interval** é definido como 15. Assim, o gatilho executa o pipeline a cada 15 minutos entre as horas de início e término.
    - O elemento **timeZone** especifica o fuso horário em que o gatilho é criado. Essa configuração afeta tanto **StartTime** quanto **EndTime**.
    - O elemento **endTime** é uma hora após o valor do elemento **startTime**. Assim, o gatilho executa o pipeline 15 minutos, 30 minutos e 45 minutos após a hora de início. Não se esqueça de atualizar a hora de início para a hora UTC atual e a hora de término para uma hora após a hora de início. 

        > [!IMPORTANT]
        > Para o fuso horário UTC, StartTime e endTime precisam seguir o formato ' AAAA-MM-ddTHH: mm: SS **Z**', enquanto para outras zonas de tempo, StartTime e EndTime seguem ' aaaa-mm-ddThh: mm: SS '. 
        > 
        > Por padrão ISO 8601, o sufixo _Z_ para timestamp marca carimbo de data/hora como fuso horário UTC e renderizar o campo de fuso horário como inútil. Embora o sufixo _Z_ ausente para o fuso horário UTC resultará em um erro na _ativação_ do gatilho.

    - O gatilho está associado ao pipeline **Adfv2QuickStartPipeline**. Para associar vários pipelines a um gatilho, adicione mais seções **pipelineReference**.
    - O pipeline no Início Rápido usa dois valores de **parameters**: **inputPath** e **outputPath**. E você passa valores para esses parâmetros do gatilho.

1. Crie um gatilho usando o cmdlet **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. Confirme se o status do gatilho é **interrompido** usando o cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Inicie o gatilho usando o cmdlet **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Confirme se o status do gatilho é **iniciado** usando o cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  Obtenha as execuções do gatilho em Azure PowerShell usando o cmdlet **Get-AzDataFactoryV2TriggerRun** . Para obter as informações sobre as execuções do gatilho, execute o comando a seguir periodicamente. Atualize os valores de **TriggerRunStartedAfter** e **TriggerRunStartedBefore** para que correspondam aos valores na definição de gatilho:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    > [!NOTE]
    > O tempo de gatilho de gatilhos de agendamento é especificado no carimbo de data/hora UTC. _TriggerRunStartedAfter_ e _TriggerRunStartedBefore_ também esperam carimbo de data/hora UTC

    Para monitorar as execuções de gatilho e de pipeline no portal do Azure, consulte [Monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="net-sdk"></a>SDK .NET

Esta seção mostra como usar o SDK do .NET para criar, iniciar e monitorar um gatilho. Para ver essa amostra funcionando, primeiro acompanhe o [Início Rápido: Criar um data factory usando o SDK do .NET](quickstart-create-data-factory-dot-net.md). Em seguida, adicione o seguinte código ao método principal, que cria e inicia um gatilho de agenda que é executado a cada 15 minutos. O gatilho é associado a um pipeline chamado **Adfv2QuickStartPipeline** que você cria como parte do Início Rápido.

Para criar e iniciar um gatilho de agendamento que é executado a cada 15 minutos, adicione o seguinte código ao método principal:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Para criar gatilhos em um fuso horário diferente, exceto o UTC, as seguintes configurações são necessárias:
```csharp
<<ClientInstance>>.SerializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.SerializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
<<ClientInstance>>.SerializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.DeserializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
```

Para monitorar uma execução de gatilho, adicione o seguinte código antes da última instrução `Console.WriteLine` na amostra:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Para monitorar as execuções de gatilho e de pipeline no portal do Azure, consulte [Monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="python-sdk"></a>SDK do Python

Esta seção mostra como usar o SDK do Python para criar, iniciar e monitorar um gatilho. Para ver essa amostra funcionando, primeiro acompanhe o [Início Rápido: Criar um data factory usando o SDK do Python](quickstart-create-data-factory-python.md). Em seguida, adicione o bloco de código a seguir após o bloco de código “monitorar a execução do pipeline” no script do Python. Esse código cria um gatilho de agenda que é executado a cada 15 minutos entre os horários de início e término especificados. Atualize a variável **start_time** para a hora UTC atual e a variável **end_time** para uma hora após a hora UTC atual.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00Z', end_time='2017-12-12T05:00:00Z', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Para monitorar as execuções de gatilho e de pipeline no portal do Azure, consulte [Monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Você pode usar um modelo do Azure Resource Manager para criar um gatilho. Para obter instruções passo a passo, consulte [Criar um Azure Data Factory usando um modelo do Resource Manager](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Passe a hora de início do gatilho para um pipeline

O Azure Data Factory versão 1 é compatível com leitura ou gravação de dados particionados usando as variáveis do sistema **SliceStart**, **SliceEnd**, **WindowStart** e **WindowEnd**. Na versão atual do Azure Data Factory, é possível obter esse comportamento usando um parâmetro de pipeline. A hora de início e a hora agendada para o gatilho estão definidos com o valor para o parâmetro do pipeline. No seguinte exemplo, o horário agendado para o gatilho é passado como um valor para o parâmetro **scheduledRunTime** do pipeline:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON schema

A seguinte definição de JSON mostra como criar um gatilho de agendamento com o agendamento e a recorrência:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
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
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  A propriedade **parameters** é uma propriedade obrigatória do elemento **pipelines**. Se o pipeline não usar nenhum parâmetro, será necessário incluir uma definição de JSON vazia na propriedade **parameters**.


### <a name="schema-overview"></a>Visão geral do esquema

A seguinte tabela fornece uma visão geral de alto nível dos principais elementos de esquema relacionados à recorrência e ao agendamento de um gatilho:

| Propriedade JSON | Descrição |
|:--- |:--- |
| **startTime** | Um valor de Data/Hora. Para agendamentos simples, o valor da propriedade **startTime** aplica-se à primeira ocorrência. Para agendamentos complexos, o gatilho não é iniciado antes do valor de **startTime** especificado. <br> Para o fuso horário UTC, o formato é `'yyyy-MM-ddTHH:mm:ssZ'` , para outro fuso horário, o formato é `'yyyy-MM-ddTHH:mm:ss'` . |
| **Final** | A data e a hora de início do gatilho. O gatilho não é executado após a data e a hora de término especificadas. O valor da propriedade não pode estar no passado. Essa propriedade é opcional.  <br> Para o fuso horário UTC, o formato é `'yyyy-MM-ddTHH:mm:ssZ'` , para outro fuso horário, o formato é `'yyyy-MM-ddTHH:mm:ss'` . |
| **Fuso horário** | O fuso horário em que o gatilho é criado. Essa configuração afeta **StartTime**, **EndTime** e **Schedule**. Ver a [lista de fuso horário com suporte](#time-zone-option) |
| **Recurrence** | Um objeto de recorrência que especifica as regras de recorrência para o gatilho. O objeto de recorrência dá suporte aos elementos **frequência**, **intervalo**, **EndTime**, **contagem** e **agendamento** . Quando um objeto de recorrência é definido, o elemento **frequency** é obrigatório. Os outros elementos do objeto de recorrência são opcionais. |
| **frequência** | A unidade de frequência com a qual o gatilho se repete. Os valores com suporte incluem “minute”, “hour”, “day”, “week” e “month”. |
| **intervalo** | Um inteiro positivo que indica o intervalo para o valor de **frequência**, que determina a frequência com a qual o gatilho é executado. Por exemplo, se **interval** for 3 e **frequency** é "week", o gatilho é repetido a cada 3 semanas. |
| **agendamento** | O agendamento de recorrência do gatilho. Um gatilho com um valor de **frequency** especificado altera sua recorrência com base em um agendamento de recorrência. A propriedade **schedule** contém modificações para a recorrência que se baseiam em minutos, horas, dias da semana, dias do mês e número da semana.

> [!IMPORTANT]
> Para o fuso horário UTC, StartTime e endTime precisam seguir o formato ' AAAA-MM-ddTHH: mm: SS **Z**', enquanto para outras zonas de tempo, StartTime e EndTime seguem ' aaaa-mm-ddThh: mm: SS '. 
> 
> Por padrão ISO 8601, o sufixo _Z_ para timestamp marca carimbo de data/hora como fuso horário UTC e renderizar o campo de fuso horário como inútil. Embora o sufixo _Z_ ausente para o fuso horário UTC resultará em um erro na _ativação_ do gatilho.

### <a name="schema-defaults-limits-and-examples"></a>Padrões, limites e exemplos de esquema

| Propriedade JSON | Type | Obrigatório | Valor padrão | Valores válidos | Exemplo |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | String | Sim | Nenhum | Data e hora ISO 8601 | para fuso horário UTC `"startTime" : "2013-01-09T09:30:00-08:00Z"` <br> para outro fuso horário `"2013-01-09T09:30:00-08:00"` |
| **Fuso horário** | String | Sim | Nenhum | [Valores de fuso horário](#time-zone-option)  | `"UTC"` |
| **Recurrence** | Objeto | Sim | Nenhum | Objeto de recorrência | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **intervalo** | Número | Não | 1 | 1 a 1.000 | `"interval":10` |
| **Final** | String | Sim | Nenhum | Um valor de Data/Hora que representa uma hora no futuro. | para fuso horário UTC `"endTime" : "2013-02-09T09:30:00-08:00Z"` <br> para outro fuso horário `"endTime" : "2013-02-09T09:30:00-08:00"`|
| **agendamento** | Objeto | Não | Nenhum | Objeto Agendamento | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="time-zone-option"></a>Opção de fuso horário

Aqui estão alguns dos fusos horários com suporte para gatilhos de agendamento:

| Fuso horário | Diferença UTC (salvamento não-verão) | Valor do fuso horário | Observe o horário de verão | Formato de carimbo de data/hora |
| :--- | :--- | :--- | :--- | :--- |
| Tempo universal coordenado | 0 | `UTC` | Não | `'yyyy-MM-ddTHH:mm:ssZ'`|
| Hora do Pacífico (PT) | -8 | `Pacific Standard Time` | Sim | `'yyyy-MM-ddTHH:mm:ss'` |
| Hora central (CT) | -6 | `Central Standard Time` | Sim | `'yyyy-MM-ddTHH:mm:ss'` |
| Hora do leste (ET) | -5 | `Eastern Standard Time` | Sim | `'yyyy-MM-ddTHH:mm:ss'` |
| Greenwich Mean Time (GMT) | 0 | `GMT Standard Time` | Sim | `'yyyy-MM-ddTHH:mm:ss'` |
| Hora oficial da Europa Central | +1 | `W. Europe Standard Time` | Sim | `'yyyy-MM-ddTHH:mm:ss'` |
| Hora padrão da Índia (IST) | + 5:30 | `India Standard Time` | Não | `'yyyy-MM-ddTHH:mm:ss'` |
| Hora oficial da China | + 8 | `China Standard Time` | Não | `'yyyy-MM-ddTHH:mm:ss'` |

Esta lista está incompleta. Para obter uma lista completa das opções de fuso horário, explore na [página de criação de gatilho](#data-factory-ui) do portal data Factory

### <a name="starttime-property"></a>Propriedade startTime
A seguinte tabela mostra como a propriedade **startTime** controla uma execução de gatilho:

| Valor startTime | Recorrência sem agendamento | Recorrência com agendamento |
|:--- |:--- |:--- |
| Hora de início no passado | Calcula a primeira hora de execução futura após a hora de início e executa nessa hora.<br/><br/>Executa as execuções seguintes com base no cálculo do tempo da última execução.<br/><br/>Consulte o exemplo após esta tabela. | O gatilho _não inicia antes_ da hora de início especificada. A primeira ocorrência baseia-se no agendamento calculado com base na hora de início.<br/><br/>Executa as execuções seguintes com base no agendamento de recorrência. |
| Hora de início no futuro ou no momento | É executado uma vez na hora de início especificada.<br/><br/>Executa as execuções seguintes com base no cálculo do tempo da última execução. | O gatilho é iniciado _não antes da_ hora de início especificada. A primeira ocorrência baseia-se no agendamento calculado com base na hora de início.<br/><br/>Executa as execuções seguintes com base no agendamento de recorrência. |

Vejamos um exemplo do que acontece quando a hora de início está no passado, com uma recorrência, mas sem agendamento. Suponha que a hora atual seja `2017-04-08 13:00`, a hora de início seja `2017-04-07 14:00` e a recorrência seja a cada dois dias. (O valor de **recorrência** é definido definindo a propriedade **Frequency** como "Day" e a propriedade **Interval** como 2.) Observe que o valor **StartTime** está no passado e ocorre antes da hora atual.

Sob essas condições, a primeira execução está em `2017-04-09` às `14:00` . O mecanismo do Agendador calcula as ocorrências de execução desde a hora de início. As instâncias no passado serão descartadas. O mecanismo usa a próxima instância que ocorrer no futuro. Nesse cenário, a hora de início é `2017-04-07` em `2:00pm` , portanto, a próxima instância é de dois dias a partir dessa hora, que está `2017-04-09` em `2:00pm` .

A primeira hora de execução é igual, mesmo que o valor de **startTime** seja `2017-04-05 14:00` ou `2017-04-01 14:00`. Após a primeira execução, as execuções seguintes são calculadas com o uso do agendamento. Portanto, as execuções subsequentes estão em, em seguida, em, depois em e `2017-04-11` `2:00pm` assim por `2017-04-13` `2:00pm` `2017-04-15` `2:00pm` diante.

Por fim, quando as horas ou os minutos não estiverem definidos no agendamento de um gatilho, as horas ou os minutos da primeira execução serão usados como padrões.

### <a name="schedule-property"></a>Propriedade schedule

Por um lado, o uso de um agendamento pode limitar o número de execuções de gatilho. Por exemplo, se um gatilho com uma frequência mensal é agendado para execução somente no dia 31, ele é executado apenas nos meses que têm o dia 31.

Enquanto que, um elemento schedule também pode expandir o número de execuções do gatilho. Por exemplo, um gatilho com uma frequência mensal agendado para execução nos dias 1 e 2 do mês é executado nos dias 1 e 2 do mês, em vez de uma vez por mês.

Se vários elementos **schedule** forem especificados, a ordem de avaliação será feita da maior configuração de agendamento para a menor. A avaliação começa com o número da semana e, em seguida, dia do mês, dia da semana, hora e, por fim, minuto.

A seguinte tabela descreve elementos **schedule** em detalhes:

| Elemento JSON | Descrição | Valores válidos |
|:--- |:--- |:--- |
| **alguns** | Minutos da hora em que o gatilho será executado. | <ul><li>Integer</li><li>Matriz de inteiros</li></ul>
| **duração** | As horas do dia em que o gatilho será executado. | <ul><li>Integer</li><li>Matriz de inteiros</li></ul> |
| **Dias da semana** | Dias da semana em que o gatilho é executado. O valor pode ser especificado apenas com uma frequência semanal. | <ul><li>Segunda-feira, Terça-feira, Quarta-feira, Quinta-feira, Sexta-feira, Sábado e Domingo</li><li>Matriz de valores de dia (o tamanho máximo da matriz é 7)</li><li>Os valores de dia não diferenciam maiúsculas de minúsculas</li></ul> |
| **monthlyOccurrences** | Dias do mês em que o gatilho é executado. O valor pode ser especificado apenas com uma frequência mensal. | <ul><li>Matriz de objetos **monthlyOccurrence** : `{ "day": day,  "occurrence": occurrence }`.</li><li>O atributo **day** é o dia da semana no qual o gatilho é executado. Por exemplo, uma propriedade **monthlyOccurrences** com um valor de **day** igual a `{Sunday}` significa todo domingo do mês. O atributo **day** é obrigatório.</li><li>O atributo **occurrence** é a ocorrência de **day** especificado durante o mês. Por exemplo, uma propriedade **monthlyOccurrences** com os valores de **day** e **occurrence** iguais a `{Sunday, -1}` significa o último domingo do mês. O atributo **occurrence** é opcional.</li></ul> |
| **monthDays** | Dia do mês em que o gatilho é executado. O valor pode ser especificado apenas com uma frequência mensal. | <ul><li>Qualquer valor <= -1 e >= -31</li><li>Qualquer valor >= 1 e <= 31</li><li>Matriz de valores</li></ul> |

## <a name="examples-of-trigger-recurrence-schedules"></a>Exemplos de agendamentos de recorrência de gatilho

Esta seção fornece exemplos de agendamentos de recorrência e se concentra no objeto **schedule** e seus elementos.

Os exemplos pressupõem que o valor de **interval** seja 1 e que o valor de **frequency** esteja correto de acordo com a definição de agendamento. Por exemplo, não é possível ter um valor de **frequency** igual a "day" e também ter uma modificação de "monthDays" no objeto **schedule**. Restrições desse tipo são mencionadas na tabela na seção anterior.

| Exemplo | Descrição |
|:--- |:--- |
| `{"hours":[5]}` | Executar às 5h todos os dias. |
| `{"minutes":[15], "hours":[5]}` | Executar às 5h15 todos os dias. |
| `{"minutes":[15], "hours":[5,17]}` | Executar às 5h15 e 17h15 todos os dias. |
| `{"minutes":[15,45], "hours":[5,17]}` | Executar às 5h15, 5h45, 17h15 e 17h45 todos os dias. |
| `{"minutes":[0,15,30,45]}` | Executar a cada 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Executar a cada hora. Este gatilho é executado a cada hora. Os minutos são controlados pelo valor de **startTime**, quando um valor é especificado. Se um valor não for especificado, os minutos serão controlados pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 12h25, o gatilho será executado às 24h25, 1h25, 2h25, … e 23h25.<br/><br/>Esse agendamento é equivalente a ter um gatilho com um valor de **Frequency** de "hora", um valor de **intervalo** de 1 e nenhum **agendamento**.  Esse agendamento pode ser usado com valores diferentes de **frequency** e **interval** para criar outros gatilhos. Por exemplo, quando o valor de **Frequency** é "month", o agendamento é executado somente uma vez por mês, em vez de todos os dias, quando o valor de **Frequency** é "Day". |
| `{"minutes":[0]}` | Executar a cada hora na hora. Esse gatilho é executado a cada hora na hora, começando às 24h, 1h, 2h e assim por diante.<br/><br/>Esse agendamento é equivalente a um gatilho com um valor de **frequency** igual a “hour” e um valor de **startTime** igual a zero minutos ou nenhum **schedule**, mas um valor de **frequency** igual a “day”. Se o valor de **frequency** é "week" ou "month", o agendamento é executado somente um dia da semana ou um dia do mês, respectivamente. |
| `{"minutes":[15]}` | Executa 15 minutos após cada hora. Esse gatilho é executado a cada hora em 15 minutos após a hora, começando às 24h15, 1h15, 2h15 e assim por diante, terminando em 23h15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Executar às 17h aos sábados toda semana. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17h às segundas-feiras, quartas-feiras e sextas-feiras toda semana. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Executar às 17h15 e às 17h45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar a cada 15 minutos em dias da semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Executar a cada 15 minutos em dias da semana entre 9h e 16h45. |
| `{"weekDays":["tuesday", "thursday"]}` | Executar às terças-feiras e quintas-feiras na hora de início especificada. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Executar às 6h no 28º dia de cada mês (supondo um valor de **frequency** igual a “month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Executar às 6h no último dia do mês. Para executar um gatilho no último dia de um mês, use -1, em vez do dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Executar às 6h no primeiro e último dia de cada mês. |
| `{monthDays":[1,14]}` | Executar no primeiro e no 14º dia de cada mês na hora de início especificada. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês às 5h. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Executar na primeira sexta-feira de cada mês na hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Executar na terceira sexta-feira do final do mês, todo mês, na hora de início especificada. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sexta-feira de cada mês às 5h15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Executar na primeira e última sexta-feira de cada mês na hora de início especificada. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Executar na quinta sexta-feira de cada mês na hora de início especificada. Quando não houver nenhuma quinta sexta-feira em um mês, o pipeline não será executado, uma vez que ele está agendado para ser executado apenas nas quintas sextas-feiras. Para executar o gatilho na última sexta-feira do mês, considere o uso de -1, em vez de 5 para o valor de **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Executar a cada 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Executar às 5h15, 5h45, 17h15 e 17h45 na terceira quarta-feira de cada mês. |

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
- Saiba como referenciar metadados de gatilho no pipeline, consulte [metadados de gatilho de referência em execuções de pipeline](how-to-use-trigger-parameterization.md)
