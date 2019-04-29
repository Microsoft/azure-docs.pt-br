---
title: Como usar a API de Localização de Anomalias com Python - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Obtenha informações e exemplos de códigos para ajudar você a começar a usar a Localização de Anomalias com Python em Serviços Cognitivos.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: df894fee3328b2199b8f7436adabfd3db62f4004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217285"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Usar a API de Localização de Anomalias com Python

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Este artigo fornece informações e exemplos de código para ajudar você a começar a usar a API de Localização de Anomalias com Python para realizar tarefas de obtenção de resultado de anomalias para dados de série temporal.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Obter pontos de anomalias com a API de Localização de Anomalias usando Python 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemplo de dados de série temporal

A seguir está um exemplo de pontos de dados de série temporal.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Exemplo de análise de dados e de obtenção de pontos de anomalias em Python

Certifique-se de ter instalado o Python3, depois crie um arquivo executável do Python chamado detect.py. Em detect.py, você deve incluir o código a seguir. Antes de executar o código, lembre-se de substituir o valor `[YOUR_SUBSCRIPTION_KEY]` pela sua chave de assinatura válida.
Substitua `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` pelos seus pontos de dados.

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>Exemplo de resposta

Uma resposta com êxito é retornada em JSON. A seguir está a resposta de exemplo.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo Python](../tutorials/python-tutorial.md)
