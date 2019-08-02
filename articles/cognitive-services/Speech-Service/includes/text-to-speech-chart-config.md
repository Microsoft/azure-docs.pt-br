---
title: Instalar contêineres de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico Helm de conversão de texto em fala.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717222"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Conversão de texto em fala (subgráfico: gráficos/textToSpeech)

Para substituir o gráfico "abrangência", adicione o prefixo `textToSpeech.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, ele substituirá o parâmetro correspondente, por `textToSpeech.numberOfConcurrentRequest` exemplo `numberOfConcurrentRequest`, substitui.

|Parâmetro|Descrição|Padrão|
| -- | -- | -- |
| `enabled` | Se o serviço de **conversão de texto em fala** está habilitado. | `false` |
| `numberOfConcurrentRequest` | O número de solicitações simultâneas para o serviço de **conversão de texto em fala** . Este gráfico calcula automaticamente os recursos de CPU e memória, com base nesse valor. | `2` |
| `optimizeForTurboMode`| Se o serviço precisa otimizar para entrada de texto por meio de arquivos de texto. Se `true`, esse gráfico alocará mais recursos de CPU ao serviço. | `false` |
| `image.registry`| O registro de imagem do Docker de **conversão de texto em fala** . | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagens do Docker de **texto para fala** . | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | A marca de imagem do Docker de **conversão de texto em fala** . | `latest` |
| `image.pullSecrets` | Os segredos da imagem para extrair a imagem do Docker de **conversão de texto em fala** . | |
| `image.pullByHash`| Se a imagem do Docker é retirada por hash. Se `true` ,`image.hash` for necessário. | `false` |
| `image.hash`| O hash de imagem do Docker de **conversão de texto em fala** . Usado somente quando `image.pullByHash: true`.  | |
| `image.args.eula`necessária | Indica que você aceitou a licença. O único valor válido é`accept` | |
| `image.args.billing`necessária | O valor do URI do ponto de extremidade de cobrança está disponível na página Visão geral de fala do portal do Azure. | |
| `image.args.apikey`necessária | Usado para rastrear informações de cobrança. ||
| `service.type` | O tipo de serviço kubernetes do serviço de **conversão de texto em fala** . Consulte as [instruções de tipos de serviço kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verificar o suporte ao provedor de nuvem. | `LoadBalancer` |
| `service.port`|  A porta do serviço de **conversão de texto em fala** . | `80` |
| `service.autoScaler.enabled` | Se a [escala de aumento horizontal do pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) é habilitada. Se `true`, o `text-to-speech-autoscaler` será implantado no cluster kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [orçamento de interrupção do pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está habilitado. Se `true`, o `text-to-speech-poddisruptionbudget` será implantado no cluster kubernetes. | `true` |