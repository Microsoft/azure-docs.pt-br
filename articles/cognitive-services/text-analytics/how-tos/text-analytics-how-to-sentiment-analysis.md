---
title: Análise de sentimento usando a API REST de Análise de Texto dos Serviços Cognitivos do Azure | Microsoft Docs
description: Saiba como detectar sentimento usando a API REST de Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: c3004dd3910dd5fdafc933efa213c9f097310e87
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001704"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Exemplo: Detectar o sentimento com a Análise de Texto

A [API de Análise de Sentimento do Azure](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) avalia a entrada de texto e retorna uma pontuação de sensibilidade para cada documento. As pontuações variam de 0 (negativo) a 1 (positivo).

Esse recurso é útil para detectar o sentimento positivo e negativo em fóruns de discussão, mídia social e revisões de cliente. O conteúdo é fornecido por você. Os modelos e dados de treinamento são fornecidos pelo serviço.

Atualmente, a API de Análise de Sentimento dá suporte a inglês, alemão, espanhol e francês. Os idiomas estão em versão prévia. Para obter mais informações, consulte [Linguagens com suporte](../text-analytics-supported-languages.md).

> [!TIP]
> A API de Análise de Texto do Azure também fornece uma imagem de contêiner do Docker baseada em Linux para análise de sentimento, para que você possa [instalar e executar o contêiner de análise de texto](text-analytics-how-to-install-containers.md) perto de seus dados.

## <a name="concepts"></a>Conceitos

Análise de texto usa um algoritmo de classificação de aprendizado de máquina para gerar uma pontuação de sensibilidade entre 0 e 1. As pontuações mais próximas de 1 indicam sentimento positivo, enquanto as classificações mais próximas de 0 indicam sentimento negativo.” O modelo é classificação com um amplo corpo de texto com associações de sensibilidade. Atualmente, não é possível fornecer seus próprios dados de treinamento. O modelo usa uma combinação de técnicas durante a análise de texto. As técnicas incluem processamento de texto, análise de parte da fala, posicionamento de palavras e associações de palavras. Para obter mais informações sobre o algoritmo, consulte [Apresentando Análise de Texto do Azure Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

A análise de sentimento é executada em todo o documento, em vez de extrair o sentimento para uma entidade específica no texto. Na prática, há uma tendência de precisão de pontuação para melhorar quando documentos contêm uma ou duas frases em vez de um grande bloco de texto. Durante a fase de avaliação objetividade, o modelo determina se um documento como um todo é objetivo ou contém sentimento. Um documento que é principalmente objetivo não faz progresso para a frase de detecção de sentimento, resultando em uma pontuação 0,50, sem nenhum processamento adicional. Para documentos que continuam no pipeline, a próxima fase gera uma pontuação acima ou abaixo de 0,50. A pontuação depende do grau de sentimento detectado no documento.

## <a name="preparation"></a>Preparação

A análise de sentimento produz um resultado de qualidade superior quando você fornece a ela partes menores de texto nas quais trabalhar. Este é o oposto da extração de frase-chave que executa melhor em grandes blocos de texto. Para obter os melhores resultados de ambas as operações, considere a reestruturação de entradas adequadamente.

Você deve ter documentos JSON neste formato: ID, texto e idioma.

O tamanho do documento deve ser inferior a 5.120 caracteres. Você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação. A amostra a seguir é um exemplo de conteúdo que você pode enviar para análise de sentimento:

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Etapa 1: Estruturar a solicitação

Para obter mais informações sobre a definição de solicitação, confira [Chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são redeclarados para conveniência:

+ Crie uma solicitação POST. Para examinar a documentação da API para esta solicitação, consulte [API de Análise de Sentimento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Defina o ponto de extremidade HTTP para a análise de sentimento, usando um recurso de Análise de Texto no Azure ou um [contêiner de Análise de Texto](text-analytics-how-to-install-containers.md) instanciado. Ele precisa incluir o recurso `/sentiment`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`.

+ Defina um cabeçalho de solicitação para incluir a chave de acesso para operações de Análises de Texto do Azure Machine Learning. Para obter mais informações, confira [Localizar pontos de extremidade e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

> [!Tip]
> Use o [Postman](text-analytics-how-to-call-api.md) ou abra o **console de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) para estruturar uma solicitação e postá-la no serviço.

## <a name="step-2-post-the-request"></a>Etapa 2: Postar a solicitação

A análise é executada após o recebimento da solicitação. Para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo, confira a seção [Limites de dados](../overview.md#data-limits) na visão geral.

Lembre-se de que o serviço é sem estado. Nenhum dado é armazenado em sua conta. Os resultados são retornados imediatamente na resposta.


## <a name="step-3-view-the-results"></a>Etapa 3: Exibir os resultados

O analisador de sentimento classifica texto como predominantemente positivo ou negativo. Ele atribui uma pontuação no intervalo de 0 a 1. Valores próximos 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma cadeia de caracteres não pode ser analisada para sentimento ou não tem nenhum sentimento, a pontuação é sempre exatamente 0,5. Por exemplo, se você passar uma cadeia de caracteres espanhol com um código de idioma inglês, a pontuação é 0,5.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local. Em seguida, importe a saída para um aplicativo que você possa usar para classificar, pesquisar e manipular os dados.

O exemplo a seguir mostra a resposta para o conjunto de documentos neste artigo:

```json
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="sentiment-analysis-v3-public-preview"></a>Análise de Sentimento v3 em versão prévia pública

A [próxima versão da Análise de Sentimento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) agora está disponível em versão prévia pública. Ele fornece melhorias significativas na precisão e nos detalhes da pontuação e categorização de texto da API. 

> [!NOTE]
> * O formato de solicitação e os [limites de dados](../overview.md#data-limits) da Análise de Sentimento v3 são os mesmos que os da versão anterior.
> * No momento, a Análise de Sentimento v3: 
>    * Dá suporte apenas para o idioma inglês.  
>    * Está disponível nas seguintes regiões: `Central US`, `Central Canada` e `East Asia`.

|Recurso |DESCRIÇÃO  |
|---------|---------|
|Precisão aprimorada     | Melhoria significativa na detecção de sentimento positivo, neutro, negativo e misto em documentos de texto com relação às versões anteriores.           |
|Pontuação de sentimento no nível da frase e do documento     | Detecte o sentimento de um documento e de suas frases individuais. Se o documento incluir várias frases, cada frase também receberá uma pontuação de sentimento.         |
|Categoria e pontuação de sentimento     | Agora, a API retorna categorias de sentimento para texto, além de uma pontuação de sentimento. As categorias são `positive`, `negative`, `neutral` e `mixed`.       |
| Resultados aprimorados | Agora, a Análise de Sentimento retorna informações sobre um documento de texto inteiro e suas frases individuais. |

### <a name="sentiment-labeling"></a>Rotulação de sentimento

A Análise de Sentimento v3 pode retornar pontuações e rótulos no nível do documento e da frase. As pontuações e os rótulos são `positive`, `negative` e `neutral`. No nível do documento, o rótulo (e não a pontuação) de sentimento `mixed` também pode ser retornado. O sentimento do documento é determinado agregando-se as pontuações das frases.

| Sentimento da frase                                                        | Rótulo do documento retornado |
|---------------------------------------------------------------------------|----------------|
| Pelo menos uma frase é positiva e o restante das frases são neutras. | `positive`     |
| Pelo menos uma frase é negativa e o restante das frases são neutras.  | `negative`     |
| Pelo menos uma frase é negativa e pelo menos uma frase é positiva.         | `mixed`        |
| Todas as sentenças são neutras.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Exemplo de solicitação da Análise de Sentimento v3

O JSON a seguir é um exemplo de solicitação feita para a nova versão da Análise de Sentimento. Observe que a formatação da solicitação é a mesma que a da versão anterior:

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Hello world. This is some input text that I love."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    }
  ]
}
```

### <a name="sentiment-analysis-v3-example-response"></a>Exemplo de resposta da Análise de Sentimento v3

Embora o formato da solicitação seja o mesmo que o da versão anterior, o formato da resposta foi alterado. O JSON a seguir é um exemplo de resposta da nova versão da API:

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

### <a name="example-c-code"></a>Código C# de exemplo

Você pode encontrar um aplicativo em C# de exemplo que chama esta versão da Análise de Sentimento no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e fluxo de trabalho para detecção de idioma usando a Análise de Texto nos Serviços Cognitivos do Azure. Em resumo:

+ [A API de Análise de Sentimento](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) está disponível para os idiomas selecionados.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
+ A solicitação POST é feita para um ponto de extremidade `/sentiment` usando uma [chave de acesso personalizada e um ponto de extremidade](text-analytics-how-to-access-key.md) que é válido para sua assinatura.
+ Saída de resposta, que pontuação de sentimento para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceita JSON. Os aplicativos de exemplo incluem Excel e Power BI, para citar alguns.

## <a name="see-also"></a>Consulte também 

 [Visão geral da Análise de Texto](../overview.md)  
 [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto de Análise de Texto do Azure Machine Learning](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Extraia frases-chave](text-analytics-how-to-keyword-extraction.md)
