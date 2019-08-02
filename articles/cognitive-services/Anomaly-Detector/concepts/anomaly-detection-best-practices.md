---
title: Melhores práticas ao usar a API do Detector de Anomalias
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as práticas recomendadas ao detectar anomalias com a API do Detector de anomalias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721628"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Práticas recomendadas para usar a API do Detector de anomalias

A API do Detector de anomalias é um serviço de detecção de anomalias sem monitoração de estado. A precisão e o desempenho de seus resultados podem ser afetados por:

* Como seus dados de série temporal são preparados.
* Os parâmetros de API do Detector de anomalias que foram usados.
* O número de pontos de dados em sua solicitação de API. 

Use este artigo para saber mais sobre práticas recomendadas para usar a API de obtenção dos melhores resultados para seus dados. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Quando usar o lote (inteiro) ou a versão mais recente (última) do ponto de detecção de anomalias

Ponto de extremidade de detecção de lote da API de Detector de anomalias permite detectar anomalias por meio de toda vezes os dados de série. Nesse modo de detecção, um único modelo de estatístico é criado e aplicado a cada ponto no conjunto de dados. Se sua série temporal tem as características, abaixo, é recomendável usar a detecção de lote para visualizar seus dados em uma chamada de API.

* Uma série temporal sazonal, com anomalias ocasionais.
* Uma série de tempo de tendência plana, com picos ocasionais/dips. 

Não recomendamos o uso de detecção de anomalias de lote para monitoramento, ou usá-lo em dados de série temporal que não tem acima características de dados em tempo real. 

* Detecção de lote cria e aplica-se apenas um modelo, a detecção de cada ponto é feita no contexto de série inteira. Se as tendências de dados de série de tempo e reduza verticalmente sem sazonalidade, alguns pontos de alterar (dips e picos de dados) podem ser ignoradas pelo modelo. Da mesma forma, alguns pontos que são menos significativos do que aquelas mais tarde no conjunto de dados de alteração não podem ser contados como significativa o suficiente para ser incorporado ao modelo.

* Detecção de lote é mais lenta do que detectar o status de anomalias do último ponto ao fazer o monitoramento de dados em tempo real, devido ao número de pontos que estão sendo analisados.

Para monitorar dados em tempo real, é recomendável detectar o status de anomalias de seu último ponto de dados somente. Aplicando continuamente detecção do ponto mais recente, o monitoramento de dados de streaming pode ser feito com mais eficiência e precisão.

O exemplo a seguir descreve o impacto que desses modos de detecção podem ter no desempenho. A primeira figura mostra o resultado da detecção continuamente o ponto mais recente do status de anomalias ao longo de pontos de dados Vista anteriormente 28. Os pontos vermelhos são anomalias.

![Uma imagem que mostra a detecção de anomalias usando o ponto mais recente](../media/last.png)

Abaixo está o mesmo conjunto de dados usando a detecção de anomalias de lote. O modelo criado para a operação ignorou várias anomalias, marcadas por retângulos.

![Uma imagem que mostra a detecção de anomalias usando o método de lote](../media/entire.png)

## <a name="data-preparation"></a>Preparação dos dados

A API do Detector de anomalias aceita a série temporal dados formatados em um objeto de solicitação JSON. Uma série de tempo pode ser qualquer dados numéricos registrados ao longo do tempo, em ordem sequencial. Você pode enviar o windows de seus dados de série temporal para o ponto de extremidade de API do Detector de anomalias para melhorar o desempenho da API. O número mínimo de pontos de dados, que você pode enviar é 12, e o máximo é 8640 pontos. [Granularidade](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) é definida como a taxa de amostragem em seus dados. 

Pontos de dados enviados para a API do Detector de anomalias devem ter um carimbo de hora válidos Tempo Universal Coordenado (UTC) e valor numérico. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Se seus dados são amostrados a um intervalo de tempo não padrão, você pode especificá-lo adicionando o `customInterval` atributo em sua solicitação. Por exemplo, se a série é amostrada a cada 5 minutos, você pode adicionar o seguinte à sua solicitação JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Pontos de dados ausentes

Pontos de dados ausentes são comuns em conjuntos de dados de série tempo uniformemente distribuído, especialmente aqueles com uma granularidade fina (um intervalo de amostragem pequeno. Por exemplo, os dados amostrados alguns minutos). Tem menos de 10% do número esperado de pontos em seus dados não deve ter um impacto negativo em seus resultados de detecção. Considere preenchendo as lacunas em seus dados com base em suas características, como substituição de pontos de dados de um período anterior, uma interpolação linear ou uma média móvel.

### <a name="aggregate-distributed-data"></a>Agregação de dados distribuídos

A API do Detector de anomalias funciona melhor em uma série de tempo uniformemente distribuído. Se seus dados são distribuídos aleatoriamente, você deve agregá-la por uma unidade de tempo, como por minuto, hora ou diariamente por exemplo.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Detecção de anomalias em dados com padrões sazonais

Se você souber que seus dados de série temporal tem um padrão sazonal (aquela que ocorre em intervalos regulares), você pode melhorar a precisão e tempo de resposta de API. 

Especificando um `period` ao construir sua solicitação JSON pode reduzir a latência de detecção de anomalias em até 50%. O `period` é um inteiro que especifica a série temporal de pontos de dados aproximadamente quantas necessárias para repetir um padrão. Por exemplo, uma série de tempo com um ponto de dados por dia seria ter uma `period` como `7`, e uma série de tempo com um ponto por hora (com o mesmo padrão semanal) teria um `period` de `7*24`. Se você não tiver certeza dos padrões dos seus dados, você não precisa especificar esse parâmetro.

Para obter melhores resultados, forneça 4 `period`da partir do ponto de dados, mais um adicional. Por exemplo, os dados por hora com um padrão semanal conforme descrito acima devem fornecer 673 pontos de dados no corpo da solicitação (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Dados de amostragem para monitoramento em tempo real

Se os dados de streaming são amostrados em um curto intervalo (por exemplo, segundos ou minutos), enviar o número recomendado de pontos de dados pode exceder máximo número permitido (8640 pontos de dados) a API Detector de anomalias. Se seus dados mostram um padrão sazonal estável, considere o envio de uma amostra de seus dados de série temporal em um intervalo de tempo maior, como horas. Amostragem dos dados dessa forma, também visivelmente pode melhorar o tempo de resposta de API. 

## <a name="next-steps"></a>Próximas etapas

* [O que é a API do Detector de anomalias?](../overview.md)
* [Início Rápido: Detectar anomalias em seus dados de série temporal usando a API de REST do Detector de anomalias](../quickstarts/detect-data-anomalies-csharp.md)
