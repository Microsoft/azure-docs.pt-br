---
title: Habilidade cognitiva do Text Split
titleSuffix: Azure Cognitive Search
description: Quebrar o texto em partes ou páginas de texto com base no comprimento em um pipeline de enriquecimento de ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 52aaeb01fef551eee350c6db662c2690ef7b3e78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84981941"
---
# <a name="text-split-cognitive-skill"></a>Habilidade cognitiva do Text Split

A habilidade **Text Split** quebra o texto em partes do texto. Você pode especificar se deseja quebrar o texto em sentenças ou em páginas de um tamanho específico. Essa habilidade é especialmente útil se houver requisitos de comprimento em outras habilidades downstream em texto. 

> [!NOTE]
> Essa habilidade não está associada a uma API de Serviços Cognitivos e você não é cobrado por utilizá-la. No entanto, você ainda deverá [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para substituir a opção de recurso **Gratuito** que limita você a um pequeno número de enriquecimentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `textSplitMode`    | "Páginas" ou "sentenças" | 
| `maximumPageLength` | Se o textSplitMode for definido como "páginas", isso se refere ao comprimento máximo da página, conforme medido pelo `String.Length`. O valor mínimo é 300.  Se o textSplitMode for definido como "pages", o algoritmo tentará dividir o texto em partes com um tamanho de, no máximo, "maximumPageLength". Nesse caso, o algoritmo fará o melhor para quebrar a frase em um limite de orações, de modo que o tamanho da parte possa ser um pouco menor que "maximumPageLength". | 
| `defaultLanguageCode` | (opcional) Um dos seguintes códigos de idioma: `da, de, en, es, fi, fr, it, ko, pt`. O padrão é inglês (en). Algumas coisas para levar em consideração:<ul><li>Se você passar um formato languagecode-countrycode, somente a parte languagecode do formato é usada.</li><li>Se o idioma não estiver na lista anterior, a habilidade de divisão quebra o texto em limites de caractere.</li><li>Fornecer um código de idioma é útil para evitar cortar uma palavra na metade para idiomas que não sejam espaços em branco, como chinês, japonês e coreano.</li><li>Se você não souber o idioma (ou seja, precisar dividir o texto para entrada no [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), o padrão de Inglês (EN) deve ser suficiente. </li></ul>  |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome do parâmetro       | Descrição      |
|----------------------|------------------|
| `text`    | O texto a ser dividido em subcadeias. |
| `languageCode`    | (opcional) Código de idioma para o documento. Se você não souber o idioma (ou seja, precisar dividir o texto para entrada no [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), será seguro remover essa entrada.  |

## <a name="skill-outputs"></a>Saídas de habilidades 

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `textItems`   | Uma matriz de subcadeias de caracteres que foram extraídos. |


##  <a name="sample-definition"></a>Definição de exemplo

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
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
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Casos de erro
Se não há suporte para um idioma, um aviso será gerado e o texto é dividido em limites de caractere.

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
