---
title: Como usar a API de Localização de Anomalias com Ruby - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Obtenha informações e exemplos de códigos para ajudar você a começar a usar Ruby e a API de Localização de Anomalias em Serviços Cognitivos.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 020c957baf6673365d64c613bd908a440bc3d05c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729198"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Usar a API de Localização de Anomalias com Ruby

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Este artigo fornece informações e exemplos de código para ajudar você a começar a usar a API de Localização de Anomalias com Ruby para realizar tarefas de obtenção de resultado de detecção de anomalias em dados de série temporal.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Obter pontos de anomalias com a API de Localização de Anomalias usando Ruby 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemplo de dados de série temporal
A seguir está um exemplo de pontos de dados de série temporal,

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Exemplo de análise de dados e de obtenção de pontos de anomalias em Ruby

As etapas de como usar o exemplo estão a seguir.

1. Instalar [rest-client](https://github.com/rest-client/rest-client) executando “gem install rest-client”.
2. Salve o código abaixo como um arquivo .rb.
3. Substitua o valor `[YOUR_SUBSCRIPTION_KEY]` pela sua chave de assinatura válida.
4. Substitua `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` pelo exemplo ou por seus próprios pontos de dados.
5. Execute e verifique a resposta.

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>Exemplo de resposta

Uma resposta com êxito é retornada em JSON. A seguir está a resposta de exemplo.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
