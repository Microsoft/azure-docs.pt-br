---
title: Instalar contêineres de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de Helm de fala para texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002249"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Conversão de fala em texto (subgráfico: gráficos/speechToText)

Para substituir o gráfico "abrangência", adicione o prefixo `speechToText.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, ele substituirá o parâmetro correspondente, por exemplo, `speechToText.numberOfConcurrentRequest` substitui `numberOfConcurrentRequest` .

|Parâmetro|Descrição|Padrão|
| -- | -- | -- |
| `enabled` | Se o serviço de **fala a texto** está habilitado. | `false` |
| `numberOfConcurrentRequest` | O número de solicitações simultâneas para o serviço de **conversão de fala em texto** . Este gráfico calcula automaticamente os recursos de CPU e memória, com base nesse valor. | `2` |
| `optimizeForAudioFile`| Se o serviço precisa otimizar para entrada de áudio por meio de arquivos de áudio. Se `true` , esse gráfico alocará mais recursos de CPU ao serviço. | `false` |
| `image.registry`| O registro de imagem do Docker de **fala para texto** . | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagens do Docker de **fala para texto** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A marca de imagem do Docker de **fala para texto** . | `latest` |
| `image.pullSecrets` | Os segredos da imagem para obter a imagem do Docker de **fala para texto** . | |
| `image.pullByHash`| Se a imagem do Docker é retirada por hash. Se `true` , `image.hash` for necessário. | `false` |
| `image.hash`| O hash de imagem do Docker de **fala para texto** . Usado somente quando `image.pullByHash: true` .  | |
| `image.args.eula` (obrigatório) | Indica que você aceitou a licença. O único valor válido é `accept` | |
| `image.args.billing` (obrigatório) | O valor do URI do ponto de extremidade de cobrança está disponível na página Visão geral de fala do portal do Azure. | |
| `image.args.apikey` (obrigatório) | Usado para rastrear informações de cobrança. ||
| `service.type` | O tipo de serviço kubernetes do serviço de **fala a texto** . Consulte as [instruções de tipos de serviço kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verificar o suporte ao provedor de nuvem. | `LoadBalancer` |
| `service.port`|  A porta do serviço de **conversão de fala em texto** . | `80` |
| `service.annotations` | As anotações de **fala para texto** para os metadados de serviço. As anotações são pares chave-valor. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se a [escala de aumento horizontal do pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) é habilitada. Se `true` , o `speech-to-text-autoscaler` será implantado no cluster kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [orçamento de interrupção do pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está habilitado. Se `true` , o `speech-to-text-poddisruptionbudget` será implantado no cluster kubernetes. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Análise de sentimentos (subgráfico: gráficos/speechToText)

Começando com v 2.2.0 do contêiner de fala a texto e v 0.2.0 do gráfico de Helm, os parâmetros a seguir são usados para análise de sentimentos usando o API de Análise de Texto.

|Parâmetro|Descrição|Valores|Padrão|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Se o serviço **de análise de texto** está habilitado| true/false| `false`|
|`textanalytics.image.registry`| O registro de imagem do Docker da **análise de texto**| registro de imagem do Docker válido| |
|`textanalytics.image.repository`| O repositório de imagens do Docker da **análise de texto**| repositório de imagem do Docker válido| |
|`textanalytics.image.tag`| A marca de imagem do Docker da **análise de texto**| marca de imagem do Docker válida| |
|`textanalytics.image.pullSecrets`| Os segredos da imagem para extrair a imagem do Docker de **análise de texto**| nome de segredos válidos| |
|`textanalytics.image.pullByHash`| Especifica se você está extraindo a imagem do Docker por hash.  Se `yes` , `image.hash` também deve ser necessário. Se `no` , defina como ' false '. O padrão é `false`.| true/false| `false`|
|`textanalytics.image.hash`| O hash da imagem do Docker da **análise de texto** . Use-o apenas com `image.pullByHash:true` .| hash de imagem do Docker válido | |
|`textanalytics.image.args.eula`| Um dos argumentos necessários por contêiner de **análise de texto** , que indica que você aceitou a licença. O valor dessa opção deve ser: `accept` .| `accept`, se você quiser usar o contêiner | |
|`textanalytics.image.args.billing`| Um dos argumentos necessários por contêiner de **análise de texto** , que especifica o URI do ponto de extremidade de cobrança. O valor do URI do ponto de extremidade de cobrança está disponível na página Visão geral de fala do portal do Azure.|URI de ponto de extremidade de cobrança válido||
|`textanalytics.image.args.apikey`| Um dos argumentos necessários por contêiner **de análise de texto** , que é usado para rastrear informações de cobrança.| apiKey válido||
|`textanalytics.cpuRequest`| A CPU solicitada para o contêiner de **análise de texto**| INT| `3000m`|
|`textanalytics.cpuLimit`| A CPU limitada para o contêiner de **análise de texto**| | `8000m`|
|`textanalytics.memoryRequest`| A memória solicitada para o contêiner de **análise de texto**| | `3Gi`|
|`textanalytics.memoryLimit`| A memória limitada para o contêiner de **análise de texto**| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| O sufixo de URI da análise de sentimentos, o URI inteiro está no formato "http:// `<service>` : `<port>` / `<sentimentURISuffix>` ". | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| O tipo de serviço de **análise de texto** em kubernetes. Consulte [tipos de serviço kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) | tipo de serviço kubernetes válido | `LoadBalancer` |
|`textanalytics.service.port`| A porta do serviço de **análise de texto**| INT| `50085`|
|`textanalytics.service.annotations`| As anotações que os usuários podem adicionar aos metadados do serviço **de análise de texto** . Por exemplo:<br/> **anotações**<br/>`   `**some/annotation1: value1**<br/>`  `**some/annotation2: value2** | anotações, uma por cada linha| |
|`textanalytics.serivce.autoScaler.enabled`| Se a [autoescala de Pod horizontal](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está habilitada. Se habilitada, `text-analytics-autoscaler` será implantada no cluster kubernetes | true/false| `true`|
|`textanalytics.service.podDisruption.enabled`| Se o [orçamento de interrupção do pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está habilitado. Se habilitada, `text-analytics-poddisruptionbudget` será implantada no cluster kubernetes| true/false| `true`|
