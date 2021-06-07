---
title: Monitorar atividade de cópia
description: Saiba mais sobre como monitorar a execução da atividade de cópia em Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: jingwang
ms.openlocfilehash: 4aefeea33e61b6ee2541e929dbeb3fc36343cac5
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771372"
---
# <a name="monitor-copy-activity"></a>Monitorar atividade de cópia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como monitorar a execução da atividade de cópia em Azure Data Factory. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="monitor-visually"></a>Monitorar visualmente

Depois de criar e publicar um pipeline no Azure Data Factory, você pode associá-lo a um gatilho ou iniciar manualmente uma execução ad hoc. Você pode monitorar todas as execuções de pipeline nativamente na experiência de usuário do Azure Data Factory. Saiba mais sobre o monitoramento de Azure Data Factory em geral do [Visual monitor Azure data Factory](monitor-visually.md).

Para monitorar a execução da atividade de cópia, vá para a interface do usuário do monitor do data factory **autor &** . Na guia **Monitor** , você vê uma lista de execuções de pipeline, clica no link **nome do pipeline** para acessar a lista de execuções de atividade na execução do pipeline.

![Monitorar execução do pipeline](./media/copy-activity-overview/monitor-pipeline-run.png)

Nesse nível, você pode ver links para entrada de atividade de cópia, saída e erros (se a execução da atividade de cópia falhar), bem como estatísticas como duração/status. Clicar no botão **detalhes** (óculos) ao lado do nome da atividade de cópia fornecerá detalhes detalhados sobre a execução da atividade de cópia. 

![Monitorar execução da atividade de cópia](./media/copy-activity-overview/monitor-copy-activity-run.png)

Neste modo de exibição de monitoramento gráfico, Azure Data Factory apresenta as informações de execução da atividade de cópia, incluindo o volume de leitura/gravação de dados, o número de arquivos/linhas de dados copiados da origem para o coletor, a taxa de transferência, as configurações aplicadas ao seu cenário de cópia, as etapas que a atividade de cópia passa com as durações e os detalhes correspondentes e muito mais. Consulte [esta tabela](#monitor-programmatically) em cada métrica possível e sua descrição detalhada. 

Em alguns cenários, ao executar uma atividade de cópia no Data Factory, você verá **"dicas de ajuste de desempenho"**  na parte superior da exibição de monitoramento da atividade de cópia, conforme mostrado no exemplo. As dicas informam o afunilamento identificado pelo ADF para a execução de cópia específica, juntamente com a sugestão sobre o que mudar para impulsionar a produtividade da cópia. Saiba mais sobre as [dicas de ajuste de desempenho automático](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Os **detalhes e as durações da execução** inferior descrevem as principais etapas pelas quais sua atividade de cópia passa, o que é especialmente útil para solucionar problemas de desempenho de cópia. O afunilamento de sua execução de cópia é aquele com a duração mais longa. Consulte [solucionar problemas de desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md) em para o que cada estágio representa e as diretrizes de solução de problemas detalhadas.

**Exemplo: copiar do Amazon S3 para Azure Data Lake Storage Gen2**

![Monitorar detalhes da execução da atividade de cópia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitorar programaticamente

Os detalhes de execução da atividade de cópia e as características de desempenho também são retornados na seção saída de **resultado da execução da atividade de cópia**  >   , que é usada para renderizar a exibição de monitoramento da interface do usuário. A seguir está uma lista completa de propriedades que podem ser retornadas. Você verá apenas as propriedades que são aplicáveis ao seu cenário de cópia. Para obter informações sobre como monitorar as execuções de atividade programaticamente em geral, consulte [monitorar programaticamente um data Factory do Azure](monitor-programmatically.md).

| Nome da propriedade  | Descrição | Unidade na saída |
|:--- |:--- |:--- |
| dataRead | A quantidade real de dados lidos da origem. | Valor Int64, em bytes |
| dataWritten | A montagem real de dados gravados/confirmados no coletor. O tamanho pode ser diferente do `dataRead` tamanho, pois ele relaciona como cada repositório de dados armazena os dados. | Valor Int64, em bytes |
| filesRead | O número de arquivos lidos da fonte baseada em arquivo. | Valor Int64 (nenhuma unidade) |
| filesWritten | O número de arquivos gravados/confirmados no coletor baseado em arquivo. | Valor Int64 (nenhuma unidade) |
| filesSkipped | O número de arquivos ignorados da fonte baseada em arquivo. | Valor Int64 (nenhuma unidade) |
| dataConsistencyVerification | Detalhes da verificação de consistência de dados em que você pode ver se os dados copiados foram verificados como consistentes entre o repositório de origem e de destino. Saiba mais [neste artigo](copy-activity-data-consistency.md#monitoring). | Array |
| sourcePeakConnections | Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados de origem durante a execução da atividade de cópia. | Valor Int64 (nenhuma unidade) |
| sinkPeakConnections | Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados do coletor durante a execução da atividade de cópia. | Valor Int64 (nenhuma unidade) |
| rowsRead | Número de linhas lidas da origem. Essa métrica não se aplica ao copiar arquivos no estado em que se encontram sem analisá-los, por exemplo, quando os conjuntos de fontes de origem e de coletor são do tipo formato binário ou outro tipo de formato com configurações idênticas. | Valor Int64 (nenhuma unidade) |
| rowsCopied | Número de linhas copiadas para o coletor. Essa métrica não se aplica ao copiar arquivos no estado em que se encontram sem analisá-los, por exemplo, quando os conjuntos de fontes de origem e de coletor são do tipo formato binário ou outro tipo de formato com configurações idênticas.  | Valor Int64 (nenhuma unidade) |
| rowsSkipped | Número de linhas incompatíveis que foram ignoradas. Você pode habilitar linhas incompatíveis para serem ignoradas definindo `enableSkipIncompatibleRow` como true. | Valor Int64 (nenhuma unidade) |
| copyDuration | Duração da execução da cópia. | Valor Int32, em segundos |
| throughput | Taxa de transferência de dados, calculada pela `dataRead` divisão por `copyDuration` . | Número de ponto flutuante, em KBps |
| sourcePeakConnections | Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados de origem durante a execução da atividade de cópia. | Valor Int32 (nenhuma unidade) |
| sinkPeakConnections| Número máximo de conexões simultâneas estabelecidas com o armazenamento de dados do coletor durante a execução da atividade de cópia.| Valor Int32 (nenhuma unidade) |
| sqlDwPolyBase | Se o polybase é usado quando os dados são copiados para o Azure Synapse Analytics. | Boolean |
| redshiftUnload | Se o UNLOAD é usado quando os dados são copiados do redshift. | Boolean |
| hdfsDistcp | Se DistCp é usado quando os dados são copiados do HDFS. | Boolean |
| effectiveIntegrationRuntime | O tempo de execução de integração (IR) ou tempos de execução usados para ativar a atividade de execução, no formato `<IR name> (<region if it's Azure IR>)` . | Texto (cadeia de caracteres) |
| usedDataIntegrationUnits | As unidades de integração de dados efetivas durante a cópia. | Valor Int32 |
| usedParallelCopies | ParallelCopies efetivos durante a cópia. | Valor Int32 |
| logPath | Caminho para o log de sessão de dados ignorados no armazenamento de BLOBs. Consulte [tolerância a falhas](copy-activity-overview.md#fault-tolerance). | Texto (cadeia de caracteres) |
| executionDetails | Mais detalhes sobre os estágios pelos quais a atividade de cópia passa e as etapas correspondentes, durações, configurações e assim por diante. Não recomendamos que você analise esta seção porque ela pode ser alterada. Para entender melhor como ele ajuda a entender e solucionar problemas de desempenho de cópia, consulte a seção [monitorar visualmente](#monitor-visually) . | Array |
| perfRecommendation | Copiar dicas de ajuste de desempenho. Consulte [dicas de ajuste de desempenho](copy-activity-performance-troubleshooting.md#performance-tuning-tips) para obter detalhes. | Array |
| billingReference | O consumo de cobrança para a execução fornecida. Saiba mais em [monitorar o consumo no nível de execução da atividade](plan-manage-costs.md#monitor-consumption-at-activity-run-level). | Objeto |
| durationInQueue | Duração da fila em segundo antes da atividade de cópia começar a ser executada. | Objeto |

**Exemplo:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

\- [Visão geral da atividade de cópia](copy-activity-overview.md)

\- [Desempenho da atividade de cópia](copy-activity-performance.md)