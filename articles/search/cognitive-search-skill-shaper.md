---
title: Habilidade de Formatador da pesquisa cognitiva – Azure Search
description: Extrair metadados e informações estruturadas de dados não estruturados e formatá-los como um tipo complexo em um pipeline de enriquecimento do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c55783e9b209a1280a21edca34b75e72481f4cb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127035"
---
#   <a name="shaper-cognitive-skill"></a>Habilidades cognitivas do Shaper

O **Shaper** habilidade consolida várias entradas em um tipo complexo que pode ser referenciado posteriormente no pipeline enriquecimento. A habilidade **Formatador** permite basicamente que você crie uma estrutura, defina o nome dos membros dessa estrutura e atribua valores a cada membro. Campos consolidados útil em cenários de pesquisa com exemplos de combinação de um nome e sobrenome em uma única estrutura, cidade e estado em uma única estrutura, ou o nome e data de nascimento em uma única estrutura para estabelecer a identidade exclusiva.

Por padrão, essa técnica oferece suporte a objetos que estão um nível de profundidade. Para objetos mais complexos, encadeie várias etapas do **Formatador**.

Em resposta, o nome de saída é sempre "saída". Internamente, o pipeline pode mapear um nome diferente, como "analyzedText" nos exemplos abaixo para "saída", mas a habilidade **Formatador** em si retorna "saída" na resposta. Isso pode ser importante se você estiver depurando documentos enriquecidos e observar a discrepância de nomenclatura, ou se você criar uma habilidade personalizada e estruturação de resposta por conta própria.

> [!NOTE]
> Essa habilidade não está associada a uma API de Serviços Cognitivos e você não é cobrado por utilizá-la. No entanto, você ainda deverá [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para substituir a opção de recurso **Gratuito** que limita você a um pequeno número de enriquecimentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Amostra 1: tipos complexos

Considere um cenário onde você deseja criar uma estrutura chamada *analyzedText* que tem dois membros: *texto* e *sentimento*, respectivamente. No Azure Search, um campo de pesquisado de várias parte é chamado um *tipo complexo* e ainda não tem suporte fora da caixa. Nesta visualização, uma habilidade do **Formatador** pode ser usada para gerar campos de um tipo complexo no índice. 

O exemplo a seguir fornece os nomes do membro como a entrada. A estrutura de saída (campo complexo no Azure Search) é especificada por meio de *targetName*. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Entrada de exemplo
Um documento JSON que fornece uma entrada utilizável para essa habilidade **Formatador** pode ser:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="sample-output"></a>Saída de exemplo
A habilidade **Formatador** gera um novo elemento chamado *analyzedText* com os elementos combinados de *texto* e *sentimento*. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="sample-2-input-consolidation"></a>Exemplo 2: consolidação de entrada

Em outro exemplo, imagine que em diferentes estágios do processamento de pipeline, você extraiu o título de um livro e títulos de capítulo em diferentes páginas do livro. Agora você pode criar uma única estrutura composta por essas várias entradas.

A definição de habilidade Shaper para esse cenário pode parecer com o exemplo a seguir:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="sample-output"></a>Saída de exemplo
Nesse caso, o Shaper mescla todos os títulos de capítulo para criar uma matriz. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)

