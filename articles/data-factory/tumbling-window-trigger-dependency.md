---
title: Criar dependências de gatilho de janela em cascata
description: Saiba como criar dependência em um gatilho de janela em cascata no Azure Data Factory.
services: data-factory
ms.author: chez
author: chez-charlie
manager: weetok
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: 221f8bd7908613812a728d420a68b747051e095b
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508791"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Criar uma dependência de gatilho de janela em cascata
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece etapas para criar uma dependência em um gatilho de janela em cascata. Para obter informações gerais sobre gatilhos de Janela em Cascata, consulte [Como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md).

Para criar uma cadeia de dependência e certificar-se de que um gatilho executará somente após a execução com êxito de outro gatilho no data factory, use esse recurso avançado para criar uma dependência de janela em cascata.

Para ver uma demonstração sobre como criar pipelines dependentes em seu Azure Data Factory usando o gatilho de janela em cascata, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Criar uma dependência na interface do usuário do Data Factory

Para criar dependência em um gatilho, selecione **Gatilho > Avançado > Novo** e, em seguida, escolha o gatilho para depender do deslocamento e tamanho apropriados. Selecione **Concluir** e publique as alterações do data factory para que as dependências entrem em vigor.

![Criação de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "Criação de dependência")

## <a name="tumbling-window-dependency-properties"></a>Propriedades de dependência de janela em cascata

Um gatilho de janela em cascata com uma dependência tem as seguintes propriedades:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
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
        }
    }
}
```

A tabela a seguir fornece a lista de atributos necessários para definir uma dependência de Janela em Cascata.

| **Nome da propriedade** | **Descrição**  | **Tipo** | **Necessário** |
|---|---|---|---|
| type  | Todos os gatilhos da janela em cascata existentes são exibidos neste menu suspenso. Escolha o gatilho para assumir a dependência.  | TumblingWindowTriggerDependencyReference ou SelfDependencyTumblingWindowTriggerReference | Sim |
| deslocamento | Deslocamento do gatilho de dependência. Forneça um valor no formato de intervalo de tempo e deslocamentos negativos e positivos são permitidos. Essa propriedade será obrigatória se o gatilho estiver dependendo de si mesmo e em todos os outros casos for opcional. A autodependência deverá sempre ser um deslocamento negativo. Se nenhum valor for especificado, a janela será igual ao próprio gatilho. | Timespan<br/>(hh:mm:ss) | Dependência automática: Sim<br/>Outro: não |
| tamanho | Tamanho da janela em cascata de dependência. Forneça um valor de TimeSpan positivo. Essa propriedade é opcional. | Timespan<br/>(hh:mm:ss) | Não  |

> [!NOTE]
> Um gatilho de janela em cascata pode depender de um máximo de cinco outros gatilhos.

## <a name="tumbling-window-self-dependency-properties"></a>Propriedades de autodependência de janela em cascata

Em cenários em que o gatilho não deve prosseguir para a próxima janela até que a janela anterior seja concluída com êxito, crie uma autodependência. Um gatilho de dependência automática dependente do sucesso das execuções anteriores de si mesmo dentro da hora anterior terá as propriedades indicadas no código a seguir.

> [!NOTE]
> Se o pipeline disparado depender da saída de pipelines em janelas disparadas anteriormente, é recomendável usar apenas a janela em cascata disparará a autodependência. Para limitar as execuções de gatilho paralelo, defina a simultaneidade do gatilho maximimum.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Cenários e exemplos de uso

Veja abaixo as ilustrações de cenários e o uso de propriedades de dependência de janela em cascata.

### <a name="dependency-offset"></a>Deslocamento de dependência

![Exemplo de deslocamento](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "Exemplo de deslocamento")

### <a name="dependency-size"></a>Tamanho da dependência

![Exemplo de tamanho](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "Exemplo de tamanho")

### <a name="self-dependency"></a>Autodependência

![Dependência automática](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "Autodependência")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependência de outro gatilho de janela em cascata

Um trabalho de processamento diário de telemetria dependendo de outro trabalho diário agregando a saída dos últimos sete dias e gera fluxos de janela sem interrupção de sete dias:

![Exemplo de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "Exemplo de dependência")

### <a name="dependency-on-itself"></a>Dependência de si próprio

Um trabalho diário sem lacunas nos fluxos de saída do trabalho:

![Exemplo de dependência automática](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "Exemplo de dependência automática")

## <a name="monitor-dependencies"></a>Monitorar dependências

Você pode monitorar a cadeia de dependência e as janelas correspondentes na página monitoramento de execução de gatilho. Navegue até  **Monitoramento > Execuções de Gatilho**. Se um gatilho de janela em cascata tiver dependências, o nome do gatilho terá um hiperlink para a exibição de monitoramento de dependência.  

![Monitorar execuções de gatilho](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "Monitorar execuções de gatilho-inteiro para exibição de dependência de janela em cascata")

Clique no nome do gatilho para exibir as dependências do gatilho. O painel à direita mostra informações detalhadas de execução do gatilho, como RunID, hora da janela, status e assim por diante.

![Exibição da lista de dependências do monitor](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "Exibição da lista de dependências do monitor")

Você pode ver o status das dependências e do Windows para cada gatilho dependente. Se um dos disparadores de dependências falhar, você deverá executá-lo com êxito para que o gatilho dependente seja executado.

Um gatilho de janela em cascata aguardará as dependências por _sete dias_ antes de atingir o tempo limite. Após sete dias, a execução do gatilho falhará.

Para obter mais visual para exibir a agenda de dependência do gatilho, selecione o modo de exibição de Gantt.

![Gráfico de Gantt de dependências do monitor](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "Exibição do gráfico de Gantt de dependências do monitor")

As caixas transparentes mostram as janelas de dependências para cada gatilho dependente de fluxo para baixo, enquanto as caixas de cores sólidas acima mostram execuções de janela individuais. Aqui estão algumas dicas para interpretar a exibição do gráfico de Gantt:

* A caixa transparente renderiza azul quando as janelas dependentes estão no estado pendente ou em execução
* Depois que todas as janelas forem executadas com sucesso para um gatilho dependente, a caixa transparente ficará verde
* A caixa transparente renderiza vermelho quando alguma janela dependente falha. Procure uma caixa vermelha sólida para identificar a execução da janela de falha

Para executar novamente uma janela na exibição de gráfico de Gantt, selecione a caixa de cor sólida para a janela e um painel de ação será exibido com detalhes e execute novamente as opções

## <a name="next-steps"></a>Próximas etapas

* Examine [como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md)
