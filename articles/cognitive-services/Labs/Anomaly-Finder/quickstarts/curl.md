---
title: Como usar a API do Localizador de Anomalias com cURL - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Obtenha informações para ajudá-lo a começar a usar cURL e API do Localizador de Anomalias em Serviços Cognitivos.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 7a4996c83d57b34fcfcff43650b21359acb4e65e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094809"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Usar a API do Localizador de Anomalias com cURL

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a usar a API do Localizador de Anomalias com cURL para realizar a tarefa de obter resultados anormais de dados de série temporais.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Obter pontos de anomalias com a API do Localizador de Anomalias usando cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemplo de dados de série temporal

A seguir está um exemplo de pontos de dados de série temporal.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Exemplo de análise de dados e de obtenção de pontos de anomalias em cURL

As etapas de como usar o exemplo estão a seguir.

1. Substitua o valor `[YOUR_SUBSCRIPTION_KEY]` pela sua chave de assinatura válida.
2. Substitua `[YOUR_REGION]` para usar a localização em que você obteve suas chaves de assinatura.
3. Substitua `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` pelo exemplo ou por seus próprios pontos de dados.
4. Execute e verifique a resposta.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Exemplo de resposta
Uma resposta com êxito é retornada em JSON. A seguir está a resposta de exemplo: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
