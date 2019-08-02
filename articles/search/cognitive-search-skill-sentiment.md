---
title: Habilidade de Sentimento da pesquisa cognitiva – Azure Search
description: Extraia uma pontuação de sentimento positivo/negativo do texto em um pipeline de enriquecimento do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f950bea4ea32ecc95b1721c6930903c3afaee848
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347664"
---
#   <a name="sentiment-cognitive-skill"></a>Habilidade cognitiva do Sentiment

A habilidade do **Sentiment** avalia o texto não estruturado ao longo de um conjunto negativo positivo e para cada registro, retorna uma pontuação numérica entre 0 e 1. Pontuações próximas de 1 indicam sentimento positivo e pontuações próximas de 0 indicam sentimento negativo. Essa habilidade usa os modelos de machine learning fornecidos pela [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) nos Serviços Cognitivos.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem como parte do estágio de decodificação de documentos no Azure Search. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço de extração de imagem é descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 5000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar interromper o backup de seus dados antes de enviá-lo ao analisador de sentimentos, use a [habilidade Text Split](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do Parâmetro |                      |
|----------------|----------------------|
| defaultLanguageCode | (opcional) O código de idioma a ser aplicado a documentos que não especifica explicitamente o idioma. <br/> Consulte [Lista completa dos idiomas com suporte](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Entradas de habilidades 

| Nome de entrada | Descrição |
|--------------------|-------------|
| texto | O texto a ser analisado.|
| languageCode  |  (opcional) Uma cadeia de caracteres que indica o idioma dos registros. Se este parâmetro não for especificado, o valor padrão é “en”. <br/>Consulte [Lista completa dos idiomas com suporte](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída | Descrição |
|--------------------|-------------|
| para seu app&#39;s | Um valor entre 0 e 1 que representa o sentimento do texto analisado. Valores próximos a 0 têm sentimento negativo, perto de 0,5, têm sentimento neutro, e valores próximo a 1 têm o sentimento positivo.|


##  <a name="sample-definition"></a>Definição de exemplo

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
        }
    ]
}
```

##  <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Saída de exemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Observações
Se estiver vazio, uma pontuação de sensibilidade não é retornada para os registros.

## <a name="error-cases"></a>Casos de erro
Se não há suporte para um idioma, será gerado um erro e nenhuma pontuação de sensibilidade é retornada.

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
