---
title: Como usar a API de Localização de Anomalias com PHP - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Obtenha informações e exemplos de códigos para ajudar você a começar a usar a Localização de Anomalias com PHP em Serviços Cognitivos.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: f65f389d93a90df1bc16d2228586beead5f96a73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597754"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>Usar a API de Localização de Anomalias com PHP

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Este artigo fornece informações e exemplos de código para ajudar você a começar a usar a API de Localização de Anomalias com PHP para realizar tarefas de obtenção de resultado de anomalias para dados de série temporal.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>Obter pontos de anomalias com a API de Localização de Anomalias usando PHP
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemplo de dados de série temporal
A seguir está um exemplo de dados de série temporal.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>Exemplo de análise de dados e de obtenção de pontos de anomalias em PHP
1. Substitua o valor `[YOUR_SUBSCRIPTION_KEY]` pela sua chave de assinatura válida.
2. Substitua `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` pelo exemplo ou por seus próprios pontos de dados.
3. Execute e verifique a resposta.

```PHP
<?php
# This sample uses the Apache HTTP client from HTTP components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new HTTP_Request2('https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection');
$url = $request->getUrl();

$requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

$headers = array(
    # Request headers
    'Content-Type' => 'application/json',
    # NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '[YOUR_SUBSCRIPTION_KEY]',
);

$request->setHeader($headers);

$request->setMethod(HTTP_Request2::METHOD_POST);

# Request body
$request->setBody($requestData);

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}
?>
```

### <a name="example-response"></a>Exemplo de resposta

Uma resposta com êxito é retornada em JSON. A seguir está a resposta de exemplo.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
