---
title: Instalar contêineres de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico Helm de conversão de texto em fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002250"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Conversão de texto em fala (subgráfico: gráficos/textToSpeech)

Para substituir o gráfico "abrangência", adicione o prefixo `textToSpeech.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, ele substituirá o parâmetro correspondente, por exemplo, `textToSpeech.numberOfConcurrentRequest` substitui `numberOfConcurrentRequest` .

|Parâmetro|Descrição|Padrão|
| -- | -- | -- |
| `enabled` | Se o serviço de **conversão de texto em fala** está habilitado. | `false` |
| `numberOfConcurrentRequest` | O número de solicitações simultâneas para o serviço de **conversão de texto em fala** . Este gráfico calcula automaticamente os recursos de CPU e memória, com base nesse valor. | `2` |
| `optimizeForTurboMode`| Se o serviço precisa otimizar para entrada de texto por meio de arquivos de texto. Se `true` , esse gráfico alocará mais recursos de CPU ao serviço. | `false` |
| `image.registry`| O registro de imagem do Docker de **conversão de texto em fala** . | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagens do Docker de **texto para fala** . | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | A marca de imagem do Docker de **conversão de texto em fala** . | `latest` |
| `image.pullSecrets` | Os segredos da imagem para extrair a imagem do Docker de **conversão de texto em fala** . | |
| `image.pullByHash`| Se a imagem do Docker é retirada por hash. Se `true` , `image.hash` for necessário. | `false` |
| `image.hash`| O hash de imagem do Docker de **conversão de texto em fala** . Usado somente quando `image.pullByHash: true` .  | |
| `image.args.eula` (obrigatório) | Indica que você aceitou a licença. O único valor válido é `accept` | |
| `image.args.billing` (obrigatório) | O valor do URI do ponto de extremidade de cobrança está disponível na página Visão geral de fala do portal do Azure. | |
| `image.args.apikey` (obrigatório) | Usado para rastrear informações de cobrança. ||
| `service.type` | O tipo de serviço kubernetes do serviço de **conversão de texto em fala** . Consulte as [instruções de tipos de serviço kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verificar o suporte ao provedor de nuvem. | `LoadBalancer` |
| `service.port`|  A porta do serviço de **conversão de texto em fala** . | `80` |
| `service.annotations` | As anotações de **conversão de texto em fala** para os metadados de serviço. As anotações são pares chave-valor. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se a [escala de aumento horizontal do pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) é habilitada. Se `true` , o `text-to-speech-autoscaler` será implantado no cluster kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [orçamento de interrupção do pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está habilitado. Se `true` , o `text-to-speech-poddisruptionbudget` será implantado no cluster kubernetes. | `true` |