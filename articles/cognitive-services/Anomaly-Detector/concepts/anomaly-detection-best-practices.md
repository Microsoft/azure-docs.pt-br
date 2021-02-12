---
title: Melhores práticas ao usar a API do Detector de Anomalias
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as práticas recomendadas ao detectar anomalias com a API do detector de anomalias.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: mbullwin
ms.openlocfilehash: 43ccde054a9630b251aa6c206028d29c7c699316
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936204"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Práticas recomendadas para usar a API do detector de anomalias

A API do detector de anomalias é um serviço de detecção de anomalias sem estado. A precisão e o desempenho de seus resultados podem ser afetados por:

* Como os dados de série temporal são preparados.
* Os parâmetros da API do detector de anomalias que foram usados.
* O número de pontos de dados em sua solicitação de API. 

Use este artigo para saber mais sobre as práticas recomendadas para usar a API para obter os melhores resultados para seus dados. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Quando usar o lote (inteiro) ou a detecção de anomalias de ponto mais recente (último)

O ponto de extremidade de detecção de lote da API do detector de anomalias permite detectar anomalias por meio de seus dados de série de horários inteiros. Nesse modo de detecção, um único modelo estatístico é criado e aplicado a cada ponto no conjunto de dados. Se sua série temporal tiver as características abaixo, é recomendável usar a detecção de lote para visualizar seus dados em uma chamada à API.

* Uma série temporal sazonal, com anomalias ocasionais.
* Uma série temporal de tendência simples, com picos ocasionais/DIPs. 

Não é recomendável usar a detecção de anomalias em lote para monitoramento de dados em tempo real ou usá-la em dados de série temporal que não têm as características acima. 

* A detecção de lote cria e aplica apenas um modelo, a detecção de cada ponto é feita no contexto da série inteira. Se as tendências de dados de série temporal estiverem para cima e para baixo sem sazonalidade, alguns pontos de alteração (DIPs e picos nos dados) poderão ser perdidos pelo modelo. Da mesma forma, alguns pontos de alteração que são menos significativos do que os mais tarde no conjunto de dados podem não ser contados como significativos o suficiente para serem incorporados ao modelo.

* A detecção de lote é mais lenta do que detectar o status de anomalia do ponto mais recente ao fazer o monitoramento de dados em tempo real, devido ao número de pontos que estão sendo analisados.

Para o monitoramento de dados em tempo real, é recomendável detectar o status de anomalias somente do ponto de dados mais recente. Ao aplicar continuamente a detecção de ponto mais recente, o monitoramento de dados de streaming pode ser feito com mais eficiência e precisão.

O exemplo a seguir descreve o impacto que esses modos de detecção podem ter sobre o desempenho. A primeira imagem mostra o resultado da detecção contínua do ponto mais recente do status de anomalia em 28 pontos de dados vistos anteriormente. Os pontos vermelhos são anomalias.

![Uma imagem mostrando a detecção de anomalias usando o ponto mais recente](../media/last.png)

Abaixo está o mesmo conjunto de dados usando a detecção de anomalias do lote. O modelo criado para a operação ignorou várias anomalias, marcadas por retângulos.

![Uma imagem mostrando a detecção de anomalias usando o método de lote](../media/entire.png)

## <a name="data-preparation"></a>Preparação de dados

A API do detector de anomalias aceita dados de série temporal formatados em um objeto de solicitação JSON. Uma série temporal pode ser qualquer dado numérico registrado ao longo do tempo em ordem sequencial. Você pode enviar o Windows dos dados de série temporal para o ponto de extremidade da API do detector de anomalias para melhorar o desempenho da API. O número mínimo de pontos de dados que você pode enviar é 12 e o máximo é de 8640 pontos. A [granularidade](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity) é definida como a taxa em que os dados são amostrados. 

Os pontos de dados enviados para a API do detector de anomalias devem ter um carimbo de data/hora UTC (tempo Universal Coordenado) válido e um valor numérico. 

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

Se os dados forem amostrados em um intervalo de tempo não padrão, você poderá especificá-los adicionando o `customInterval` atributo em sua solicitação. Por exemplo, se sua série for amostrada a cada 5 minutos, você poderá adicionar o seguinte à sua solicitação JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Pontos de dados ausentes

Os pontos de dados ausentes são comuns em conjuntos de dados de série temporal distribuídos uniformemente, especialmente aqueles com uma granularidade refinada (um pequeno intervalo de amostragem. Por exemplo, dados amostrados a cada poucos minutos). Faltam menos de 10% do número esperado de pontos em seus dados não devem ter um impacto negativo nos resultados da detecção. Considere preencher lacunas em seus dados com base em suas características, como substituir pontos de dados de um período anterior, interpolação linear ou uma média móvel.

### <a name="aggregate-distributed-data"></a>Agregar dados distribuídos

A API do detector de anomalias funciona melhor em uma série temporal distribuída uniformemente. Se os dados forem distribuídos aleatoriamente, você deverá agregar-os por uma unidade de tempo, como por minuto, por hora ou por dia.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Detecção de anomalias em dados com padrões sazonais

Se você sabe que os dados de série temporal têm um padrão sazonal (um que ocorre em intervalos regulares), você pode melhorar a precisão e o tempo de resposta da API. 

A especificação de um `period` quando você constrói sua solicitação JSON pode reduzir a latência de detecção de anomalias em até 50%. O `period` é um inteiro que especifica aproximadamente quantos pontos de dados a série temporal leva para repetir um padrão. Por exemplo, uma série temporal com um ponto de dados por dia teria um `period` as `7` , e uma série temporal com um ponto por hora (com o mesmo padrão semanal) teria um `period` de  `7*24` . Se você não tiver certeza dos padrões de seus dados, não precisará especificar esse parâmetro.

Para obter melhores resultados, forneça `period` um ponto de dados de quatro dias, mais um adicional. Por exemplo, dados por hora com um padrão semanal, conforme descrito acima, devem fornecer 673 pontos de dados no corpo da solicitação ( `7 * 24 * 4 + 1` ).

### <a name="sampling-data-for-real-time-monitoring"></a>Dados de amostragem para monitoramento em tempo real

Se os dados de streaming forem amostrados em um intervalo curto (por exemplo, segundos ou minutos), o envio do número recomendado de pontos de dados poderá exceder o número máximo permitido da API do detector de anomalias (8640 pontos de dados). Se os dados mostrarem um padrão sazonal estável, considere enviar um exemplo dos dados de série temporal em um intervalo de tempo maior, como horas. A amostragem dos dados dessa maneira também pode melhorar notavelmente o tempo de resposta da API. 

## <a name="next-steps"></a>Próximas etapas

* [O que é a API do Detector de Anomalias?](../overview.md)
* [Início Rápido: Detectar anomalias nos dados de série temporal usando o Detector de Anomalias](../quickstarts/client-libraries.md)
