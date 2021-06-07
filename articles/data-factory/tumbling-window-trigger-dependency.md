---
title: Criar dependências de gatilho periódico
description: Saiba como criar dependência em um gatilho de janela em cascata no Azure Data Factory.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: f969c06a3419a8017cfc5ebc0de19caa67c8dc68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361461"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Criar uma dependência de gatilho de janela em cascata
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece etapas para criar uma dependência em um gatilho de janela em cascata. Para obter informações gerais sobre gatilhos de Janela em Cascata, consulte [Como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md).

Para criar uma cadeia de dependência e certificar-se de que um gatilho executará somente após a execução com êxito de outro gatilho no data factory, use esse recurso avançado para criar uma dependência de janela em cascata.

Para obter uma demonstração sobre como criar pipelines dependentes no Azure Data Factory usando o gatilho periódico, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Criar uma dependência na interface do usuário do Data Factory

Para criar dependência em um gatilho, selecione **Gatilho > Avançado > Novo** e, em seguida, escolha o gatilho para depender do deslocamento e tamanho apropriados. Selecione **Concluir** e publique as alterações do data factory para que as dependências entrem em vigor.

![Criação de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "Criação de dependência")

## <a name="tumbling-window-dependency-properties"></a>Propriedades de dependência de janela em cascata

Um gatilho periódico com uma dependência tem as seguintes propriedades:

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

| **Nome da Propriedade** | **Descrição**  | **Tipo** | **Necessário** |
|---|---|---|---|
| type  | Todos os gatilhos da janela em cascata existentes são exibidos neste menu suspenso. Escolha o gatilho para assumir a dependência.  | TumblingWindowTriggerDependencyReference ou SelfDependencyTumblingWindowTriggerReference | Sim |
| deslocamento | Deslocamento do gatilho de dependência. Forneça um valor no formato de período, bem como deslocamentos negativos e positivos são permitidos. Essa propriedade será obrigatória se o gatilho estiver dependendo de si mesmo e em todos os outros casos for opcional. A autodependência deverá sempre ser um deslocamento negativo. Se nenhum valor for especificado, a janela será igual ao próprio gatilho. | Timespan<br/>(hh:mm:ss) | Autodependência: Sim<br/>Outros: Não |
| tamanho | Tamanho da janela em cascata de dependência. Forneça um valor de período positivo. Essa propriedade é opcional. | Timespan<br/>(hh:mm:ss) | Não  |

> [!NOTE]
> Um gatilho periódico pode depender de, no máximo, cinco outros gatilhos.

## <a name="tumbling-window-self-dependency-properties"></a>Propriedades de autodependência de janela em cascata

Nos cenários em que o gatilho não deverá prosseguir para a próxima janela até que a janela anterior seja concluída com êxito, crie uma autodependência. Um gatilho de autodependência que seja dependente do sucesso das execuções anteriores de si mesmo no período que abrange a hora anterior terá as propriedades indicadas no código a seguir.

> [!NOTE]
> Se o pipeline disparado depende da saída de pipelines em janelas disparadas anteriormente, recomendamos usar apenas a autodependência do gatilho periódico. Para limitar as execuções de gatilho paralelas, defina a simultaneidade máxima do gatilho.

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

Veja abaixo as ilustrações dos cenários e do uso de propriedades de dependência da janela em cascata.

### <a name="dependency-offset"></a>Deslocamento de dependência

![Exemplo de deslocamento](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "Exemplo de deslocamento")

### <a name="dependency-size"></a>Tamanho da dependência

![Exemplo de tamanho](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "Exemplo de tamanho")

### <a name="self-dependency"></a>Autodependência

![Autodependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "Autodependência")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependência de outro gatilho de janela em cascata

Um trabalho de processamento diário de telemetria que depende de outro trabalho diário agregando a saída dos últimos sete dias e que gera fluxos de janela sem interrupção de sete dias:

![Exemplo de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "Exemplo de dependência")

### <a name="dependency-on-itself"></a>Dependência de si próprio

Um trabalho diário sem lacunas nos fluxos de saída do trabalho:

![Exemplo de autodependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "Exemplo de autodependência")

## <a name="monitor-dependencies"></a>Monitorar dependências

É possível monitorar a cadeia de dependência e as janelas correspondentes na página de monitoramento da execução de gatilho. Navegue até  **Monitoramento > Execuções de Gatilho**. Se um gatilho periódico tiver dependências, o nome do gatilho terá um hiperlink para a exibição de monitoramento de dependência.  

![Monitorar execuções de gatilho](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "Monitorar execuções de gatilho – completo para exibição de dependência da janela em cascata")

Clique no nome do gatilho para ver as dependências dele. O painel à direita mostra informações detalhadas da execução de gatilho, como RunID, horário da janela, status etc.

![Monitorar a exibição da lista de dependências](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "Monitorar a exibição da lista de dependências")

Você pode ver o status das dependências e as janelas de cada gatilho dependente. Se um dos gatilhos de dependências falhar, você precisará executá-lo com êxito novamente para que o gatilho dependente seja executado.

Um gatilho periódico aguardará as dependências por _sete dias_ antes de atingir o tempo limite. Após sete dias, a execução de gatilho falhará.

Para obter uma forma mais visual para ver a agenda de dependência do gatilho, selecione a exibição de Gantt.

![Monitorar o gráfico de Gantt de dependências](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "Monitorar a exibição do gráfico de Gantt de dependências")

As caixas transparentes mostram as janelas de dependências para cada gatilho dependente downstream, enquanto as caixas de cores sólidas acima mostram as execuções de janela individuais. Estas são algumas dicas para interpretar a exibição do gráfico de Gantt:

* A caixa transparente é renderizada em azul quando as janelas dependentes estão no estado pendente ou de execução
* Depois que todas as janelas forem executadas com sucesso para um gatilho dependente, a caixa transparente ficará verde
* A caixa transparente é renderizada em vermelho quando uma janela dependente falha. Procure uma caixa vermelha sólida para identificar a execução da janela com falha

Para executar novamente uma janela na exibição de gráfico de Gantt, selecione a caixa de cor sólida da janela e um painel de ação será exibido com detalhes e opções de nova execução

## <a name="next-steps"></a>Próximas etapas

* Examine [Como criar um gatilho periódico](how-to-create-tumbling-window-trigger.md)
