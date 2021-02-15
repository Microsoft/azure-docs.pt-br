---
title: Criar gatilhos de janela em cascata no Azure Data Factory
description: Saiba como criar um gatilho no Azure Data Factory que execute um pipeline em uma janela em cascata.
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: f5bc9951229c61dd988f44b06b8fcd40881226ae
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393693"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Criar um gatilho que execute um pipeline em uma janela em cascata
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece etapas para criar, iniciar e monitorar um gatilho de janela em cascata. Para obter informações gerais sobre gatilhos e os tipos com suporte, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md).

Os gatilhos de janela em cascata são um tipo de gatilho acionado em um intervalo de tempo periódico a partir de uma hora de início especificada, enquanto mantém o estado. As janelas em cascata são uma série de intervalos de tempo de tamanho fixo, não sobrepostos e contíguos. Um gatilho de janela em cascata tem uma relação de um para um com um pipeline, e só pode fazer referência a um único pipeline. O gatilho de janela em cascata é uma alternativa mais pesada para o gatilho de agendamento que oferece um conjunto de recursos para cenários complexos ([dependência de outros gatilhos de janela em cascata](#tumbling-window-trigger-dependency), [execução de um trabalho com falha](tumbling-window-trigger-dependency.md#monitor-dependencies) e [definição de repetição de usuário para pipelines](#user-assigned-retries-of-pipelines)). Para entender ainda mais a diferença entre o gatilho de agendamento e o gatilho de janela em cascata, visite [aqui](concepts-pipeline-execution-triggers.md#trigger-type-comparison).

## <a name="data-factory-ui"></a>IU do Data Factory

1. Para criar um gatilho de janela do em cascata na interface do usuário do Data Factory, selecione a guia **gatilhos** e, em seguida, selecione **novo**. 
1. Depois que o painel de configuração do gatilho for aberto, selecione **janela em cascata** e defina as propriedades do gatilho da janela em cascata. 
1. Quando terminar, selecione **Salvar**.

![Criar um gatilho de janela em cascata no portal do Azure](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Propriedades do tipo de gatilho da janela em cascata

Uma janela em cascata tem as seguintes propriedades de tipo de gatilho:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

A seguinte tabela fornece uma visão geral de alto nível dos principais elementos de JSON relacionados à recorrência e ao agendamento de um gatilho de janela em cascata:

| Elemento JSON | Descrição | Type | Valores permitidos | Obrigatório |
|:--- |:--- |:--- |:--- |:--- |
| **tipo** | O tipo do gatilho. O tipo é o valor fixo "TumblingWindowTrigger". | String | "TumblingWindowTrigger" | Sim |
| **runtimeState** | O estado atual do tempo de execução do gatilho.<br/>**Observação**: esse elemento é \<readOnly> . | String | “Iniciado”, “Interrompido”, “Desabilitado” | Sim |
| **frequência** | Uma cadeia de caracteres que representa a unidade de frequência (em minutos ou horas) no qual o gatilho ocorre periodicamente. Se os valores de dados **startTime** são mais granulares do que o valor de **frequência**, os dados **startTime** são consideradas quando os limites da janela são computados. Por exemplo, se o valor de **frequência** for de hora em hora e o valor **startTime** for 2017-09-01T10:10:10Z, a primeira janela será (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | String | "minuto", "hora"  | Sim |
| **intervalo** | Um inteiro positivo que indica o intervalo para o valor de **frequência**, que determina a frequência com a qual o gatilho é executado. Por exemplo, se **intervalo** for 3 e **frequência** é "hora", o gatilho é repetido a cada 3 horas. <br/>**Observação**: o intervalo mínimo da janela é de 5 minutos. | Integer | Um número inteiro positivo. | Sim |
| **startTime**| A primeira ocorrência, que pode estar no passado. O primeiro intervalo do gatilho é (**startTime**, **startTime** + **intervalo**). | Datetime | Um valor Datetime. | Sim |
| **Final**| A última ocorrência, que pode estar no passado. | Datetime | Um valor Datetime. | Sim |
| **retardo** | A quantidade de tempo para atrasar o início do processamento de dados para a janela. A execução do pipeline é iniciada após o tempo de execução esperado mais a quantidade de **atraso**. O **atraso** define quanto tempo o gatilho espera antes de disparar uma nova execução. O **atraso** não altera a janela **startTime**. Por exemplo, um valor **atraso** igual a 00:10:00 indica um atraso de 10 minutos. | Timespan<br/>(hh:mm:ss)  | Um valor temporal em que o padrão é 00:00:00. | Não |
| **maxConcurrency** | O número de execuções do gatilho simultâneas que são acionadas para janelas prontas. Por exemplo, para fazer o preenchimento por hora é executada para resultados de ontem em 24 janelas. Se **maxConcurrency** = 10, os eventos de gatilho serão disparados somente para as 10 primeiras janelas (00:00-01:00 - 09:00-10:00). Após a conclusão das primeiras 10 execuções de pipeline disparada, as execuções de gatilho serão disparadas para as próximas 10 janelas (10:00-11:00-19:00 às 20:00). Continuando com esse exemplo de **maxConcurrency** = 10, se houver 10 janelas prontas, há 10 execuções totais de pipeline. Se houver apenas 1 janela pronta, há apenas 1 execução do pipeline. | Integer | Um número inteiro entre 1 e 50. | Sim |
| **retryPolicy: Count** | O número de repetições antes da execução do pipeline ser marcada como "Failed."  | Integer | Um inteiro, em que o padrão é 0 (sem repetições). | Não |
| **retryPolicy: intervalInSeconds** | O intervalo entre tentativas de repetição especificado em segundos. | Integer | O número de segundos, em que o padrão é 30. | Não |
| **depende: tipo** | O tipo de TumblingWindowTriggerReference. Necessário se uma dependência for definida. | String |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Não |
| **depende: tamanho** | O tamanho da janela de dependência em cascata. | Timespan<br/>(hh:mm:ss)  | Um valor de TimeSpan positivo em que o padrão é o tamanho da janela do gatilho filho  | Não |
| **depende: deslocamento** | O deslocamento do gatilho de dependência. | Timespan<br/>(hh:mm:ss) |  Um valor TimeSpan que deve ser negativo em uma autodependência. Se nenhum valor for especificado, a janela será igual ao próprio gatilho. | Dependência automática: Sim<br/>Outro: não  |

> [!NOTE]
> Depois que um gatilho de janela em cascata é publicado, o **intervalo** e a **frequência** não podem ser editados.

### <a name="windowstart-and-windowend-system-variables"></a>Variáveis do sistema WindowStart e WindowEnd

Você pode usar as variáveis de sistema **WindowStart** e **WindowEnd** do gatilho de janela em cascata na sua definição de **pipeline** (ou seja, para a parte de uma consulta). Passar as variáveis de sistema como parâmetros para o pipeline na definição de **gatilho**. O exemplo a seguir mostra como passar essas variáveis como parâmetros:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Para usar os valores variáveis de sistema de **WindowStart** e **WindowEnd** na definição de pipeline, use adequadamente seus parâmetros de "MyWindowStart" e "MyWindowEnd".

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ordem de execução de janelas em um cenário de compensação

Se o StartTime de Trigger estiver no passado, com base nessa fórmula, M = (CurrentTime-TriggerStartTime)/TumblingWindowSize, o gatilho gerará {M} aterramento (passado) é executado em paralelo, respeitando a simultaneidade do gatilho antes de executar as execuções futuras. A ordem de execução para o Windows é determinística, do mais antigo aos intervalos mais recentes. Atualmente, esse comportamento não pode ser modificado.

### <a name="existing-triggerresource-elements"></a>Elementos TriggerResource existentes

Os seguintes pontos se aplicam à atualização de elementos **TriggerResource** existentes:

* O valor do elemento **Frequency** (ou tamanho da janela) do gatilho juntamente com o elemento **Interval** não pode ser alterado depois que o gatilho é criado. Isso é necessário para o funcionamento adequado da execução do triggerRun e avaliações de dependência
* Se o valor para o elemento **endTime** do gatilho altera (adicionado ou atualizado), o estado das janelas que já foram processadas *não* é redefinido. O gatilho respeita o novo valor **endTime**. Se o novo valor **endTime** estiver antes da janela que já foi executado, o gatilho é interrompido. Caso contrário, o gatilho interrompe quando o novo valor **endTime** for encontrado.

### <a name="user-assigned-retries-of-pipelines"></a>Tentativas de repetição de pipelines atribuídas pelo usuário

No caso de falhas de pipeline, o gatilho de janela em cascata pode repetir a execução do pipeline referenciado automaticamente, usando os mesmos parâmetros de entrada, sem a intervenção do usuário. Isso pode ser especificado usando a propriedade "retryPolicy" na definição do gatilho.

### <a name="tumbling-window-trigger-dependency"></a>Dependência de gatilho de janela em cascata

Se você quiser garantir que um gatilho de janela em cascata seja executado somente após a execução bem-sucedida de outro gatilho de janela em cascata na data factory, [crie uma dependência de gatilho de janela em cascata](tumbling-window-trigger-dependency.md).

### <a name="cancel-tumbling-window-run"></a>Cancelar a execução da janela em cascata

Você pode cancelar execuções para um gatilho de janela em cascata, se a janela específica estiver em _espera_, _aguardando dependência_ ou _executando_ o estado

* Se a janela estiver em estado de **execução** , cancele a _execução do pipeline_ associado e a execução do gatilho será marcada como _cancelada_ posteriormente
* Se a janela estiver em **espera** ou **aguardando** o estado de dependência, você poderá cancelar a janela do monitoramento:

![Cancelar um gatilho de janela em cascata da página monitoramento](media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger.png)

Você também pode executar novamente uma janela cancelada. A execução executará as _últimas_ definições publicadas do gatilho, e as dependências para a janela especificada serão _reavaliadas_ após a nova execução

![Executar novamente um gatilho de janela em cascata para execuções canceladas anteriormente](media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger.png)

## <a name="sample-for-azure-powershell"></a>Exemplo para o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Esta seção mostra como usar o Azure PowerShell para criar, iniciar e monitorar um gatilho.

1. Crie um arquivo JSON chamado **MyTrigger.js** na pasta C:\ADFv2QuickStartPSH\ com o seguinte conteúdo:

    > [!IMPORTANT]
    > Antes de salvar o arquivo JSON, defina o valor do elemento **startTime** com a hora UTC atual. Defina o valor do elemento **endTime** como uma hora após a hora UTC atual.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Crie um gatilho usando o cmdlet **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Confirme se o status do gatilho é **interrompido** usando o cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Inicie o gatilho usando o cmdlet **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confirme se o status do gatilho é **iniciado** usando o cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Obtenha as execuções do gatilho em Azure PowerShell usando o cmdlet **Get-AzDataFactoryV2TriggerRun** . Para obter informações sobre as execuções do gatilho, execute o comando a seguir periodicamente. Atualize os valores de **TriggerRunStartedAfter** e **TriggerRunStartedBefore** para que correspondam aos valores na definição de gatilho:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Para monitorar execuções de gatilho e de pipeline no portal do Azure, consulte [Monitorar execuções de pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Criar uma dependência de gatilho de janela em cascata](tumbling-window-trigger-dependency.md)
